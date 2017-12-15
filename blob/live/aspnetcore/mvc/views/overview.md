---
title: "ASP.NET Core MVC görünümlerde"
author: ardalis
description: "Görünümler, uygulamanın veri sunumu ve ASP.NET Core MVC kullanıcı etkileşimini nasıl işleneceğini öğrenin."
keywords: "ASP.NET Core, görüntülemek, MVC, razor, viewmodel, viewdata, Görünüm Paketi"
ms.author: riande
manager: wpickett
ms.date: 12/12/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/overview
ms.openlocfilehash: 2562d4e5fb85159e6ccb47990f54448ddc188077
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/14/2017
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="91401-104">ASP.NET Core MVC görünümlerde</span><span class="sxs-lookup"><span data-stu-id="91401-104">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="91401-105">Tarafından [Steve Smith](https://ardalis.com/) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="91401-105">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="91401-106">Bu belgede ASP.NET Core MVC uygulamalarında kullanılan görünümler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="91401-106">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="91401-107">Razor sayfalarında daha fazla bilgi için bkz: [Razor sayfalarının giriş](xref:mvc/razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="91401-107">For information on Razor Pages, see [Introduction to Razor Pages](xref:mvc/razor-pages/index).</span></span>

<span data-ttu-id="91401-108">İçinde **M**odel -**V**görünümü -**C**ontroller (MVC) deseni *Görünüm* uygulamanın veri sunu ve kullanıcı etkileşimini işler.</span><span class="sxs-lookup"><span data-stu-id="91401-108">In the **M**odel-**V**iew-**C**ontroller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="91401-109">Bir HTML şablonu görünümdür ile katıştırılmış [Razor biçimlendirme](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="91401-109">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="91401-110">Razor biçimlendirme istemciye gönderilen bir Web sayfası oluşturmak için HTML biçimlendirmesi etkileşimde kodudur.</span><span class="sxs-lookup"><span data-stu-id="91401-110">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="91401-111">ASP.NET Core MVC'de görünümlerdir *.cshtml* dosyaları kullanan [C# programlama dili](/dotnet/csharp/) Razor biçimlendirme.</span><span class="sxs-lookup"><span data-stu-id="91401-111">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="91401-112">Genellikle, her uygulamanın adlı klasörlere dosyaları görüntüle gruplandırılmış [denetleyicileri](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="91401-112">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="91401-113">Klasörleri depolanmış bir *görünümleri* uygulama kökünde klasör:</span><span class="sxs-lookup"><span data-stu-id="91401-113">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Visual Studio Çözüm Gezgini görünümler klasöründe giriş klasörü About.cshtml, Contact.cshtml ve Index.cshtml dosyaları göstermek için açık açık](overview/_static/views_solution_explorer.png)

<span data-ttu-id="91401-115">*Giriş* denetleyicisi ile temsil edilir bir *giriş* klasöre *görünümleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="91401-115">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="91401-116">*Giriş* görünümleri içeren klasör *hakkında*, *kişi*, ve *dizin* (giriş sayfası) Web sayfaları.</span><span class="sxs-lookup"><span data-stu-id="91401-116">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="91401-117">Bir kullanıcı isteğinde bulunduğunda bu üç Web sayfaları, denetleyici eylemleri birini *giriş* denetleyicisi belirlemek, üç görünüm oluşturmak ve bir Web sayfası kullanıcıya döndürmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="91401-117">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="91401-118">Kullanım [düzenleri](xref:mvc/views/layout) tutarlı Web sayfası bölümler sağlar ve kod yineleme azaltmak için.</span><span class="sxs-lookup"><span data-stu-id="91401-118">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="91401-119">Düzenleri genellikle üstbilgi, gezinme ve menü öğeleri ve alt bilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="91401-119">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="91401-120">Üstbilgi ve altbilgi genellikle Demirbaş biçimlendirme çok sayıda meta veri öğeleri ve komut dosyası ve stil varlıklar bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="91401-120">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="91401-121">Düzenleri görünümlerinizi bu Demirbaş biçimlendirme önlemenize yardımcı.</span><span class="sxs-lookup"><span data-stu-id="91401-121">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="91401-122">[Kısmi görünümler](xref:mvc/views/partial) görünümleri yeniden kullanılabilir bölümlerini yöneterek kod yinelemesinden azaltın.</span><span class="sxs-lookup"><span data-stu-id="91401-122">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="91401-123">Örneğin, kısmi görünüm, birkaç görünümlerde bir blog Web sitesinde Yazar biyografisi yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="91401-123">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="91401-124">Yazar biyografisi sıradan görünüm içeriğin ve Web sayfasının içeriği üretmek için yürütmek için kodu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="91401-124">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="91401-125">Bu içerik türü için kısmi görünüm kullanılması idealdir şekilde yazar biyografisi içerik görünüm tek başına, model bağlama tarafından kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="91401-125">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="91401-126">[Görüntüleme bileşenleri](xref:mvc/views/view-components) yineleyen kod azaltmanızı izin vermesi benzeyen kısmi görünümler, ancak bu Web sayfası oluşturmak için sunucuda çalıştırmak için kod gerektiren görünümü içerik için uygun.</span><span class="sxs-lookup"><span data-stu-id="91401-126">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="91401-127">İşlenmiş içeriği gerektirdiğinde alışveriş sepeti bir Web sitesi için veritabanı etkileşimini gibi görünüm bileşenleri yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="91401-127">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="91401-128">Görünüm bileşenleri Web sayfası bir çıktı oluşturmak için bağlama modellemek için sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="91401-128">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="91401-129">Görünümleri kullanmanın yararları</span><span class="sxs-lookup"><span data-stu-id="91401-129">Benefits of using views</span></span>

<span data-ttu-id="91401-130">Görünümleri Yardım kurmak için bir [ **S**eparation **o**f **C**oncerns (SoC) tasarım](http://deviq.com/separation-of-concerns/) kullanıcı arabirimi biçimlendirmeden ayıran bir MVC uygulaması içindeki uygulamanın diğer bölümleri.</span><span class="sxs-lookup"><span data-stu-id="91401-130">Views help to establish a [**S**eparation **o**f **C**oncerns (SoC) design](http://deviq.com/separation-of-concerns/) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="91401-131">SoC tasarım aşağıdaki uygulamanızı birkaç avantaj sağlayan modüler yapar:</span><span class="sxs-lookup"><span data-stu-id="91401-131">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="91401-132">Uygulama, daha iyi organize çünkü korumak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="91401-132">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="91401-133">Görünümleri genellikle uygulama özelliği tarafından gruplandırılır.</span><span class="sxs-lookup"><span data-stu-id="91401-133">Views are generally grouped by app feature.</span></span> <span data-ttu-id="91401-134">Bu, bir özellik çalışırken ilgili görünümleri bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="91401-134">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="91401-135">Uygulama bölümlerini gevşek.</span><span class="sxs-lookup"><span data-stu-id="91401-135">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="91401-136">Derleme ve uygulamanın görünümleri ayrı ayrı iş mantığı ve verileri erişim bileşenleri güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="91401-136">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="91401-137">Uygulamanın diğer bölümleri güncelleştirmek mutlaka zorunda kalmadan uygulama görünümlerini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="91401-137">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="91401-138">Uygulama kullanıcı arabirimi bölümlerini görünümleri ayrı birimi olduğundan test daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="91401-138">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="91401-139">Daha iyi düzenleme nedeniyle kullanıcı arabiriminin yanlışlıkla yineleme bölümleri gerekir daha az olasıdır.</span><span class="sxs-lookup"><span data-stu-id="91401-139">Due to better organization, it's less likely that you'll accidently repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="91401-140">Bir görünümü oluşturma</span><span class="sxs-lookup"><span data-stu-id="91401-140">Creating a view</span></span>

<span data-ttu-id="91401-141">Bir denetleyiciye özgü görünümler oluşturulan *görünümler / [controllername öğesi]* klasör.</span><span class="sxs-lookup"><span data-stu-id="91401-141">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="91401-142">Denetleyicileri arasında paylaşılan görünümleri yerleştirilir *görünümler/paylaşılan* klasör.</span><span class="sxs-lookup"><span data-stu-id="91401-142">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="91401-143">Bir görünüm oluşturmak için yeni bir dosya ekleyin ve onun ilişkili denetleyici eylemi ile aynı adı vermek *.cshtml* dosya uzantısı.</span><span class="sxs-lookup"><span data-stu-id="91401-143">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="91401-144">Karşılık gelen bir görünüm oluşturmak için *hakkında* eylemde *giriş* denetleyicisi oluşturma bir *About.cshtml* dosyasını *görünümler/giriş*klasörü:</span><span class="sxs-lookup"><span data-stu-id="91401-144">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[Main](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="91401-145">*Razor* biçimlendirme ile başlayan `@` simgesi.</span><span class="sxs-lookup"><span data-stu-id="91401-145">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="91401-146">C# yerleştirerek çalıştırma C# deyimleri kod içinde [Razor kod blokları](xref:mvc/views/razor#razor-code-blocks) küme ayraçları ayarlayın (`{ ... }`).</span><span class="sxs-lookup"><span data-stu-id="91401-146">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="91401-147">Örneğin, "Hakkında" atanması için bkz: `ViewData["Title"]` yukarıda gösterilen.</span><span class="sxs-lookup"><span data-stu-id="91401-147">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="91401-148">HTML içindeki değerleri değeriyle başvurarak görüntüleyebilirsiniz `@` simgesi.</span><span class="sxs-lookup"><span data-stu-id="91401-148">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="91401-149">İçeriğini görmek `<h2>` ve `<h3>` yukarıdaki öğeler.</span><span class="sxs-lookup"><span data-stu-id="91401-149">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="91401-150">Yukarıda gösterilen görünüm içeriği kullanıcı için işlenen tüm Web sayfası yalnızca bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="91401-150">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="91401-151">Sayfanın düzenini geri kalanı ve diğer genel görünümü yönlerini diğer görünüm dosyalarında belirtilmiş.</span><span class="sxs-lookup"><span data-stu-id="91401-151">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="91401-152">Daha fazla bilgi için bkz: [düzeni konu](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="91401-152">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="91401-153">Denetleyicileri görünümleri nasıl belirtin</span><span class="sxs-lookup"><span data-stu-id="91401-153">How controllers specify views</span></span>

<span data-ttu-id="91401-154">Görünümleri eylemlerine genellikle döndürülür bir [ViewResult](/aspnet/core/api/microsoft.aspnetcore.mvc.viewresult), bir tür olduğu [ActionResult](/aspnet/core/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="91401-154">Views are typically returned from actions as a [ViewResult](/aspnet/core/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/aspnet/core/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="91401-155">Eylem yöntemi oluşturun ve dönüş bir `ViewResult` doğrudan, ancak değil, yaygın olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="91401-155">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="91401-156">Çoğu denetleyicileri devralınmalıdır beri [denetleyicisi](/aspnet/core/api/microsoft.aspnetcore.mvc.controller), yalnızca kullandığınız `View` dönmek için yardımcı yöntem `ViewResult`:</span><span class="sxs-lookup"><span data-stu-id="91401-156">Since most controllers inherit from [Controller](/aspnet/core/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="91401-157">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="91401-157">*HomeController.cs*</span></span>

[!code-csharp[Main](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="91401-158">Bu eylem geri döndüğünde, *About.cshtml* son bölümünde gösterilen görünüm aşağıdaki Web sayfası işlenen:</span><span class="sxs-lookup"><span data-stu-id="91401-158">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Edge tarayıcısında çizilir sayfası hakkında](overview/_static/about-page.png)

<span data-ttu-id="91401-160">`View` Yardımcı yöntem birçok aşırı yüklemeye sahip.</span><span class="sxs-lookup"><span data-stu-id="91401-160">The `View` helper method has several overloads.</span></span> <span data-ttu-id="91401-161">İsteğe bağlı olarak belirtebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="91401-161">You can optionally specify:</span></span>

* <span data-ttu-id="91401-162">Döndürülecek açık bir görünümü:</span><span class="sxs-lookup"><span data-stu-id="91401-162">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```
* <span data-ttu-id="91401-163">A [modeli](xref:mvc/models/model-binding) geçirilecek görünümü:</span><span class="sxs-lookup"><span data-stu-id="91401-163">A [model](xref:mvc/models/model-binding) to pass to the the view:</span></span>

  ```csharp
  return View(Orders);
  ```
* <span data-ttu-id="91401-164">Bir görünüm ve model için:</span><span class="sxs-lookup"><span data-stu-id="91401-164">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="91401-165">Görünüm bulma</span><span class="sxs-lookup"><span data-stu-id="91401-165">View discovery</span></span>

<span data-ttu-id="91401-166">Bir eylem bir görünüm döndürdüğünde adlı bir işlem *görünüm bulma* gerçekleşmesi.</span><span class="sxs-lookup"><span data-stu-id="91401-166">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="91401-167">Bu işlem, hangi görünüm dosyası görünüm adını temel alarak kullanıldığını belirler.</span><span class="sxs-lookup"><span data-stu-id="91401-167">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="91401-168">Varsayılan davranışını `View` yöntemi (`return View();`) içinden çağırıldığında eylem yöntemi aynı ada sahip bir görünüm getirmektir.</span><span class="sxs-lookup"><span data-stu-id="91401-168">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="91401-169">Örneğin, *hakkında* `ActionResult` denetleyicinin yöntemi adı adlı bir görünüm dosyayı aramak için kullanılan *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="91401-169">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="91401-170">İlk olarak, çalışma zamanı görünür *görünümler / [controllername öğesi]* görünümü için klasör.</span><span class="sxs-lookup"><span data-stu-id="91401-170">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="91401-171">Eşleşen bir görünüm vardır bulamazsa arama *paylaşılan* görünümü için klasör.</span><span class="sxs-lookup"><span data-stu-id="91401-171">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="91401-172">Örtük olarak döndürürse önemli değildir `ViewResult` ile `return View();` veya Görünüm adı açıkça geçirmek `View` yöntemiyle `return View("<ViewName>");`.</span><span class="sxs-lookup"><span data-stu-id="91401-172">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="91401-173">Her iki durumda da, bu sırada eşleşen bir görünüm dosyası görünüm bulma arar:</span><span class="sxs-lookup"><span data-stu-id="91401-173">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="91401-174">*Görünümler /\[controllername öğesi]\[Görünümadı] .cshtml*</span><span class="sxs-lookup"><span data-stu-id="91401-174">*Views/\[ControllerName]\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="91401-175">*Görünümler/paylaşılan/\[Görünümadı] .cshtml*</span><span class="sxs-lookup"><span data-stu-id="91401-175">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="91401-176">Bir görünüm adı yerine bir görünüm dosya yolu sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="91401-176">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="91401-177">Uygulama kök dizininde başlayan mutlak bir yol kullanıyorsanız (isteğe bağlı olarak başlayarak "/" veya "~ /"), *.cshtml* uzantısı belirtilmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="91401-177">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="91401-178">Görünümler farklı dizinlerde belirtmek için göreli bir yol kullanabilirsiniz *.cshtml* uzantısı.</span><span class="sxs-lookup"><span data-stu-id="91401-178">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="91401-179">İçinde `HomeController`, geri dönebilirsiniz *dizin* görünümünü, *Yönet* göreli bir yol görünümlerle:</span><span class="sxs-lookup"><span data-stu-id="91401-179">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="91401-180">Benzer şekilde, geçerli denetleyici özgü dizinle belirtebilir ". /" öneki:</span><span class="sxs-lookup"><span data-stu-id="91401-180">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="91401-181">[Kısmi görünümler](xref:mvc/views/partial) ve [görüntülemek bileşenleri](xref:mvc/views/view-components) benzer (ancak aynı) bulma mekanizmasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="91401-181">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="91401-182">Nasıl görünümleri özel kullanarak uygulama içinde bulunur ve varsayılan kuralını özelleştirebilirsiniz [IViewLocationExpander](/aspnet/core/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span><span class="sxs-lookup"><span data-stu-id="91401-182">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/aspnet/core/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="91401-183">Görünüm bulma görünümü dosyaları dosya adına göre bulma kullanır.</span><span class="sxs-lookup"><span data-stu-id="91401-183">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="91401-184">Temeldeki dosya sistemi büyük küçük harfe duyarlı ise, görünüm adları büyük olasılıkla büyük küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="91401-184">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="91401-185">İşletim sistemleri arasında uyumluluk için denetleyici ve eylem adları ve ilişkili görünüm klasörleri ve dosya adları arasında büyük küçük harf duyarlı.</span><span class="sxs-lookup"><span data-stu-id="91401-185">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="91401-186">Bulunamayan bir görünüm dosyası büyük küçük harfe duyarlı dosya sistemi ile çalışırken bir hatayla karşılaşırsanız, büyük küçük harf istenen görünüm dosyası ve gerçek görünümü dosya adı arasında eşleştiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="91401-186">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="91401-187">Denetleyicileri, işlemler ve Bakım ve daha anlaşılır olması için görünümler arasındaki ilişkiyi yansıtmak kendi görünümlerinizi dosya yapısı düzenleme en iyi uygulama olarak izleyin.</span><span class="sxs-lookup"><span data-stu-id="91401-187">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="91401-188">Veri görünümleri geçirme</span><span class="sxs-lookup"><span data-stu-id="91401-188">Passing data to views</span></span>

<span data-ttu-id="91401-189">Çeşitli yaklaşımlar kullanarak görünümleri için veri geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="91401-189">You can pass data to views using several approaches.</span></span> <span data-ttu-id="91401-190">En güçlü belirtmek için bir yaklaşımdır bir [modeli](xref:mvc/models/model-binding) görünümünde türü.</span><span class="sxs-lookup"><span data-stu-id="91401-190">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="91401-191">Bu model, genellikle olarak adlandırılır bir *viewmodel*.</span><span class="sxs-lookup"><span data-stu-id="91401-191">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="91401-192">Viewmodel türünün bir örneği eylemden görünümüne geçin.</span><span class="sxs-lookup"><span data-stu-id="91401-192">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="91401-193">Bir görünüme veri iletmek için bir viewmodel kullanarak sağlar yararlanmak için görünümü *güçlü* türü denetleniyor.</span><span class="sxs-lookup"><span data-stu-id="91401-193">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="91401-194">*Güçlü yazarak* (veya *kesin türü belirtilmiş*) her değişken ve sabiti açıkça tanımlanmış bir türü olduğu anlamına gelir (örneğin, `string`, `int`, veya `DateTime`).</span><span class="sxs-lookup"><span data-stu-id="91401-194">*Strong typing* (or *strongly-typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="91401-195">Bir görünümde kullanılan türleri geçerliliğini derleme zamanında denetlenir.</span><span class="sxs-lookup"><span data-stu-id="91401-195">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="91401-196">[Visual Studio](https://www.visualstudio.com/vs/) ve [Visual Studio Code](https://code.visualstudio.com/) denilen bir özelliği kullanarak türü kesin belirlenmiş sınıf üyeleri liste [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="91401-196">[Visual Studio](https://www.visualstudio.com/vs/) and [Visual Studio Code](https://code.visualstudio.com/) list strongly-typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="91401-197">Bir viewmodel özelliklerini görmek istediğinizde, ardından bir noktayla viewmodel değişken adını yazın (`.`).</span><span class="sxs-lookup"><span data-stu-id="91401-197">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="91401-198">Bu kodu daha az hatalarla daha hızlı yazmanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="91401-198">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="91401-199">Kullanarak bir model belirtin `@model` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="91401-199">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="91401-200">Model ile kullanmak `@Model`:</span><span class="sxs-lookup"><span data-stu-id="91401-200">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="91401-201">Modeli görünüme sağlamak için denetleyici, bir parametre olarak geçirir:</span><span class="sxs-lookup"><span data-stu-id="91401-201">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="91401-202">Bir görünüme sağlayabilir modeli türlerinde bir kısıtlama yoktur.</span><span class="sxs-lookup"><span data-stu-id="91401-202">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="91401-203">Kullanmanızı öneririz **P**larak **O**ld **C**LR **O**nesne (POCO) viewmodels tanımlanan çok az kayıpla veya hiç davranışları (yöntemleri) sahip.</span><span class="sxs-lookup"><span data-stu-id="91401-203">We recommend using **P**lain **O**ld **C**LR **O**bject (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="91401-204">Genellikle, viewmodel sınıflar ya da depolanmış *modelleri* klasör veya ayrı bir *ViewModels* uygulama kökünde klasör.</span><span class="sxs-lookup"><span data-stu-id="91401-204">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="91401-205">*Adresi* yukarıdaki örnekte kullanılan viewmodel olan adındaki bir dosyada depolanan bir POCO viewmodel *Address.cs*:</span><span class="sxs-lookup"><span data-stu-id="91401-205">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

> [!NOTE]
> <span data-ttu-id="91401-206">Hiçbir şey viewmodel türleri ve iş modeli türleri için aynı sınıfları kullanmalarını engeller.</span><span class="sxs-lookup"><span data-stu-id="91401-206">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="91401-207">Ancak, ayrı modelleri kullanarak iş mantığı ve verileri, uygulamanızın erişim bölümleri bağımsız olarak değiştirmek kendi görünümlerinizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="91401-207">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="91401-208">Modelleri ve viewmodels ayrılması sunduğu güvenlik avantajlarından modelleri kullandığınızda [model bağlama](xref:mvc/models/model-binding) ve [doğrulama](xref:mvc/models/validation) uygulama için kullanıcı tarafından gönderilen veriler için.</span><span class="sxs-lookup"><span data-stu-id="91401-208">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>


<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-and-viewbag"></a><span data-ttu-id="91401-209">Zayıf yazılmış verileri (ViewData ve Görünüm Paketi)</span><span class="sxs-lookup"><span data-stu-id="91401-209">Weakly-typed data (ViewData and ViewBag)</span></span>

<span data-ttu-id="91401-210">Not: `ViewBag` Razor sayfalarında kullanılabilir değil.</span><span class="sxs-lookup"><span data-stu-id="91401-210">Note: `ViewBag` is not available in the Razor Pages.</span></span>

<span data-ttu-id="91401-211">Kesin türü belirtilmiş görünümleri yanı sıra, görünümleri erişimi bir *zayıf yazılmış* (olarak da bilinir *geniş yazılmış*) veri koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="91401-211">In addition to strongly-typed views, views have access to a *weakly-typed* (also called *loosely-typed*) collection of data.</span></span> <span data-ttu-id="91401-212">Güçlü türlerinin aksine *zayıf türleri* (veya *kaybetmiş türleri*) kullanmakta olduğunuz veri türünü açıkça bildirmeyin anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="91401-212">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="91401-213">Zayıf yazılmış veri koleksiyonunu küçük miktarda denetleyicileri ve görünümler ve dışındaki veri geçirmek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="91401-213">You can use the collection of weakly-typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="91401-214">Arasında veri geçirme bir...</span><span class="sxs-lookup"><span data-stu-id="91401-214">Passing data between a ...</span></span>                        | <span data-ttu-id="91401-215">Örnek</span><span class="sxs-lookup"><span data-stu-id="91401-215">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="91401-216">Denetleyici ve bir görünüm</span><span class="sxs-lookup"><span data-stu-id="91401-216">Controller and a view</span></span>                             | <span data-ttu-id="91401-217">Bir açılır liste verilerle doldurma.</span><span class="sxs-lookup"><span data-stu-id="91401-217">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="91401-218">Görünüm ve [görünümü](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="91401-218">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="91401-219">Ayarı  **\<title >** bir görünüm dosyasından düzeni görünümünde öğe içeriği.</span><span class="sxs-lookup"><span data-stu-id="91401-219">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="91401-220">[Kısmi Görünüm](xref:mvc/views/partial) ve bir görünüm</span><span class="sxs-lookup"><span data-stu-id="91401-220">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="91401-221">Kullanıcının istenen Web sayfasındaki göre verileri görüntüleyen bir pencere öğesi.</span><span class="sxs-lookup"><span data-stu-id="91401-221">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="91401-222">Bu koleksiyon yoluyla başvurulabilir `ViewData` veya `ViewBag` denetleyicileri ve görünümlere özellikleri.</span><span class="sxs-lookup"><span data-stu-id="91401-222">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="91401-223">`ViewData` Zayıf yazılmış nesneleri sözlüğü bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="91401-223">The `ViewData` property is a dictionary of weakly-typed objects.</span></span> <span data-ttu-id="91401-224">`ViewBag` Özelliktir çevresinde bir sarmalayıcı `ViewData` arka plandaki için dinamik özellikler sağlayan `ViewData` koleksiyonu.</span><span class="sxs-lookup"><span data-stu-id="91401-224">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span>

<span data-ttu-id="91401-225">`ViewData`ve `ViewBag` çalışma zamanında dinamik olarak çözümlendiği.</span><span class="sxs-lookup"><span data-stu-id="91401-225">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="91401-226">Derleme zamanı tür denetimi sunmuyoruz olduğundan, her ikisi de genellikle daha hata-bir viewmodel kullanmaktan daha fazladır.</span><span class="sxs-lookup"><span data-stu-id="91401-226">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="91401-227">Bu nedenle, bazı geliştiriciler en düşük düzeyde ya da hiç kullanmayı tercih ederseniz `ViewData` ve `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="91401-227">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>


<a name="VD"></a>

<span data-ttu-id="91401-228">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="91401-228">**ViewData**</span></span>

<span data-ttu-id="91401-229">`ViewData`olan bir [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) üzerinden erişilen nesne `string` anahtarları.</span><span class="sxs-lookup"><span data-stu-id="91401-229">`ViewData` is a [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="91401-230">Dize verilerini depolanır ve bir cast gerek kalmadan doğrudan kullanılır, ancak diğer atamalısınız `ViewData` bunları ayıkladığınızda belirli türleri için değer nesnesi.</span><span class="sxs-lookup"><span data-stu-id="91401-230">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="91401-231">Kullanabileceğiniz `ViewData` görünümlere ve görünümler dahil olmak üzere içinde denetleyicilerinden veri iletmek için [kısmi görünümler](xref:mvc/views/partial) ve [düzenleri](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="91401-231">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="91401-232">Aşağıdaki tebrik kartı ve bir adresi kullanarak ilişkin değerleri ayarlayan bir örnektir `ViewData` bir eylem:</span><span class="sxs-lookup"><span data-stu-id="91401-232">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="91401-233">Bir görünümdeki verilerle çalışma:</span><span class="sxs-lookup"><span data-stu-id="91401-233">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

<span data-ttu-id="91401-234">**Görünüm Paketi**</span><span class="sxs-lookup"><span data-stu-id="91401-234">**ViewBag**</span></span>

<span data-ttu-id="91401-235">Not: `ViewBag` Razor sayfalarında kullanılabilir değil.</span><span class="sxs-lookup"><span data-stu-id="91401-235">Note: `ViewBag` is not available in the Razor Pages.</span></span>

<span data-ttu-id="91401-236">`ViewBag`olan bir [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) depolanan nesnelere dinamik erişim sağlayan nesne `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="91401-236">`ViewBag` is a [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="91401-237">`ViewBag`atama gerektirmez, çalışmaya daha kullanışlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="91401-237">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="91401-238">Aşağıdaki örnekte nasıl kullanılacağını gösterir `ViewBag` kullanarak aynı sonucu ile `ViewData` yukarıda:</span><span class="sxs-lookup"><span data-stu-id="91401-238">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="91401-239">**ViewData ve Görünüm Paketi aynı anda kullanma**</span><span class="sxs-lookup"><span data-stu-id="91401-239">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="91401-240">Not: `ViewBag` Razor sayfalarında kullanılabilir değil.</span><span class="sxs-lookup"><span data-stu-id="91401-240">Note: `ViewBag` is not available in the Razor Pages.</span></span>

<span data-ttu-id="91401-241">Bu yana `ViewData` ve `ViewBag` aynı temel bakın `ViewData` koleksiyonu, her ikisi de kullanabilirsiniz `ViewData` ve `ViewBag` karıştırmak ve aralarındaki okurken ve yazarken değerleri aynı.</span><span class="sxs-lookup"><span data-stu-id="91401-241">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="91401-242">Başlık kullanılarak ayarlanan `ViewBag` ve açıklaması kullanarak `ViewData` en üstündeki bir *About.cshtml* görünümü:</span><span class="sxs-lookup"><span data-stu-id="91401-242">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="91401-243">Özellikleri okunmaya ancak kullanımını ters `ViewData` ve `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="91401-243">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="91401-244">İçinde *_Layout.cshtml* dosya, başlık kullanarak elde `ViewData` ve açıklaması kullanarak elde `ViewBag`:</span><span class="sxs-lookup"><span data-stu-id="91401-244">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="91401-245">Dizeler için bir cast gerektirmeyen unutmayın `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="91401-245">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="91401-246">Kullanabileceğiniz `@ViewData["Title"]` atama olmadan.</span><span class="sxs-lookup"><span data-stu-id="91401-246">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="91401-247">Her ikisini de kullanarak `ViewData` ve `ViewBag` adresindeki karıştırma ve okuma ve yazma özellikleri eşleştirme yoksa olarak aynı zaman çalışır.</span><span class="sxs-lookup"><span data-stu-id="91401-247">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="91401-248">Aşağıdaki biçimlendirmede oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="91401-248">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="91401-249">**Görünüm Paketi ViewData arasındaki farkları özeti**</span><span class="sxs-lookup"><span data-stu-id="91401-249">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="91401-250">`ViewBag`Razor sayfalarında kullanılabilir değil.</span><span class="sxs-lookup"><span data-stu-id="91401-250">`ViewBag` is not available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="91401-251">Türetilen [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)gibi yararlı olabilecek sözlük özelliklere sahip nedenle `ContainsKey`, `Add`, `Remove`, ve `Clear`.</span><span class="sxs-lookup"><span data-stu-id="91401-251">Derives from [ViewDataDictionary](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="91401-252">Boşluk izin sözlükteki anahtarları, dizelerdir.</span><span class="sxs-lookup"><span data-stu-id="91401-252">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="91401-253">Örnek:`ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="91401-253">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="91401-254">Herhangi türdeki dışındaki bir `string` kullanmak için görünümünde dönüştürülmelidir `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="91401-254">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="91401-255">Türetilen [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), böylece noktalı gösterim kullanılarak dinamik özellikleri oluşturulmasını sağlar (`@ViewBag.SomeKey = <value or object>`), ve hiçbir atama gereklidir.</span><span class="sxs-lookup"><span data-stu-id="91401-255">Derives from [DynamicViewData](/aspnet/core/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="91401-256">Söz dizimi `ViewBag` denetleyicileri ve görünümleri eklemek daha hızlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="91401-256">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="91401-257">Null değerler için denetlemek daha basit.</span><span class="sxs-lookup"><span data-stu-id="91401-257">Simpler to check for null values.</span></span> <span data-ttu-id="91401-258">Örnek:`@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="91401-258">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="91401-259">**Zaman ViewData veya Görünüm paketini kullanmak için**</span><span class="sxs-lookup"><span data-stu-id="91401-259">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="91401-260">Her ikisi de `ViewData` ve `ViewBag` eşit olan küçük miktarda denetleyicileri ve görünüm arasında veri geçirme için geçerli yaklaşımlar.</span><span class="sxs-lookup"><span data-stu-id="91401-260">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="91401-261">Hangisinin kullanılacağını Seçimi tercihine göre dayanır.</span><span class="sxs-lookup"><span data-stu-id="91401-261">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="91401-262">Karışık ve eşleşen `ViewData` ve `ViewBag` nesneleri, Bununla birlikte, kod okuyun ve sürekli olarak kullanılan bir yaklaşım ile korumak daha kolay.</span><span class="sxs-lookup"><span data-stu-id="91401-262">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="91401-263">Her iki yaklaşımın çalışma zamanında dinamik olarak çözümlenmiş ve çalışma zamanı hataları neden dolayısıyla saldırıya.</span><span class="sxs-lookup"><span data-stu-id="91401-263">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="91401-264">Bazı geliştirme ekiplerinin bunları kaçının.</span><span class="sxs-lookup"><span data-stu-id="91401-264">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="91401-265">Dinamik Görünüm</span><span class="sxs-lookup"><span data-stu-id="91401-265">Dynamic views</span></span>

<span data-ttu-id="91401-266">Bir model bildirmeyin görünümleri yazın kullanarak `@model` ancak onlara geçirilen bir model örneği sahip (örneğin, `return View(Address);`) örneğin özellikleri dinamik olarak başvurabilir:</span><span class="sxs-lookup"><span data-stu-id="91401-266">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="91401-267">Bu özellik esneklik sunar ancak derleme koruma veya IntelliSense sunmaz.</span><span class="sxs-lookup"><span data-stu-id="91401-267">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="91401-268">Web sayfası oluşturma özelliği yoksa, çalışma zamanında başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="91401-268">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="91401-269">Daha fazla görünümü özellikleri</span><span class="sxs-lookup"><span data-stu-id="91401-269">More view features</span></span>

<span data-ttu-id="91401-270">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) varolan HTML etiketleri için sunucu tarafı davranışı eklemek kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="91401-270">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="91401-271">Etiket Yardımcıları kullanarak özel kod veya kendi görünümlerinizi içinde Yardımcıları yazmak için gereken önler.</span><span class="sxs-lookup"><span data-stu-id="91401-271">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="91401-272">Etiket Yardımcıları olarak öznitelikleri HTML öğelerine uygulanır ve bunları işleyemiyor düzenleyiciler tarafından göz ardı edilir.</span><span class="sxs-lookup"><span data-stu-id="91401-272">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="91401-273">Bu, çeşitli araçları görünüm biçimlendirmesinde oluşturmak ve düzenlemek sağlar.</span><span class="sxs-lookup"><span data-stu-id="91401-273">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="91401-274">Özel HTML biçimlendirme oluşturmak çok sayıda yerleşik HTML Yardımcıları ile gerçekleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="91401-274">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="91401-275">Daha karmaşık kullanıcı arabirimi mantığı tarafından işlenebilir [görünüm bileşenleri](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="91401-275">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="91401-276">Görünüm bileşenleri aynı SoC o denetleyicileri sağlar ve görünümler sunar.</span><span class="sxs-lookup"><span data-stu-id="91401-276">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="91401-277">Bunlar, eylemleri ve ortak kullanıcı arabirimi öğeleri tarafından kullanılan verileri uğraşmanız görünümleri gereksinimini ortadan kaldırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="91401-277">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="91401-278">Birçok diğer yönlerini ASP.NET Core gibi görünümleri Destek [bağımlılık ekleme](xref:fundamentals/dependency-injection), olmasını hizmetlerine izin [görünümlere eklenen](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="91401-278">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
