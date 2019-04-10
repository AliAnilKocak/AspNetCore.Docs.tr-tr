---
title: ASP.NET core'da kısmi etiket Yardımcısı
author: scottaddie
description: ASP.NET Core kısmi etiket Yardımcısı ve her biri özniteliklerini play kısmi bir görünümü işlemeye içinde rol keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 116fce7af5dc138fbbb0351a4f38f59e88c8f338
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468688"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="95af9-103">ASP.NET core'da kısmi etiket Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="95af9-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="95af9-104">Tarafından [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="95af9-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="95af9-105">Etiket Yardımcıları genel bakış için bkz. <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="95af9-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="95af9-106">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="95af9-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="95af9-107">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="95af9-107">Overview</span></span>

<span data-ttu-id="95af9-108">Kısmi etiket Yardımcısı işleme için kullanılan bir [kısmi Görünüm](xref:mvc/views/partial) Razor sayfaları ve MVC uygulamalarında.</span><span class="sxs-lookup"><span data-stu-id="95af9-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="95af9-109">BT'nin göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="95af9-109">Consider that it:</span></span>

* <span data-ttu-id="95af9-110">ASP.NET Core 2.1 veya üzerini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="95af9-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="95af9-111">Alternatif [HTML Yardımcısı söz dizimi](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="95af9-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="95af9-112">Kısmi görünüm zaman uyumsuz olarak işler.</span><span class="sxs-lookup"><span data-stu-id="95af9-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="95af9-113">Kısmi bir görünümü işlemeye yönelik HTML Yardımcısı seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="95af9-113">The HTML Helper options for rendering a partial view include:</span></span>

* [<span data-ttu-id="95af9-114">@await Html.PartialAsync</span><span class="sxs-lookup"><span data-stu-id="95af9-114">@await Html.PartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [<span data-ttu-id="95af9-115">@await Html.RenderPartialAsync</span><span class="sxs-lookup"><span data-stu-id="95af9-115">@await Html.RenderPartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [@Html.Partial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [@Html.RenderPartial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="95af9-116">*Ürün* modeli, bu belge boyunca örnekleri kullanılır:</span><span class="sxs-lookup"><span data-stu-id="95af9-116">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="95af9-117">Kısmi etiket Yardımcısı öznitelikleri envanterini izler.</span><span class="sxs-lookup"><span data-stu-id="95af9-117">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="95af9-118">name</span><span class="sxs-lookup"><span data-stu-id="95af9-118">name</span></span>

<span data-ttu-id="95af9-119">`name` Özniteliği gereklidir.</span><span class="sxs-lookup"><span data-stu-id="95af9-119">The `name` attribute is required.</span></span> <span data-ttu-id="95af9-120">Bu, ad veya işlenecek kısmi görünümün yolunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="95af9-120">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="95af9-121">Kısmi görünümün adı sağlandığında [görünümü bulma](xref:mvc/views/overview#view-discovery) işlemi başlatıldı.</span><span class="sxs-lookup"><span data-stu-id="95af9-121">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="95af9-122">Bu işlem, açık bir yol sağlandığında atlanır.</span><span class="sxs-lookup"><span data-stu-id="95af9-122">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="95af9-123">Tümü için kabul edilebilir `name` değerleri, görmek [kısmi görünüm bulma](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="95af9-123">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="95af9-124">Aşağıdaki biçimlendirmede gösteren özel bir yol kullanır *_ProductPartial.cshtml* yüklenmesini olan *paylaşılan* klasör.</span><span class="sxs-lookup"><span data-stu-id="95af9-124">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="95af9-125">Kullanarak [için](#for) öznitelik, bir model geçirilir kısmi görünüm için bağlama.</span><span class="sxs-lookup"><span data-stu-id="95af9-125">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="95af9-126">for</span><span class="sxs-lookup"><span data-stu-id="95af9-126">for</span></span>

<span data-ttu-id="95af9-127">`for` Atar özniteliği bir [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) geçerli modeline göre değerlendirilecek.</span><span class="sxs-lookup"><span data-stu-id="95af9-127">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="95af9-128">A `ModelExpression` çıkarsar `@Model.` söz dizimi.</span><span class="sxs-lookup"><span data-stu-id="95af9-128">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="95af9-129">Örneğin, `for="Product"` yerine kullanılan `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="95af9-129">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="95af9-130">Bu varsayılan kesmesi davranışını kullanarak geçersiz kılındı `@` simge bir satır içi ifadesi tanımlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="95af9-130">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="95af9-131">Aşağıdaki biçimlendirmede yükler *_ProductPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="95af9-131">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="95af9-132">Kısmi görünüm ilişkili sayfa modeli için bağlı `Product` özelliği:</span><span class="sxs-lookup"><span data-stu-id="95af9-132">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="95af9-133">model</span><span class="sxs-lookup"><span data-stu-id="95af9-133">model</span></span>

<span data-ttu-id="95af9-134">`model` Özniteliği kısmi görünüme iletmek için bir model örneği atar.</span><span class="sxs-lookup"><span data-stu-id="95af9-134">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="95af9-135">`model` Özniteliği ile kullanılamaz [için](#for) özniteliği.</span><span class="sxs-lookup"><span data-stu-id="95af9-135">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="95af9-136">Aşağıdaki biçimlendirmede yeni `Product` nesne örneği ve geçirilen `model` bağlama için öznitelik:</span><span class="sxs-lookup"><span data-stu-id="95af9-136">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="95af9-137">verileri görüntüle</span><span class="sxs-lookup"><span data-stu-id="95af9-137">view-data</span></span>

<span data-ttu-id="95af9-138">`view-data` Atar özniteliği bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) kısmi görünüme iletmek için.</span><span class="sxs-lookup"><span data-stu-id="95af9-138">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="95af9-139">Aşağıdaki biçimlendirmede ViewData koleksiyonunun tamamını kısmi görünüm için erişilebilir hale getirir:</span><span class="sxs-lookup"><span data-stu-id="95af9-139">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="95af9-140">Önceki kodda, `IsNumberReadOnly` anahtar değeri ayarı `true` ve ViewData koleksiyona eklenir.</span><span class="sxs-lookup"><span data-stu-id="95af9-140">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="95af9-141">Sonuç olarak, `ViewData["IsNumberReadOnly"]` aşağıdaki kısmi görünümü içinde erişilebilir hale getirdik:</span><span class="sxs-lookup"><span data-stu-id="95af9-141">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="95af9-142">Bu örnekte, değerini `ViewData["IsNumberReadOnly"]` belirler olmadığını *numarası* alanı salt okunur olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="95af9-142">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="95af9-143">Bir HTML Yardımcısı geçiş</span><span class="sxs-lookup"><span data-stu-id="95af9-143">Migrate from an HTML Helper</span></span>

<span data-ttu-id="95af9-144">Aşağıdaki zaman uyumsuz HTML Yardımcısı örneği göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="95af9-144">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="95af9-145">Ürünleri koleksiyonunu yinelenir ve görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="95af9-145">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="95af9-146">Başına `PartialAsync` yöntemin ilk parametresi, *_ProductPartial.cshtml* kısmi görünüm yüklenir.</span><span class="sxs-lookup"><span data-stu-id="95af9-146">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="95af9-147">Örneği `Product` model bağlama için kısmi görünüme iletilir.</span><span class="sxs-lookup"><span data-stu-id="95af9-147">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="95af9-148">Aynı zaman uyumsuz işleme davranışı aşağıdaki kısmi etiket Yardımcısı başarır `PartialAsync` HTML Yardımcısı.</span><span class="sxs-lookup"><span data-stu-id="95af9-148">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="95af9-149">`model` Öznitelik atanmış bir `Product` bağlama kısmi görünüm için model örneği.</span><span class="sxs-lookup"><span data-stu-id="95af9-149">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="95af9-150">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="95af9-150">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
