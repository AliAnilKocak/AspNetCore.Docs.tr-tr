---
title: ASP.NET Core düzen
author: ardalis
description: Bir ASP.NET Core uygulamasında görünümler işlemeden önce ortak düzenleri kullanmayı, yönergeleri paylaşmayı ve ortak kodu çalıştırmayı öğrenin.
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: 9a7b8003b24329f6e9cbd349ee47f6844b7c5f6d
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238037"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="0d902-103">ASP.NET Core düzen</span><span class="sxs-lookup"><span data-stu-id="0d902-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="0d902-104">[Steve Smith](https://ardalis.com/) ve [bave Brock](https://twitter.com/daveabrock) tarafından</span><span class="sxs-lookup"><span data-stu-id="0d902-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="0d902-105">Sayfalar ve görünümler genellikle görsel ve programlı öğeleri paylaşır.</span><span class="sxs-lookup"><span data-stu-id="0d902-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="0d902-106">Bu makalede nasıl yapılacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0d902-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="0d902-107">Ortak düzenleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d902-107">Use common layouts.</span></span>
* <span data-ttu-id="0d902-108">Komutları paylaşma.</span><span class="sxs-lookup"><span data-stu-id="0d902-108">Share directives.</span></span>
* <span data-ttu-id="0d902-109">Sayfaları veya görünümleri işlemeden önce ortak kodu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0d902-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="0d902-110">Bu belgede, ASP.NET Core MVC 'nin iki farklı yaklaşımının düzenleri ele alınmaktadır: Görünümler içeren Razor Pages ve denetleyiciler.</span><span class="sxs-lookup"><span data-stu-id="0d902-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="0d902-111">Bu konu için, farklar en az:</span><span class="sxs-lookup"><span data-stu-id="0d902-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="0d902-112">Razor Pages, *Sayfalar* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="0d902-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="0d902-113">Görünümleri olan denetleyiciler görünümler için bir *Görünümler* klasörü kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d902-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="0d902-114">Düzen nedir?</span><span class="sxs-lookup"><span data-stu-id="0d902-114">What is a Layout</span></span>

<span data-ttu-id="0d902-115">Çoğu Web uygulaması, bir sayfadan sayfaya gezindikleri sürece kullanıcıya tutarlı bir deneyim sağlayan ortak bir düzene sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0d902-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="0d902-116">Düzen genellikle uygulama üstbilgisi, gezinti veya menü öğeleri ve alt bilgi gibi ortak kullanıcı arabirimi öğelerini içerir.</span><span class="sxs-lookup"><span data-stu-id="0d902-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Sayfa düzeni örneği](layout/_static/page-layout.png)

<span data-ttu-id="0d902-118">Betikler ve stil sayfaları gibi ortak HTML yapıları de bir uygulama içindeki birçok sayfa tarafından sık kullanılır.</span><span class="sxs-lookup"><span data-stu-id="0d902-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="0d902-119">Bu paylaşılan öğelerin tümü, bir *Düzen* dosyasında tanımlanabilir ve bu daha sonra uygulama içinde kullanılan herhangi bir görünüm tarafından başvurulabilirler.</span><span class="sxs-lookup"><span data-stu-id="0d902-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="0d902-120">Düzenler görünümlerde yinelenen kodu azaltır.</span><span class="sxs-lookup"><span data-stu-id="0d902-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="0d902-121">Kurala göre, bir ASP.NET Core uygulamasının varsayılan düzeni *_Layout. cshtml*olarak adlandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="0d902-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="0d902-122">Şablonlarla oluşturulan yeni ASP.NET Core projelerine yönelik düzen dosyaları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0d902-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="0d902-123">Razor Pages: *Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="0d902-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Çözüm Gezgini sayfa klasörü](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="0d902-125">Görünümler içeren denetleyici: *Görünümler/paylaşılan/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="0d902-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Çözüm Gezgini içindeki görünümler klasörü](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="0d902-127">Düzen, uygulamadaki görünümler için üst düzey bir şablon tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0d902-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="0d902-128">Uygulamalar bir düzen gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="0d902-128">Apps don't require a layout.</span></span> <span data-ttu-id="0d902-129">Uygulamalar, farklı düzenleri belirleyen farklı görünümlerle birden fazla düzen tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="0d902-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="0d902-130">Aşağıdaki kod, bir şablon tarafından oluşturulan ve bir denetleyici ve görünümleri olan bir proje için Düzen dosyasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="0d902-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="0d902-131">Düzen belirtme</span><span class="sxs-lookup"><span data-stu-id="0d902-131">Specifying a Layout</span></span>

<span data-ttu-id="0d902-132">Razor görünümlerinin bir `Layout` özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="0d902-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="0d902-133">Bireysel görünümler bu özelliği ayarlayarak bir düzen belirtir:</span><span class="sxs-lookup"><span data-stu-id="0d902-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="0d902-134">Belirtilen Düzen tam yol kullanabilir (örneğin, */Pages/Shared/_Layout.exe* veya */views/Shared/_Layout.exe*) veya kısmi bir ad (örnek: `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="0d902-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="0d902-135">Kısmi bir ad sağlandığında, Razor görüntüleme altyapısı, kendi standart bulma işlemini kullanarak düzen dosyasını arar.</span><span class="sxs-lookup"><span data-stu-id="0d902-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="0d902-136">Önce işleyici yönteminin (veya denetleyicinin) bulunduğu klasör, sonra *paylaşılan* klasör tarafından aranır.</span><span class="sxs-lookup"><span data-stu-id="0d902-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="0d902-137">Bu bulma işlemi, [kısmi görünümleri](xref:mvc/views/partial#partial-view-discovery)bulmak için kullanılan işlemle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="0d902-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="0d902-138">Varsayılan olarak, tüm mizanpajın çağırması `RenderBody`gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d902-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="0d902-139">Çağrısının `RenderBody` yerleştirildiği her yerde, görünümün içerikleri işlenir.</span><span class="sxs-lookup"><span data-stu-id="0d902-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="0d902-140">Bölümler</span><span class="sxs-lookup"><span data-stu-id="0d902-140">Sections</span></span>

<span data-ttu-id="0d902-141">Bir düzen, çağırarak `RenderSection`, isteğe bağlı olarak bir veya daha fazla *bölüme*başvurabilir.</span><span class="sxs-lookup"><span data-stu-id="0d902-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="0d902-142">Bölümler, belirli sayfa öğelerinin yerleştirilmesi gereken yerleri düzenlemek için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d902-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="0d902-143">Her çağrısı `RenderSection` , bu bölümün gerekli veya isteğe bağlı olup olmadığını belirtebilir:</span><span class="sxs-lookup"><span data-stu-id="0d902-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

<span data-ttu-id="0d902-144">Gerekli bir bölüm bulunamazsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0d902-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="0d902-145">Tek görünümler, `@section` Razor söz dizimi kullanarak bir bölüm içinde işlenecek içeriği belirtir.</span><span class="sxs-lookup"><span data-stu-id="0d902-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="0d902-146">Bir sayfa veya görünüm bir bölümü tanımlıyorsa, oluşturulması gerekir (veya bir hata oluşur).</span><span class="sxs-lookup"><span data-stu-id="0d902-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="0d902-147">Razor Pages görünümünde `@section` örnek tanım:</span><span class="sxs-lookup"><span data-stu-id="0d902-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

<span data-ttu-id="0d902-148">Yukarıdaki kodda *betikler/Main. js* , bir sayfa veya görünümdeki `scripts` bölümüne eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d902-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="0d902-149">Aynı uygulamadaki diğer sayfalar veya görünümler bu betiği gerektirmeyebilir ve betikler bölümü tanımlamaz.</span><span class="sxs-lookup"><span data-stu-id="0d902-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="0d902-150">Aşağıdaki biçimlendirme, *_Validationscriptspartial. cshtml*Işlemek Için [kısmi etiket yardımcısını](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) kullanır:</span><span class="sxs-lookup"><span data-stu-id="0d902-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="0d902-151">Önceki biçimlendirme, [Yapı Iskelesi kimliği](xref:security/authentication/scaffold-identity)tarafından oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="0d902-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="0d902-152">Bir sayfada veya görünümde tanımlanan bölümler yalnızca kendi düzen sayfasında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d902-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="0d902-153">Parçalardan başvurulamaz, bileşenleri veya görünüm sisteminin diğer kısımlarını bunlara başvuramaz.</span><span class="sxs-lookup"><span data-stu-id="0d902-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="0d902-154">Bölümler yoksayılıyor</span><span class="sxs-lookup"><span data-stu-id="0d902-154">Ignoring sections</span></span>

<span data-ttu-id="0d902-155">Varsayılan olarak, içerik sayfasındaki gövde ve tüm bölümler Düzen sayfası tarafından işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="0d902-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="0d902-156">Razor görünümü altyapısı, gövdenin ve her bölümün işlenip işlenmeyeceğini izleyerek bunu zorlar.</span><span class="sxs-lookup"><span data-stu-id="0d902-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="0d902-157">Görünüm altyapısına gövde veya bölümleri yok saymasını bildirmek için `IgnoreBody` ve `IgnoreSection` yöntemlerini çağırın.</span><span class="sxs-lookup"><span data-stu-id="0d902-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="0d902-158">Bir Razor sayfasındaki gövde ve her bölüm işlenen ya da yoksayıldı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0d902-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="0d902-159">Paylaşılan yönergeler içeri aktarılıyor</span><span class="sxs-lookup"><span data-stu-id="0d902-159">Importing Shared Directives</span></span>

<span data-ttu-id="0d902-160">Görünümler ve sayfalar, ad alanlarını içeri aktarmak ve [bağımlılık ekleme](dependency-injection.md)'yi kullanmak için Razor yönergeleri kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="0d902-160">Views and pages can use Razor directives to importing namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="0d902-161">Birçok görünüm tarafından paylaşılan yönergeler, ortak bir *_Viewwimports. cshtml* dosyasında belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="0d902-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0d902-162">`_ViewImports` Dosya aşağıdaki yönergeleri destekler:</span><span class="sxs-lookup"><span data-stu-id="0d902-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="0d902-163">Dosya, işlevler ve bölüm tanımları gibi diğer Razor özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="0d902-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="0d902-164">Örnek `_ViewImports.cshtml` dosya:</span><span class="sxs-lookup"><span data-stu-id="0d902-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="0d902-165">ASP.NET Core MVC uygulaması için *_Viewwimports. cshtml* dosyası genellikle *Sayfalar* (veya *Görünümler*) klasörüne yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0d902-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="0d902-166">Bir *_Viewwimports. cshtml* dosyası herhangi bir klasöre yerleştirilebilir, bu durumda yalnızca bu klasör ve alt klasörleri içindeki sayfalara veya görünümlere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0d902-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="0d902-167">`_ViewImports`dosyalar, kök düzeyinden başlayarak işlenir ve sonra her bir klasör için sayfanın konumu veya görünümü görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0d902-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="0d902-168">`_ViewImports`kök düzeyinde belirtilen ayarlar klasör düzeyinde geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="0d902-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="0d902-169">Örneğin, şunu varsayın:</span><span class="sxs-lookup"><span data-stu-id="0d902-169">For example, suppose:</span></span>

* <span data-ttu-id="0d902-170">Kök düzeyi *_viewwimports. cshtml* dosyası ve `@addTagHelper *, MyTagHelper1`içerir `@model MyModel1` .</span><span class="sxs-lookup"><span data-stu-id="0d902-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="0d902-171">Alt klasör *_viewwimports. cshtml* dosyası ve `@model MyModel2` `@addTagHelper *, MyTagHelper2`içerir.</span><span class="sxs-lookup"><span data-stu-id="0d902-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="0d902-172">Alt klasördeki sayfaların ve görünümlerin her ikisi de etiket yardımcılarını ve `MyModel2` modeline erişimi olur.</span><span class="sxs-lookup"><span data-stu-id="0d902-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="0d902-173">Dosya hiyerarşisinde birden çok *_Viewimports. cshtml* dosyası bulunursa, yönergelerin birleştirilmiş davranışı şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0d902-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="0d902-174">`@addTagHelper`, `@removeTagHelper`: tüm çalıştırma, sırasıyla</span><span class="sxs-lookup"><span data-stu-id="0d902-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="0d902-175">`@tagHelperPrefix`: görünümün en yakın olanı, diğerlerini geçersiz kılar</span><span class="sxs-lookup"><span data-stu-id="0d902-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="0d902-176">`@model`: görünümün en yakın olanı, diğerlerini geçersiz kılar</span><span class="sxs-lookup"><span data-stu-id="0d902-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="0d902-177">`@inherits`: görünümün en yakın olanı, diğerlerini geçersiz kılar</span><span class="sxs-lookup"><span data-stu-id="0d902-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="0d902-178">`@using`: tümü dahildir; yinelemeler yoksayıldı</span><span class="sxs-lookup"><span data-stu-id="0d902-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="0d902-179">`@inject`: her bir özellik için, görünümün en yakın olanı aynı özellik adına sahip diğer diğerlerini geçersiz kılar</span><span class="sxs-lookup"><span data-stu-id="0d902-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="0d902-180">Her görünümden önce kod çalıştırma</span><span class="sxs-lookup"><span data-stu-id="0d902-180">Running Code Before Each View</span></span>

<span data-ttu-id="0d902-181">Her görünüm veya sayfadan önce çalıştırılması gereken kodun *_Viewstart. cshtml* dosyasına yerleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d902-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="0d902-182">Kural gereği, *_Viewstart. cshtml* dosyası *Sayfalar* (veya *Görünümler*) klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="0d902-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="0d902-183">*_Viewstart. cshtml* dosyasında listelenen deyimler her tam görünüm (düzen değil ve kısmi görünümler değil) öncesinde çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="0d902-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="0d902-184">[Viewwimports. cshtml](xref:mvc/views/layout#viewimports)gibi, *_viewstart. cshtml* ise hiyerarşik bir görünüm olur.</span><span class="sxs-lookup"><span data-stu-id="0d902-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="0d902-185">Görünüm veya sayfalar klasöründe bir *_Viewstart. cshtml* dosyası tanımlanmışsa, *Sayfalar* (veya *Görünümler*) klasörünün kökünde (varsa) tanımlandıktan sonra çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="0d902-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="0d902-186">Örnek bir *_Viewstart. cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="0d902-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="0d902-187">Yukarıdaki dosya tüm görünümlerin *_Layout. cshtml* mizanpajını kullanacağı belirtir.</span><span class="sxs-lookup"><span data-stu-id="0d902-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="0d902-188">*_Viewstart. cshtml* ve *_Viewwimports. cshtml* genellikle */Pages/Shared* (veya */views/Shared*) klasörüne **yerleştirilmez.**</span><span class="sxs-lookup"><span data-stu-id="0d902-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="0d902-189">Bu dosyaların uygulama düzeyi sürümleri doğrudan */Pages* (veya */views*) klasörüne yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="0d902-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
