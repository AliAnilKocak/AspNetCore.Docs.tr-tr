---
title: ASP.NET Core MVC uygulamasına görünüm ekleme
author: rick-anderson
description: Basit bir ASP.NET Core MVC uygulamasına görünüm ekleme
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: de75c3b0651c0cda6629af786d7db9dc83bc4fef
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288826"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="d4f65-103">ASP.NET Core MVC uygulamasına görünüm ekleme</span><span class="sxs-lookup"><span data-stu-id="d4f65-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="d4f65-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d4f65-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4f65-105">Bu bölümde, bir istemciye HTML yanıtları oluşturma işlemini düzgün bir şekilde kapsüllemek için `HelloWorldController` sınıfını [Razor](xref:mvc/views/razor) görünümü dosyalarını kullanacak şekilde değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="d4f65-106">Razor kullanarak bir görünüm şablonu dosyası oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-106">You create a view template file using Razor.</span></span> <span data-ttu-id="d4f65-107">Razor tabanlı görünüm şablonlarının *. cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="d4f65-108">Bu kişiler ile C#HTML çıktısı oluşturmanın zarif bir yolunu sağlarlar.</span><span class="sxs-lookup"><span data-stu-id="d4f65-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="d4f65-109">Şu anda `Index` yöntemi, denetleyici sınıfında sabit kodlanmış bir ileti içeren bir dize döndürür.</span><span class="sxs-lookup"><span data-stu-id="d4f65-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="d4f65-110">@No__t-0 sınıfında, `Index` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="d4f65-111">Yukarıdaki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="d4f65-112">HTML yanıtı oluşturmak için bir görünüm şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="d4f65-113">Yukarıdaki `Index` yöntemi gibi denetleyici Yöntemleri ( *eylem yöntemleri*olarak da bilinir), genellikle `string` gibi bir tür değil, genellikle bir <xref:Microsoft.AspNetCore.Mvc.IActionResult> (veya <xref:Microsoft.AspNetCore.Mvc.ActionResult> ' ten türetilmiş bir sınıf) döndürür.</span><span class="sxs-lookup"><span data-stu-id="d4f65-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="d4f65-114">Bir görünüm ekleyin</span><span class="sxs-lookup"><span data-stu-id="d4f65-114">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d4f65-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4f65-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d4f65-116">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="d4f65-117">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni öğe ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="d4f65-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="d4f65-118">**Yeni öğe Ekle-Mvcfilmi** iletişim kutusunda</span><span class="sxs-lookup"><span data-stu-id="d4f65-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="d4f65-119">Sağ üst köşedeki arama kutusuna *Görünüm* girin</span><span class="sxs-lookup"><span data-stu-id="d4f65-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="d4f65-120">**Razor görünümü** seçin</span><span class="sxs-lookup"><span data-stu-id="d4f65-120">Select **Razor View**</span></span>

  * <span data-ttu-id="d4f65-121">*Index. cshtml* **adlı ad** kutusu değerini saklayın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="d4f65-122">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="d4f65-122">Select **Add**</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d4f65-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d4f65-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d4f65-125">@No__t-1 için `Index` görünümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="d4f65-126">*Views/HelloWorld*adlı yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="d4f65-127">*Views/HelloWorld* klasör adı *Index. cshtml*dosyasına yeni bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d4f65-128">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4f65-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d4f65-129">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="d4f65-130">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni dosya ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="d4f65-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="d4f65-131">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="d4f65-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d4f65-132">Sol bölmedeki **Web** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-132">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="d4f65-133">Orta bölmedeki **boş HTML dosyasını** seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-133">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="d4f65-134">**Ad** kutusuna *Index. cshtml* yazın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-134">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="d4f65-135">**Yeni**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-135">Select **New**.</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="d4f65-137">*Views/HelloWorld/Index. cshtml* Razor görünüm dosyasının içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="d4f65-138">`https://localhost:{PORT}/HelloWorld` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d4f65-139">@No__t-1 ' deki `Index` yöntemi çok fazla değil; metodun tarayıcıya yanıt işlemek için bir görünüm şablonu dosyası kullanması gerektiğini belirten `return View();` ifadesini çalıştırdı.</span><span class="sxs-lookup"><span data-stu-id="d4f65-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="d4f65-140">Bir görünüm şablonu dosya adı belirtilmediğinden, MVC varsayılan görünüm dosyasını kullanmaya göre varsayılan olarak ayarlanmış.</span><span class="sxs-lookup"><span data-stu-id="d4f65-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="d4f65-141">Varsayılan görünüm dosyası yöntemiyle aynı ada sahiptir (`Index`), bu nedenle */views/HelloWorld/Index.cshtml* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="d4f65-142">Aşağıdaki görüntüde "görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir</span><span class="sxs-lookup"><span data-stu-id="d4f65-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="d4f65-143">görünümde sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="d4f65-143">hard-coded in the view.</span></span>

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="d4f65-145">Görünümleri ve düzen sayfalarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="d4f65-145">Change views and layout pages</span></span>

<span data-ttu-id="d4f65-146">Menü bağlantılarını (**Mvcmovie**, **Home**ve **Gizlilik**) seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="d4f65-147">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="d4f65-148">Menü düzeni *Görünümler/Shared/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="d4f65-149">*Views/Shared/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d4f65-150">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="d4f65-151">@No__t-0 satırını bulun.</span><span class="sxs-lookup"><span data-stu-id="d4f65-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d4f65-152">`RenderBody`, oluşturduğunuz tüm görünüme özgü sayfaların, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d4f65-153">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Home/privacy. cshtml** görünümü `RenderBody` yöntemi içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="d4f65-154">Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="d4f65-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="d4f65-155">*Views/Shared/_Layout. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="d4f65-156">Değişiklikler vurgulanır:</span><span class="sxs-lookup"><span data-stu-id="d4f65-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="d4f65-157">Yukarıdaki biçimlendirme aşağıdaki değişiklikleri yaptı:</span><span class="sxs-lookup"><span data-stu-id="d4f65-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="d4f65-158">3 `MvcMovie` ' a `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="d4f65-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="d4f65-159">@No__t-0 ' dan `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` ' e kadar bağlayıcı öğesi.</span><span class="sxs-lookup"><span data-stu-id="d4f65-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="d4f65-160">Yukarıdaki biçimlendirmede, bu uygulama [alan](xref:mvc/controllers/areas)kullandığından `asp-area=""` [bağlantı etiketi Yardımcısı özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ve öznitelik değeri atlandı.</span><span class="sxs-lookup"><span data-stu-id="d4f65-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="d4f65-161">**Not**: `Movies` denetleyicisi uygulanmadı.</span><span class="sxs-lookup"><span data-stu-id="d4f65-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="d4f65-162">Bu noktada `Movie App` bağlantısı işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="d4f65-163">Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="d4f65-164">Tarayıcı sekmesindeki başlığın Gizlilik ilkesi yerine bir **film uygulaması** (Gizlilik ilkesi değil) nasıl görüntülediğini fark edin **-MVC filmi**:</span><span class="sxs-lookup"><span data-stu-id="d4f65-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="d4f65-166">**Giriş** bağlantısını seçin ve başlık ve bağlantı metninin **film uygulamasını**da görüntülediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="d4f65-167">Düzen şablonunda değişikliği bir kez yapabildik ve sitedeki tüm sayfalar yeni bağlantı metnini ve yeni başlığı yansıtmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="d4f65-168">*Views/_ViewStart. cshtml* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="d4f65-169">*Views/_ViewStart. cshtml* dosyası, *Görünümler/Shared/_Layout. cshtml* dosyasını her bir görünüme getirir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="d4f65-170">@No__t-0 özelliği farklı bir düzen görünümü ayarlamak veya `null` olarak ayarlamak için kullanılabilir; Bu nedenle hiçbir düzen dosyası kullanılmayacak.</span><span class="sxs-lookup"><span data-stu-id="d4f65-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="d4f65-171">*Views/HelloWorld/Index. cshtml* görünüm dosyasının title ve `<h2>` öğesini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="d4f65-172">Başlık ve `<h2>` öğesi biraz farklıdır, bu sayede kodun hangi bitini görüntülemesini görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="d4f65-173">Yukarıdaki kodda `ViewData["Title"] = "Movie List";`, `ViewData` sözlüğünün `Title` özelliğini "film listesi" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4f65-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="d4f65-174">@No__t-0 özelliği, Düzen sayfasındaki `<title>` HTML öğesinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d4f65-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="d4f65-175">Değişikliği kaydedin ve `https://localhost:{PORT}/HelloWorld` ' a gidin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d4f65-176">Tarayıcı başlığı, birincil başlık ve ikincil başlıkların değiştirildiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="d4f65-177">(Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülüyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="d4f65-178">Sunucudan gelen yanıtı zorlamak için tarayıcınızda CTRL + F5 tuşlarına basın.) Browser başlığı, *Index. cshtml* görünüm şablonunda ve düzen dosyasına eklenen ek "-film uygulaması" olarak ayarlandığımız `ViewData["Title"]` ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="d4f65-179">*Index. cshtml* görünüm şablonundaki içerik *views/Shared/_Layout. cshtml* görünüm şablonuyla birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="d4f65-180">Tarayıcıya tek bir HTML yanıtı gönderilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="d4f65-181">Düzen şablonları, bir uygulamadaki tüm sayfalara uygulanan değişiklikler yapmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="d4f65-182">Daha fazla bilgi için bkz. [Düzen](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="d4f65-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Film listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="d4f65-184">"Data" (Bu durumda "Görünümümüzden Merhaba!") çok az.</span><span class="sxs-lookup"><span data-stu-id="d4f65-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="d4f65-185">ileti) sabit kodludur, ancak.</span><span class="sxs-lookup"><span data-stu-id="d4f65-185">message) is hard-coded, though.</span></span> <span data-ttu-id="d4f65-186">MVC uygulamasında bir "V" (görünüm) var ve bir "C" (denetleyici) var, ancak henüz "M" (model) yok.</span><span class="sxs-lookup"><span data-stu-id="d4f65-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="d4f65-187">Denetleyiciden görünüme veri geçirme</span><span class="sxs-lookup"><span data-stu-id="d4f65-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="d4f65-188">Gelen URL isteğine yanıt olarak denetleyici eylemleri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="d4f65-189">Bir denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="d4f65-190">Denetleyici verileri bir veri kaynağından alır ve tarayıcıya ne tür bir yanıt gönderileceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="d4f65-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="d4f65-191">Görünüm şablonları bir denetleyiciden, tarayıcıya HTML yanıtı oluşturmak ve biçimlendirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="d4f65-192">Bir görünüm şablonunun yanıt işlemesi için gereken verileri sağlamaktan denetleyiciler sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="d4f65-193">En iyi yöntem: Görünüm şablonları iş **mantığı gerçekleştirmemelidir** veya doğrudan bir veritabanıyla etkileşime girmemelidir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="d4f65-194">Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sunulan verilerle birlikte çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="d4f65-195">Bu "kaygıları ayrımı", kodun temiz, test edilebilir ve sürdürülebilir kalmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="d4f65-196">Şu anda, `HelloWorldController` sınıfındaki `Welcome` yöntemi `name` ve `ID` parametresini alır ve sonra değerleri doğrudan tarayıcıya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="d4f65-197">Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, denetleyiciyi bir görünüm şablonu kullanacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="d4f65-198">Görünüm şablonu dinamik bir yanıt üretir, bu, yanıtı oluşturmak için denetleyiciden görünüme uygun veri bitlerinin geçirilmesi gereken anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="d4f65-199">Bu, denetleyicinin görünüm şablonunun erişebileceği dinamik verileri (parametreler) bir `ViewData` sözlüğünde yerleştirerek bunu yapın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="d4f65-200">*HelloWorldController.cs*' de `Welcome` yöntemini değiştirerek `ViewData` sözlüğüne bir `Message` ve `NumTimes` değeri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="d4f65-201">@No__t-0 sözlüğü dinamik bir nesnedir; bu, herhangi bir türün kullanılabileceği anlamına gelir; `ViewData` nesnesi, içine bir öğe yerleştirene kadar tanımlanmış özelliklere sahip değil.</span><span class="sxs-lookup"><span data-stu-id="d4f65-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="d4f65-202">[MVC modeli bağlama sistemi](xref:mvc/models/model-binding) , adlandırılmış parametreleri (`name` ve `numTimes`), adres çubuğundaki sorgu dizesinden yöntemdeki parametrelere otomatik olarak eşler.</span><span class="sxs-lookup"><span data-stu-id="d4f65-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="d4f65-203">Tüm *HelloWorldController.cs* dosyası şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="d4f65-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="d4f65-204">@No__t-0 sözlük nesnesi görünüme geçirilecek verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="d4f65-205">*Görünümler/HelloWorld/Welcome. cshtml*adlı bir hoş geldiniz görünüm şablonu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d4f65-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="d4f65-206">*Welcome. cshtml* görünüm şablonunda "Hello" `NumTimes` ' i görüntüleyen bir döngü oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="d4f65-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="d4f65-207">*Views/HelloWorld/Welcome. cshtml* içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="d4f65-208">Değişikliklerinizi kaydedin ve aşağıdaki URL 'ye gidin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="d4f65-209">Veriler URL 'den alınır ve [MVC model Bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="d4f65-210">Denetleyici verileri `ViewData` sözlüğüne paketler ve bu nesneyi görünüme geçirir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="d4f65-211">Daha sonra Görünüm, verileri tarayıcıda HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="d4f65-211">The view then renders the data as HTML to the browser.</span></span>

![Bir hoş geldiniz etiketi ve dört kez gösterilen Hello Rick ifadesi gösteren gizlilik görünümü](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="d4f65-213">Yukarıdaki örnekte, denetleyicideki verileri bir görünüme geçirmek için `ViewData` sözlüğü kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="d4f65-214">Öğreticide daha sonra bir görünüm modeli, bir denetleyicideki verileri bir görünüme geçirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="d4f65-215">Veri geçirme yaklaşımını görüntüleme yaklaşımı genellikle `ViewData` sözlük yaklaşımına göre çok tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="d4f65-216">Daha fazla bilgi için bkz. [ViewBag, ViewData veya TempData kullanma](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="d4f65-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="d4f65-217">Sonraki öğreticide, bir film veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d4f65-218">[Önceki](adding-controller.md)
> [İleri](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="d4f65-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4f65-219">Bu bölümde, bir istemciye HTML yanıtları oluşturma işlemini düzgün bir şekilde kapsüllemek için `HelloWorldController` sınıfını [Razor](xref:mvc/views/razor) görünümü dosyalarını kullanacak şekilde değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="d4f65-220">Razor kullanarak bir görünüm şablonu dosyası oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-220">You create a view template file using Razor.</span></span> <span data-ttu-id="d4f65-221">Razor tabanlı görünüm şablonlarının *. cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="d4f65-222">Bu kişiler ile C#HTML çıktısı oluşturmanın zarif bir yolunu sağlarlar.</span><span class="sxs-lookup"><span data-stu-id="d4f65-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="d4f65-223">Şu anda `Index` yöntemi, denetleyici sınıfında sabit kodlanmış bir ileti içeren bir dize döndürür.</span><span class="sxs-lookup"><span data-stu-id="d4f65-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="d4f65-224">@No__t-0 sınıfında, `Index` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="d4f65-225">Yukarıdaki kod denetleyicinin <xref:Microsoft.AspNetCore.Mvc.Controller.View*> yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="d4f65-226">HTML yanıtı oluşturmak için bir görünüm şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="d4f65-227">Yukarıdaki `Index` yöntemi gibi denetleyici Yöntemleri ( *eylem yöntemleri*olarak da bilinir), genellikle `string` gibi bir tür değil, genellikle bir <xref:Microsoft.AspNetCore.Mvc.IActionResult> (veya <xref:Microsoft.AspNetCore.Mvc.ActionResult> ' ten türetilmiş bir sınıf) döndürür.</span><span class="sxs-lookup"><span data-stu-id="d4f65-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="d4f65-228">Bir görünüm ekleyin</span><span class="sxs-lookup"><span data-stu-id="d4f65-228">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d4f65-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4f65-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d4f65-230">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="d4f65-231">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni öğe ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="d4f65-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="d4f65-232">**Yeni öğe Ekle-Mvcfilmi** iletişim kutusunda</span><span class="sxs-lookup"><span data-stu-id="d4f65-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="d4f65-233">Sağ üst köşedeki arama kutusuna *Görünüm* girin</span><span class="sxs-lookup"><span data-stu-id="d4f65-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="d4f65-234">**Razor görünümü** seçin</span><span class="sxs-lookup"><span data-stu-id="d4f65-234">Select **Razor View**</span></span>

  * <span data-ttu-id="d4f65-235">*Index. cshtml* **adlı ad** kutusu değerini saklayın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="d4f65-236">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="d4f65-236">Select **Add**</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d4f65-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d4f65-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d4f65-239">@No__t-1 için `Index` görünümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="d4f65-240">*Views/HelloWorld*adlı yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="d4f65-241">*Views/HelloWorld* klasör adı *Index. cshtml*dosyasına yeni bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d4f65-242">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4f65-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d4f65-243">*Görünümler* klasörüne sağ tıklayın ve ardından **Yeni > klasör ekleyin** ve *HelloWorld*klasörünü adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="d4f65-244">*Görünümler/HelloWorld* klasörüne sağ tıklayın ve ardından **> yeni dosya ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="d4f65-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="d4f65-245">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="d4f65-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d4f65-246">Sol bölmedeki **Web** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="d4f65-247">Orta bölmedeki **boş HTML dosyasını** seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="d4f65-248">**Ad** kutusuna *Index. cshtml* yazın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="d4f65-249">**Yeni**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-249">Select **New**.</span></span>

![Yeni öğe Ekle iletişim kutusu](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="d4f65-251">*Views/HelloWorld/Index. cshtml* Razor görünüm dosyasının içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="d4f65-252">`https://localhost:{PORT}/HelloWorld` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d4f65-253">@No__t-1 ' deki `Index` yöntemi çok fazla değil; metodun tarayıcıya yanıt işlemek için bir görünüm şablonu dosyası kullanması gerektiğini belirten `return View();` ifadesini çalıştırdı.</span><span class="sxs-lookup"><span data-stu-id="d4f65-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="d4f65-254">Bir görünüm şablonu dosya adı belirtilmediğinden, MVC varsayılan görünüm dosyasını kullanmaya göre varsayılan olarak ayarlanmış.</span><span class="sxs-lookup"><span data-stu-id="d4f65-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="d4f65-255">Varsayılan görünüm dosyası yöntemiyle aynı ada sahiptir (`Index`), bu nedenle */views/HelloWorld/Index.cshtml* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="d4f65-256">Aşağıdaki görüntüde "görünüm Şablonumuzdan Merhaba!" dizesi gösterilmektedir</span><span class="sxs-lookup"><span data-stu-id="d4f65-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="d4f65-257">görünümde sabit kodlanmış.</span><span class="sxs-lookup"><span data-stu-id="d4f65-257">hard-coded in the view.</span></span>

![Tarayıcı penceresi](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="d4f65-259">Görünümleri ve düzen sayfalarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="d4f65-259">Change views and layout pages</span></span>

<span data-ttu-id="d4f65-260">Menü bağlantılarını (**Mvcmovie**, **Home**ve **Gizlilik**) seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="d4f65-261">Her sayfada aynı menü düzeni gösterilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="d4f65-262">Menü düzeni *Görünümler/Shared/_Layout. cshtml* dosyasında uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="d4f65-263">*Views/Shared/_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d4f65-264">[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitenizdeki birden çok sayfaya uygulamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="d4f65-265">@No__t-0 satırını bulun.</span><span class="sxs-lookup"><span data-stu-id="d4f65-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d4f65-266">`RenderBody`, oluşturduğunuz tüm görünüme özgü sayfaların, Düzen sayfasında *kaydırılan* bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d4f65-267">Örneğin, **Gizlilik** bağlantısını seçerseniz, **Görünümler/Home/privacy. cshtml** görünümü `RenderBody` yöntemi içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="d4f65-268">Düzen dosyasındaki başlık, altbilgi ve menü bağlantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="d4f65-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="d4f65-269">Başlık ve altbilgi öğelerinde `MvcMovie` ' ı `Movie App` ' e değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="d4f65-270">@No__t-0 bağlantı öğesini `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="d4f65-271">Aşağıdaki biçimlendirme vurgulanan değişiklikleri göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d4f65-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="d4f65-272">Yukarıdaki biçimlendirmede, bu uygulama [alan](xref:mvc/controllers/areas)kullandığından `asp-area` [bağlantı etiketi Yardımcısı özniteliği](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) atlandı.</span><span class="sxs-lookup"><span data-stu-id="d4f65-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="d4f65-273">**Not**: `Movies` denetleyicisi uygulanmadı.</span><span class="sxs-lookup"><span data-stu-id="d4f65-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="d4f65-274">Bu noktada `Movie App` bağlantısı işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="d4f65-275">Değişikliklerinizi kaydedin ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="d4f65-276">Tarayıcı sekmesindeki başlığın Gizlilik ilkesi yerine bir **film uygulaması** (Gizlilik ilkesi değil) nasıl görüntülediğini fark edin **-MVC filmi**:</span><span class="sxs-lookup"><span data-stu-id="d4f65-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Gizlilik sekmesi](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="d4f65-278">**Giriş** bağlantısını seçin ve başlık ve bağlantı metninin **film uygulamasını**da görüntülediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="d4f65-279">Düzen şablonunda değişikliği bir kez yapabildik ve sitedeki tüm sayfalar yeni bağlantı metnini ve yeni başlığı yansıtmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="d4f65-280">*Views/_ViewStart. cshtml* dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="d4f65-281">*Views/_ViewStart. cshtml* dosyası, *Görünümler/Shared/_Layout. cshtml* dosyasını her bir görünüme getirir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="d4f65-282">@No__t-0 özelliği farklı bir düzen görünümü ayarlamak veya `null` olarak ayarlamak için kullanılabilir; Bu nedenle hiçbir düzen dosyası kullanılmayacak.</span><span class="sxs-lookup"><span data-stu-id="d4f65-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="d4f65-283">*Views/HelloWorld/Index. cshtml* görünüm dosyasının title ve `<h2>` öğesini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="d4f65-284">Başlık ve `<h2>` öğesi biraz farklıdır, bu sayede kodun hangi bitini görüntülemesini görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="d4f65-285">Yukarıdaki kodda `ViewData["Title"] = "Movie List";`, `ViewData` sözlüğünün `Title` özelliğini "film listesi" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4f65-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="d4f65-286">@No__t-0 özelliği, Düzen sayfasındaki `<title>` HTML öğesinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d4f65-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="d4f65-287">Değişikliği kaydedin ve `https://localhost:{PORT}/HelloWorld` ' a gidin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d4f65-288">Tarayıcı başlığı, birincil başlık ve ikincil başlıkların değiştirildiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="d4f65-289">(Tarayıcıda değişiklik görmüyorsanız, önbelleğe alınmış içeriği görüntülüyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4f65-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="d4f65-290">Sunucudan gelen yanıtı zorlamak için tarayıcınızda CTRL + F5 tuşlarına basın.) Browser başlığı, *Index. cshtml* görünüm şablonunda ve düzen dosyasına eklenen ek "-film uygulaması" olarak ayarlandığımız `ViewData["Title"]` ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="d4f65-291">Ayrıca, *Index. cshtml* görünüm şablonundaki içeriğin *Görünümler/Shared/_Layout. cshtml* görünüm şablonuyla nasıl birleştirildiğine ve tarayıcıya tek bir HTML yanıtının gönderildiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="d4f65-292">Düzen şablonları, uygulamanızdaki tüm sayfalara uygulanan değişiklikler yapmayı gerçekten kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="d4f65-293">Daha fazla bilgi için bkz. [Düzen](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="d4f65-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Film listesi görünümü](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="d4f65-295">"Data" (Bu durumda "Görünümümüzden Merhaba!") çok az.</span><span class="sxs-lookup"><span data-stu-id="d4f65-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="d4f65-296">ileti) sabit kodludur, ancak.</span><span class="sxs-lookup"><span data-stu-id="d4f65-296">message) is hard-coded, though.</span></span> <span data-ttu-id="d4f65-297">MVC uygulamasında bir "V" (görünüm) var ve bir "C" (denetleyici) var, ancak henüz "M" (model) yok.</span><span class="sxs-lookup"><span data-stu-id="d4f65-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="d4f65-298">Denetleyiciden görünüme veri geçirme</span><span class="sxs-lookup"><span data-stu-id="d4f65-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="d4f65-299">Gelen URL isteğine yanıt olarak denetleyici eylemleri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="d4f65-300">Bir denetleyici sınıfı, gelen tarayıcı isteklerini işleyen kodun yazıldığı yerdir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="d4f65-301">Denetleyici verileri bir veri kaynağından alır ve tarayıcıya ne tür bir yanıt gönderileceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="d4f65-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="d4f65-302">Görünüm şablonları bir denetleyiciden, tarayıcıya HTML yanıtı oluşturmak ve biçimlendirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="d4f65-303">Bir görünüm şablonunun yanıt işlemesi için gereken verileri sağlamaktan denetleyiciler sorumludur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="d4f65-304">En iyi yöntem: Görünüm şablonları iş **mantığı gerçekleştirmemelidir** veya doğrudan bir veritabanıyla etkileşime girmemelidir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="d4f65-305">Bunun yerine, bir görünüm şablonu yalnızca denetleyici tarafından sunulan verilerle birlikte çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="d4f65-306">Bu "kaygıları ayrımı", kodun temiz, test edilebilir ve sürdürülebilir kalmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="d4f65-307">Şu anda, `HelloWorldController` sınıfındaki `Welcome` yöntemi `name` ve `ID` parametresini alır ve sonra değerleri doğrudan tarayıcıya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="d4f65-308">Denetleyicinin bu yanıtı bir dize olarak işlemesini sağlamak yerine, denetleyiciyi bir görünüm şablonu kullanacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="d4f65-309">Görünüm şablonu dinamik bir yanıt üretir, bu, yanıtı oluşturmak için denetleyiciden görünüme uygun veri bitlerinin geçirilmesi gereken anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="d4f65-310">Bu, denetleyicinin görünüm şablonunun erişebileceği dinamik verileri (parametreler) bir `ViewData` sözlüğünde yerleştirerek bunu yapın.</span><span class="sxs-lookup"><span data-stu-id="d4f65-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="d4f65-311">*HelloWorldController.cs*' de `Welcome` yöntemini değiştirerek `ViewData` sözlüğüne bir `Message` ve `NumTimes` değeri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4f65-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="d4f65-312">@No__t-0 sözlüğü dinamik bir nesnedir; bu, herhangi bir türün kullanılabileceği anlamına gelir; `ViewData` nesnesi, içine bir öğe yerleştirene kadar tanımlanmış özelliklere sahip değil.</span><span class="sxs-lookup"><span data-stu-id="d4f65-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="d4f65-313">[MVC modeli bağlama sistemi](xref:mvc/models/model-binding) , adlandırılmış parametreleri (`name` ve `numTimes`), adres çubuğundaki sorgu dizesinden yöntemdeki parametrelere otomatik olarak eşler.</span><span class="sxs-lookup"><span data-stu-id="d4f65-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="d4f65-314">Tüm *HelloWorldController.cs* dosyası şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="d4f65-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="d4f65-315">@No__t-0 sözlük nesnesi görünüme geçirilecek verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="d4f65-316">*Görünümler/HelloWorld/Welcome. cshtml*adlı bir hoş geldiniz görünüm şablonu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d4f65-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="d4f65-317">*Welcome. cshtml* görünüm şablonunda "Hello" `NumTimes` ' i görüntüleyen bir döngü oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="d4f65-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="d4f65-318">*Views/HelloWorld/Welcome. cshtml* içeriğini aşağıdakiler ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="d4f65-319">Değişikliklerinizi kaydedin ve aşağıdaki URL 'ye gidin:</span><span class="sxs-lookup"><span data-stu-id="d4f65-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="d4f65-320">Veriler URL 'den alınır ve [MVC model Bağlayıcısı](xref:mvc/models/model-binding) kullanılarak denetleyiciye geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="d4f65-321">Denetleyici verileri `ViewData` sözlüğüne paketler ve bu nesneyi görünüme geçirir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="d4f65-322">Daha sonra Görünüm, verileri tarayıcıda HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="d4f65-322">The view then renders the data as HTML to the browser.</span></span>

![Bir hoş geldiniz etiketi ve dört kez gösterilen Hello Rick ifadesi gösteren gizlilik görünümü](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="d4f65-324">Yukarıdaki örnekte, denetleyicideki verileri bir görünüme geçirmek için `ViewData` sözlüğü kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="d4f65-325">Öğreticide daha sonra bir görünüm modeli, bir denetleyicideki verileri bir görünüme geçirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d4f65-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="d4f65-326">Veri geçirme yaklaşımını görüntüleme yaklaşımı genellikle `ViewData` sözlük yaklaşımına göre çok tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="d4f65-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="d4f65-327">Daha fazla bilgi için bkz. [ViewBag, ViewData veya TempData kullanma](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="d4f65-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="d4f65-328">Sonraki öğreticide, bir film veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d4f65-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d4f65-329">[Önceki](adding-controller.md)
> [İleri](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="d4f65-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
