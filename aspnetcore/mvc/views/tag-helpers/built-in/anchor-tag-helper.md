---
title: "Yer işareti etiketi Yardımcısı"
author: pkellner
description: "ASP.NET Core yer işareti etiketi yardımcı öznitelik ve her öznitelik HTML yer işareti etiketi davranışını genişletme oynadığı rolü bulur."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 01/31/2018
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: f3b704174c3287edda12725b7973a2464e485bac
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/01/2018
---
# <a name="anchor-tag-helper"></a><span data-ttu-id="2713b-103">Yer işareti etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="2713b-103">Anchor Tag Helper</span></span>

<span data-ttu-id="2713b-104">Tarafından [Peter Kellner](http://peterkellner.net) ve [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2713b-104">By [Peter Kellner](http://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2713b-105">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/tag-helpers/built-in/samples/TagHelpersBuiltInAspNetCore) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2713b-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tag-helpers/built-in/samples/TagHelpersBuiltInAspNetCore) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2713b-106">[Yer işareti etiketi yardımcı](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper) standart HTML bağlantı geliştirir (`<a ... ></a>`) yeni öznitelikler ekleyerek etiketi.</span><span class="sxs-lookup"><span data-stu-id="2713b-106">The [Anchor Tag Helper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="2713b-107">Kurala göre öznitelik adları ile önek `asp-`.</span><span class="sxs-lookup"><span data-stu-id="2713b-107">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="2713b-108">İşlenen bağlantı öğenin `href` öznitelik değeri değerleri tarafından belirlenir `asp-` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="2713b-108">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="2713b-109">*SpeakerController* örnekleri bu belge boyunca kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2713b-109">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltInAspNetCore/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

<span data-ttu-id="2713b-110">Bir envanterini `asp-` aşağıdaki öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="2713b-110">An inventory of the `asp-` attributes follows.</span></span>

## <a name="asp-controller"></a><span data-ttu-id="2713b-111">ASP denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="2713b-111">asp-controller</span></span>

<span data-ttu-id="2713b-112">[Asp denetleyicisi](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.controller) özniteliği URL oluşturmak için kullanılan denetleyici atar.</span><span class="sxs-lookup"><span data-stu-id="2713b-112">The [asp-controller](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.controller) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="2713b-113">Aşağıdaki biçimlendirmede tüm konuşmacılar listeler:</span><span class="sxs-lookup"><span data-stu-id="2713b-113">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="2713b-114">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-114">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="2713b-115">Varsa `asp-controller` özniteliği belirtilmediyse ve `asp-action` değil, varsayılan `asp-action` şu anda yürütülen görünüm ile ilişkilendirilen denetleyici eylemi bir değerdir.</span><span class="sxs-lookup"><span data-stu-id="2713b-115">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="2713b-116">Varsa `asp-action` önceki biçimlendirmeden atlanır ve yer işareti etiketi yardımcı kullanılan *HomeController*'s *dizin* Görünüm (*/ev*), oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-116">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

## <a name="asp-action"></a><span data-ttu-id="2713b-117">ASP eylemi</span><span class="sxs-lookup"><span data-stu-id="2713b-117">asp-action</span></span>

<span data-ttu-id="2713b-118">[Asp eylem](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.action) öznitelik değerini temsil eder oluşturulmuş dahil denetleyici eylem adı `href` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="2713b-118">The [asp-action](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.action) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="2713b-119">Aşağıdaki biçimlendirmede oluşturulan ayarlar `href` Konuşmacı değerlendirmeleri sayfasına öznitelik değeri:</span><span class="sxs-lookup"><span data-stu-id="2713b-119">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="2713b-120">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-120">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="2713b-121">Öyle değilse `asp-controller` özniteliği belirtilmezse, geçerli görünümü yürütme görünümü çağırma varsayılan denetleyicisi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2713b-121">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="2713b-122">Varsa `asp-action` öznitelik değeri `Index`, hiçbir eylem varsayılan çağrılması için önde gelen URL, eklenecek sonra `Index` eylem.</span><span class="sxs-lookup"><span data-stu-id="2713b-122">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="2713b-123">Eylem belirtilen (veya varsayılan), başvurulan denetleyicisi bulunmalıdır `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="2713b-123">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

## <a name="asp-route-value"></a><span data-ttu-id="2713b-124">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="2713b-124">asp-route-{value}</span></span>

<span data-ttu-id="2713b-125">[Asp - rota-{value}](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.routevalues) öznitelik joker karakter rota öneki sağlar.</span><span class="sxs-lookup"><span data-stu-id="2713b-125">The [asp-route-{value}](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.routevalues) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="2713b-126">Herhangi bir değer kaplayan `{value}` yer tutucu, olası bir rota parametresi olarak yorumlanır.</span><span class="sxs-lookup"><span data-stu-id="2713b-126">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="2713b-127">Varsayılan bir yol bulunmazsa, bu rota öneki eklenir oluşturulan `href` istek parametresi ve değeri olarak özniteliği.</span><span class="sxs-lookup"><span data-stu-id="2713b-127">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="2713b-128">Aksi takdirde, rota şablonu konur.</span><span class="sxs-lookup"><span data-stu-id="2713b-128">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="2713b-129">Aşağıdaki denetleyici eylemi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2713b-129">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltInAspNetCore/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="2713b-130">Tanımlanan varsayılan rota şablonuyla *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="2713b-130">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltInAspNetCore/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="2713b-131">MVC görünüm gibi eylem tarafından sağlanan modeli kullanır:</span><span class="sxs-lookup"><span data-stu-id="2713b-131">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="2713b-132">Varsayılan rotanın `{id?}` yer tutucu eşleşti.</span><span class="sxs-lookup"><span data-stu-id="2713b-132">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="2713b-133">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-133">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="2713b-134">Rota öneki eşleşen yönlendirme şablonunun parçası değil olarak aşağıdaki MVC görünümü ile varsayın:</span><span class="sxs-lookup"><span data-stu-id="2713b-134">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker" 
       asp-action="Detail" 
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="2713b-135">Aşağıdaki HTML çünkü oluşturulan `speakerid` eşleşen rotada bulunamadı:</span><span class="sxs-lookup"><span data-stu-id="2713b-135">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="2713b-136">Her iki `asp-controller` veya `asp-action` de olduğu gibi aynı varsayılan işleme ardından belirtilen olmayan `asp-route` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="2713b-136">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

## <a name="asp-route"></a><span data-ttu-id="2713b-137">ASP yol</span><span class="sxs-lookup"><span data-stu-id="2713b-137">asp-route</span></span>

<span data-ttu-id="2713b-138">[Asp rota](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.route) özniteliği, bir URL bir adlandırılmış rota doğrudan bağlama oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2713b-138">The [asp-route](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.route) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="2713b-139">Kullanarak [yönlendirme öznitelikleri](xref:mvc/controllers/routing#attribute-routing), bir rota gösterildiği gibi adlı `SpeakerController` ve kullanılan kendi `Evaluations` eylem:</span><span class="sxs-lookup"><span data-stu-id="2713b-139">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="2713b-140">Aşağıdaki biçimlendirmede `asp-route` özniteliği adlandırılmış rota başvuruyor:</span><span class="sxs-lookup"><span data-stu-id="2713b-140">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="2713b-141">Yer işareti etiketi yardımcı doğrudan URL'yi kullanarak bu denetleyici eylemi için bir yol oluşturur */Konuşmacı/değerlendirmeleri*.</span><span class="sxs-lookup"><span data-stu-id="2713b-141">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="2713b-142">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-142">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="2713b-143">Varsa `asp-controller` veya `asp-action` ek olarak belirtilen `asp-route`, oluşturulan rota beklediğiniz olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="2713b-143">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="2713b-144">Bir rota çakışmayı önlemek için `asp-route` ile kullanılmaması `asp-controller` ve `asp-action` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="2713b-144">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

## <a name="asp-all-route-data"></a><span data-ttu-id="2713b-145">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="2713b-145">asp-all-route-data</span></span>

<span data-ttu-id="2713b-146">[Tüm rota veri asp](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.routevalues) özniteliği, anahtar-değer çiftleri sözlüğü oluşturulmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="2713b-146">The [asp-all-route-data](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.routevalues) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="2713b-147">Parametre adı anahtarıdır ve değerin parametre değeridir.</span><span class="sxs-lookup"><span data-stu-id="2713b-147">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="2713b-148">Aşağıdaki örnekte, bir sözlük başlatıldı ve Razor görünümüne geçirildi.</span><span class="sxs-lookup"><span data-stu-id="2713b-148">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="2713b-149">Alternatif olarak, veri modeliniz oturum geçirilen.</span><span class="sxs-lookup"><span data-stu-id="2713b-149">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="2713b-150">Yukarıdaki kod aşağıdaki HTML oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2713b-150">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="2713b-151">`asp-all-route-data` Aşırı yüklenmiş gereksinimlerini karşılayan bir sorgu dizesi üretmek için Sözlük düzleştirilmiş `Evaluations` eylem:</span><span class="sxs-lookup"><span data-stu-id="2713b-151">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltInAspNetCore/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="2713b-152">Sözlükteki tüm anahtarları rota parametrelerinin eşleşiyorsa, bu değerleri uygun şekilde rotadaki yerine kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2713b-152">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="2713b-153">Diğer eşleşmeyen değerleri İstek parametreleri üretilir.</span><span class="sxs-lookup"><span data-stu-id="2713b-153">The other non-matching values are generated as request parameters.</span></span>

## <a name="asp-fragment"></a><span data-ttu-id="2713b-154">ASP parçası</span><span class="sxs-lookup"><span data-stu-id="2713b-154">asp-fragment</span></span>

<span data-ttu-id="2713b-155">[Asp parça](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.fragment) özniteliği URL'si eklemek için URL parçası tanımlar.</span><span class="sxs-lookup"><span data-stu-id="2713b-155">The [asp-fragment](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.fragment) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="2713b-156">Yer işareti etiketi yardımcı karma karakteri ekler (#).</span><span class="sxs-lookup"><span data-stu-id="2713b-156">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="2713b-157">Aşağıdaki biçimlendirmede göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2713b-157">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="2713b-158">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-158">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="2713b-159">Karma etiketleri, istemci-tarafı uygulamaları oluştururken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="2713b-159">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="2713b-160">Bunlar, kolay işaretleme ve JavaScript'te, örneğin arama için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2713b-160">They can be used for easy marking and searching in JavaScript, for example.</span></span>

## <a name="asp-area"></a><span data-ttu-id="2713b-161">asp-area</span><span class="sxs-lookup"><span data-stu-id="2713b-161">asp-area</span></span>

<span data-ttu-id="2713b-162">[Asp alanı](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.area) öznitelik ayarlar uygun yol ayarlamak için kullanılan alan adı.</span><span class="sxs-lookup"><span data-stu-id="2713b-162">The [asp-area](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.area) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="2713b-163">Aşağıdaki örnek, alan özniteliği yeniden eşleme yolların nasıl neden gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="2713b-163">The following example depicts how the area attribute causes a remapping of routes.</span></span> <span data-ttu-id="2713b-164">Ayarı `asp-area` "Bloglara" dizin önekleri *alanları/blog* ilişkili denetleyicilerinin ve görünümlerin bu yer işareti etiketi için yollar.</span><span class="sxs-lookup"><span data-stu-id="2713b-164">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span>

* <span data-ttu-id="2713b-165">**< proje adı\>**</span><span class="sxs-lookup"><span data-stu-id="2713b-165">**<Project name\>**</span></span>
  * <span data-ttu-id="2713b-166">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="2713b-166">**wwwroot**</span></span>
  * <span data-ttu-id="2713b-167">**Alanlar**</span><span class="sxs-lookup"><span data-stu-id="2713b-167">**Areas**</span></span>
    * <span data-ttu-id="2713b-168">**Bloglar**</span><span class="sxs-lookup"><span data-stu-id="2713b-168">**Blogs**</span></span>
      * <span data-ttu-id="2713b-169">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="2713b-169">**Controllers**</span></span>
        * <span data-ttu-id="2713b-170">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="2713b-170">*HomeController.cs*</span></span>
      * <span data-ttu-id="2713b-171">**Görünümler**</span><span class="sxs-lookup"><span data-stu-id="2713b-171">**Views**</span></span>
        * <span data-ttu-id="2713b-172">**Giriş**</span><span class="sxs-lookup"><span data-stu-id="2713b-172">**Home**</span></span>
          * <span data-ttu-id="2713b-173">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2713b-173">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="2713b-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2713b-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="2713b-175">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2713b-175">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="2713b-176">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="2713b-176">**Controllers**</span></span>

<span data-ttu-id="2713b-177">Yukarıdaki dizin hiyerarşisinin başvurmak için biçimlendirme verilen *AboutBlog.cshtml* dosyasıdır:</span><span class="sxs-lookup"><span data-stu-id="2713b-177">Given the preceding directory hierarchy, the markup to reference the *AboutBlog.cshtml* file is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="2713b-178">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-178">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="2713b-179">Varsa bir MVC uygulamasında çalışmaya alanları için rota şablonu alanı için bir başvuru içermelidir.</span><span class="sxs-lookup"><span data-stu-id="2713b-179">For areas to work in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="2713b-180">Bu şablon ikinci parametre tarafından temsil edilen `routes.MapRoute` yöntem çağrısı *Startup.Configure*:[!code-csharp[](samples/TagHelpersBuiltInAspNetCore/Startup.cs?name=snippet_UseMvc&highlight=5)]</span><span class="sxs-lookup"><span data-stu-id="2713b-180">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*: [!code-csharp[](samples/TagHelpersBuiltInAspNetCore/Startup.cs?name=snippet_UseMvc&highlight=5)]</span></span>

## <a name="asp-protocol"></a><span data-ttu-id="2713b-181">ASP Protokolü</span><span class="sxs-lookup"><span data-stu-id="2713b-181">asp-protocol</span></span>

<span data-ttu-id="2713b-182">[Asp Protokolü](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.protocol) özniteliği olan bir protokolü belirtmek için (gibi `https`) URL'nizde.</span><span class="sxs-lookup"><span data-stu-id="2713b-182">The [asp-protocol](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.protocol) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="2713b-183">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2713b-183">For example:</span></span>

<span data-ttu-id="2713b-184">[!code-cshtml[samples/TagHelpersBuiltInAspNetCore/Views/Index.cshtml?name=snippet_AspProtocol]]</span><span class="sxs-lookup"><span data-stu-id="2713b-184">[!code-cshtml[samples/TagHelpersBuiltInAspNetCore/Views/Index.cshtml?name=snippet_AspProtocol]]</span></span>

<span data-ttu-id="2713b-185">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-185">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="2713b-186">Localhost ana bilgisayar adıdır örnekteki ancak yer işareti etiketi yardımcı Web sitesinin ortak etki alanı için URL oluşturulurken kullanır.</span><span class="sxs-lookup"><span data-stu-id="2713b-186">The host name in the example is localhost, but the Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

## <a name="asp-host"></a><span data-ttu-id="2713b-187">ASP ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="2713b-187">asp-host</span></span>

<span data-ttu-id="2713b-188">[Asp konak](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.host) özniteliktir, URL'de bir ana bilgisayar adını belirtmek için.</span><span class="sxs-lookup"><span data-stu-id="2713b-188">The [asp-host](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.host) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="2713b-189">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2713b-189">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="2713b-190">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-190">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

## <a name="asp-page"></a><span data-ttu-id="2713b-191">ASP sayfasının</span><span class="sxs-lookup"><span data-stu-id="2713b-191">asp-page</span></span>

<span data-ttu-id="2713b-192">[Asp sayfasının](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.page) özniteliği Razor sayfalarıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2713b-192">The [asp-page](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.page) attribute is used with Razor Pages.</span></span> <span data-ttu-id="2713b-193">Bir bağlantı etiketinin ayarlamak için kullanın `href` belirli bir sayfaya öznitelik değeri.</span><span class="sxs-lookup"><span data-stu-id="2713b-193">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="2713b-194">Sayfa adı bir eğik çizgi ("/") ile önek URL oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2713b-194">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="2713b-195">Aşağıdaki örnek, katılımcı Razor sayfasını noktaları:</span><span class="sxs-lookup"><span data-stu-id="2713b-195">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="2713b-196">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-196">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="2713b-197">`asp-page` Özniteliği ile birbirini dışlayan `asp-route`, `asp-controller`, ve `asp-action` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="2713b-197">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="2713b-198">Ancak, `asp-page` ile kullanılan `asp-route-{value}` yönlendirme, aşağıdaki biçimlendirme gösterdiği gibi denetlemek için:</span><span class="sxs-lookup"><span data-stu-id="2713b-198">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="2713b-199">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-199">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

## <a name="asp-page-handler"></a><span data-ttu-id="2713b-200">ASP sayfası işleyicisi</span><span class="sxs-lookup"><span data-stu-id="2713b-200">asp-page-handler</span></span>

<span data-ttu-id="2713b-201">[Asp sayfası işleyici](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.pagehandler) özniteliği Razor sayfalarıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2713b-201">The [asp-page-handler](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.anchortaghelper.pagehandler) attribute is used with Razor Pages.</span></span> <span data-ttu-id="2713b-202">Belirli bir sayfaya işleyicilerine bağlama için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2713b-202">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="2713b-203">Aşağıdaki sayfayı işleyicisini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="2713b-203">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltInAspNetCore/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="2713b-204">Sayfa modeli biçimlendirme bağlantılar ilişkili `OnGetProfile` sayfası işleyicisi.</span><span class="sxs-lookup"><span data-stu-id="2713b-204">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="2713b-205">Unutmayın `On<Verb>` sayfa işleyici yöntemi adı öneki atlanırsa `asp-page-handler` öznitelik değeri.</span><span class="sxs-lookup"><span data-stu-id="2713b-205">Note that the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="2713b-206">Bu zaman uyumsuz bir yöntem olsaydı `Async` soneki etmeyebilirsiniz çok.</span><span class="sxs-lookup"><span data-stu-id="2713b-206">If this were an asynchronous method, the `Async` suffix would be omitted too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltInAspNetCore/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="2713b-207">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="2713b-207">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="2713b-208">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2713b-208">Additional resources</span></span>

* [<span data-ttu-id="2713b-209">Alanlar</span><span class="sxs-lookup"><span data-stu-id="2713b-209">Areas</span></span>](xref:mvc/controllers/areas)
* [<span data-ttu-id="2713b-210">Razor sayfalarının giriş</span><span class="sxs-lookup"><span data-stu-id="2713b-210">Intro to Razor Pages</span></span>](xref:mvc/razor-pages/index)
