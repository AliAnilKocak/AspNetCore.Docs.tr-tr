---
title: ASP.NET core'da yer işareti etiketi Yardımcısı
author: pkellner
description: ASP.NET Core yer işareti etiketi Yardımcısı öznitelikleri ve her bir öznitelik HTML yer işareti etiketi davranışını genişletmek oynadığı rolü keşfedin.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/18/2019
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: de45c99194d4825c1e404aa193b0f076ba659748
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898577"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="f1d0d-103">ASP.NET core'da yer işareti etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="f1d0d-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f1d0d-104">Tarafından [Peter Kellner](http://peterkellner.net) ve [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f1d0d-104">By [Peter Kellner](http://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="f1d0d-105">[Yer işareti etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) standart HTML tutturucusu geliştirir (`<a ... ></a>`) yeni özellikler ekleyerek etiketi.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="f1d0d-106">Kural gereği, öznitelik adları ile ön ekli `asp-`.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="f1d0d-107">İşlenen bağlantı öğenin `href` öznitelik değeri, değerleri tarafından belirlenir `asp-` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="f1d0d-108">Etiket Yardımcıları genel bakış için bkz. <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="f1d0d-109">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1d0d-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f1d0d-110">*SpeakerController* örnekleri bu belge boyunca kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="f1d0d-111">Yer işareti etiketi Yardımcısı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="f1d0d-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="f1d0d-112">ASP denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="f1d0d-112">asp-controller</span></span>

<span data-ttu-id="f1d0d-113">[Asp denetleyicisi](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) öznitelik, URL'yi oluşturmak için kullanılan denetleyici atar.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="f1d0d-114">Aşağıdaki biçimlendirmede tüm konuşmacılarını listeler:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="f1d0d-115">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="f1d0d-116">Varsa `asp-controller` özniteliği belirtilirse ve `asp-action` değil, varsayılan `asp-action` yürütülmekte görünüm ile ilişkilendirilen denetleyici eylemi bir değerdir.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="f1d0d-117">Varsa `asp-action` önceki biçimlendirmeden atlanır ve yer işareti etiketi Yardımcısı kullanılır *HomeController*'s *dizin* görünümü (*/Home*), oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

## <a name="asp-action"></a><span data-ttu-id="f1d0d-118">ASP eylemi</span><span class="sxs-lookup"><span data-stu-id="f1d0d-118">asp-action</span></span>

<span data-ttu-id="f1d0d-119">[Asp eylem](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) öznitelik değeri temsil eden oluşturulmuş dahil denetleyici eylem adı `href` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="f1d0d-120">Aşağıdaki biçimlendirmede oluşturulan ayarlar `href` Konuşmacı değerlendirmeleri sayfasına öznitelik değeri:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="f1d0d-121">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="f1d0d-122">Hayır ise `asp-controller` özniteliği belirtilmezse, geçerli görünüm yürütme görünümü çağırma varsayılan denetleyicisi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="f1d0d-123">Varsa `asp-action` öznitelik değeri `Index`, hiçbir eylem için varsayılan çağırma giden URL, eklenecek sonra `Index` eylem.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="f1d0d-124">Eylem belirtilen (veya varsayılan), başvurulan denetleyicisindeki bulunmalıdır `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="f1d0d-125">ASP - route-{value}</span><span class="sxs-lookup"><span data-stu-id="f1d0d-125">asp-route-{value}</span></span>

<span data-ttu-id="f1d0d-126">[Asp - route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) öznitelik joker karakter rota öneki sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="f1d0d-127">Herhangi bir değer kaplayan `{value}` yer tutucusu, olası bir rota parametresini yorumlanır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="f1d0d-128">Varsayılan bir yol bulunmazsa, bu rota öneki eklenir oluşturulan `href` bir istek parametresi ve değeri olarak özniteliği.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="f1d0d-129">Aksi takdirde, bu rota şablonu konur.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="f1d0d-130">Şu denetleyici eylemi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="f1d0d-131">İçinde tanımlanan varsayılan rota şablonuyla *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="f1d0d-132">MVC görünümü gibi bir eylem tarafından sağlanan bir model kullanır:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-132">The MVC view uses the model, provided by the action, as follows:</span></span>

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

<span data-ttu-id="f1d0d-133">Varsayılan yolun `{id?}` yer tutucu eşleşti.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="f1d0d-134">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="f1d0d-135">Rota öneki eşleştirme yönlendirme şablonunun parçası olmadığından aşağıdaki MVC görünümü olarak kabul edin:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

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

<span data-ttu-id="f1d0d-136">Aşağıdaki HTML'yi çünkü oluşturulan `speakerid` eşleşen yolda bulunamadı:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="f1d0d-137">Ya da `asp-controller` veya `asp-action` belirtilmemişse, sonra aynı varsayılan işlem olduğundan ardından `asp-route` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="f1d0d-138">ASP yol</span><span class="sxs-lookup"><span data-stu-id="f1d0d-138">asp-route</span></span>

<span data-ttu-id="f1d0d-139">[Asp rota](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) öznitelik, URL'yi doğrudan adlandırılmış bir rotayı bağlama oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="f1d0d-140">Kullanarak [yönlendirme öznitelikleri](xref:mvc/controllers/routing#attribute-routing), gösterildiği gibi bir yol adlandırılabilir `SpeakerController` ve kullanılan kendi `Evaluations` eylem:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="f1d0d-141">Aşağıdaki biçimlendirmede `asp-route` öznitelik adlandırılmış rota başvuruyor:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="f1d0d-142">Yer işareti etiketi Yardımcısı doğrudan URL kullanılarak bu denetleyici eylemi bir yol oluşturur */Konuşmacı/değerlendirmeleri*.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="f1d0d-143">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="f1d0d-144">Varsa `asp-controller` veya `asp-action` ek olarak belirtilen `asp-route`, oluşturulan rota beklediğiniz olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="f1d0d-145">Bir rota çakışmayı önlemek için `asp-route` ile kullanılmaması `asp-controller` ve `asp-action` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

## <a name="asp-all-route-data"></a><span data-ttu-id="f1d0d-146">ASP tüm rota veri</span><span class="sxs-lookup"><span data-stu-id="f1d0d-146">asp-all-route-data</span></span>

<span data-ttu-id="f1d0d-147">[Tüm rota veri asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) özniteliği bir anahtar-değer çiftlerinin dictionary'si oluşturulmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="f1d0d-148">Anahtarı parametre adı ve değerin parametre değeri olduğu.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="f1d0d-149">Aşağıdaki örnekte, bir sözlük başlatılır ve bir Razor görünüme geçirildi.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="f1d0d-150">Alternatif olarak, veri modelinizi oturum geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="f1d0d-151">Yukarıdaki kod, aşağıdaki HTML'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="f1d0d-152">`asp-all-route-data` Sözlük Aşırı yüklenen gereksinimlerini karşılayan bir sorgu dizesi üretmek için düzleştirilir `Evaluations` eylem:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="f1d0d-153">Sözlükteki tüm anahtarları rota parametrelerinin eşleşiyorsa, bu değerleri uygun şekilde rotadaki yerine kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="f1d0d-154">Eşleşmeyen diğer değerleri, istek parametreleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="f1d0d-155">ASP parçası</span><span class="sxs-lookup"><span data-stu-id="f1d0d-155">asp-fragment</span></span>

<span data-ttu-id="f1d0d-156">[Asp parça](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) özniteliği URL'ye için URL parçası belirtmesini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="f1d0d-157">Yer işareti etiketi Yardımcısı karma karakteri ekler (#).</span><span class="sxs-lookup"><span data-stu-id="f1d0d-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="f1d0d-158">Aşağıdaki biçimlendirmede göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="f1d0d-159">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="f1d0d-160">Karma etiketleri, istemci tarafı uygulamalar oluştururken yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="f1d0d-161">Bunlar, kolay işaretleme ve JavaScript'te, örneğin arama için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="f1d0d-162">ASP alanı</span><span class="sxs-lookup"><span data-stu-id="f1d0d-162">asp-area</span></span>

<span data-ttu-id="f1d0d-163">[Asp alan](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) özniteliği uygun bir yol ayarlamak için kullanılan alan adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="f1d0d-164">Aşağıdaki örnekler tarif nasıl `asp-area` özniteliği, yeniden eşleme yollarını neden olur.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

### <a name="usage-in-razor-pages"></a><span data-ttu-id="f1d0d-165">Razor sayfaları kullanımı</span><span class="sxs-lookup"><span data-stu-id="f1d0d-165">Usage in Razor Pages</span></span>

<span data-ttu-id="f1d0d-166">Razor sayfaları alanları, ASP.NET Core 2.1 veya sonraki sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="f1d0d-167">Aşağıdaki dizin hiyerarşi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="f1d0d-168">**{} Proje adı**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-168">**{Project name}**</span></span>
  * <span data-ttu-id="f1d0d-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-169">**wwwroot**</span></span>
  * <span data-ttu-id="f1d0d-170">**Alanlar**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-170">**Areas**</span></span>
    * <span data-ttu-id="f1d0d-171">**Oturumları**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-171">**Sessions**</span></span>
      * <span data-ttu-id="f1d0d-172">**Sayfalar**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-172">**Pages**</span></span>
        * <span data-ttu-id="f1d0d-173">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1d0d-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="f1d0d-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1d0d-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="f1d0d-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1d0d-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="f1d0d-176">**Sayfalar**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-176">**Pages**</span></span>

<span data-ttu-id="f1d0d-177">Başvurmak için biçimlendirmeyi *oturumları* alan *dizin* Razor sayfası:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="f1d0d-178">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="f1d0d-179">Razor sayfaları uygulamada alanlarını desteklemek için aşağıdakilerden birini yapın `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="f1d0d-180">Ayarlama [uyumluluk sürümü](xref:mvc/compatibility-version) 2.1 veya üzeri.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="f1d0d-181">Ayarlama [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) özelliğini `true`:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

### <a name="usage-in-mvc"></a><span data-ttu-id="f1d0d-182">MVC kullanımı</span><span class="sxs-lookup"><span data-stu-id="f1d0d-182">Usage in MVC</span></span>

<span data-ttu-id="f1d0d-183">Aşağıdaki dizin hiyerarşi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="f1d0d-184">**{} Proje adı**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-184">**{Project name}**</span></span>
  * <span data-ttu-id="f1d0d-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-185">**wwwroot**</span></span>
  * <span data-ttu-id="f1d0d-186">**Alanlar**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-186">**Areas**</span></span>
    * <span data-ttu-id="f1d0d-187">**Bloglar**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-187">**Blogs**</span></span>
      * <span data-ttu-id="f1d0d-188">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-188">**Controllers**</span></span>
        * <span data-ttu-id="f1d0d-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="f1d0d-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="f1d0d-190">**Görünümler**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-190">**Views**</span></span>
        * <span data-ttu-id="f1d0d-191">**Giriş**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-191">**Home**</span></span>
          * <span data-ttu-id="f1d0d-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1d0d-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="f1d0d-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1d0d-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="f1d0d-194">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1d0d-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="f1d0d-195">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="f1d0d-195">**Controllers**</span></span>

<span data-ttu-id="f1d0d-196">Ayarı `asp-area` "Bloglarda" dizin ön ekleri *alanlar/Blog'lar* ilişkili denetleyicileri ve görünümlerinin bu yer işareti etiketi için yollar.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="f1d0d-197">Başvurmak için biçimlendirmeyi *AboutBlog* görünümü:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="f1d0d-198">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="f1d0d-199">Bir MVC uygulamasında alanlarını desteklemek için rota şablonu varsa, alan başvuru içermelidir.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="f1d0d-200">Bu şablon ikinci parametre tarafından temsil edilen `routes.MapRoute` yöntem çağrısı *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="f1d0d-201">ASP Protokolü</span><span class="sxs-lookup"><span data-stu-id="f1d0d-201">asp-protocol</span></span>

<span data-ttu-id="f1d0d-202">[Asp Protokolü](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) özniteliği, bir protokol belirtmek için (gibi `https`), URL.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="f1d0d-203">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="f1d0d-204">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="f1d0d-205">Ana bilgisayar adı örnekteki localhost'tur.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-205">The host name in the example is localhost.</span></span> <span data-ttu-id="f1d0d-206">Yer işareti etiketi Yardımcısı Web sitesinin genel etki alanı için URL oluşturulurken kullanır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="f1d0d-207">ASP konak</span><span class="sxs-lookup"><span data-stu-id="f1d0d-207">asp-host</span></span>

<span data-ttu-id="f1d0d-208">[Asp konak](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) özniteliği, bir ana bilgisayar adı, URL belirtmek için.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="f1d0d-209">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="f1d0d-210">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="f1d0d-211">ASP sayfası</span><span class="sxs-lookup"><span data-stu-id="f1d0d-211">asp-page</span></span>

<span data-ttu-id="f1d0d-212">[Asp sayfasının](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) özniteliği, Razor sayfaları ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="f1d0d-213">Bir yer işareti etiketin ayarlamak için kullanın `href` belirli bir sayfaya öznitelik değeri.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="f1d0d-214">Sayfanın adını bir eğik çizgi ("/"), URL oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="f1d0d-215">Aşağıdaki örnek, katılımcı Razor sayfası noktaları:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="f1d0d-216">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="f1d0d-217">`asp-page` Özniteliktir birbirini dışlayan `asp-route`, `asp-controller`, ve `asp-action` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="f1d0d-218">Ancak, `asp-page` kullanılabilir `asp-route-{value}` yönlendirme, aşağıdaki biçimlendirme gösterildiği gibi denetlemek için:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="f1d0d-219">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="f1d0d-220">ASP sayfası işleyicisi</span><span class="sxs-lookup"><span data-stu-id="f1d0d-220">asp-page-handler</span></span>

<span data-ttu-id="f1d0d-221">[Asp sayfasını işleyici](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) özniteliği, Razor sayfaları ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="f1d0d-222">Belirli bir sayfaya işleyicilerine bağlamak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="f1d0d-223">Aşağıdaki sayfayı işleyici göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="f1d0d-224">Sayfa modeli biçimlendirme bağlantılar ilişkili `OnGetProfile` sayfası işleyicisi.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="f1d0d-225">Not `On<Verb>` sayfa işleyicisi yöntem adı ön eki atlanırsa `asp-page-handler` öznitelik değeri.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="f1d0d-226">Yöntemi zaman uyumsuz olduğunda `Async` soneki atlanırsa, çok.</span><span class="sxs-lookup"><span data-stu-id="f1d0d-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="f1d0d-227">Oluşturulan HTML:</span><span class="sxs-lookup"><span data-stu-id="f1d0d-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="f1d0d-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f1d0d-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
