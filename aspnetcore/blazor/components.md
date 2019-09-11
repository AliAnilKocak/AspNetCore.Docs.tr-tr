---
title: ASP.NET Core Razor bileşenleri oluşturma ve kullanma
author: guardrex
description: Veri bağlama, olayları işleme ve bileşen yaşam döngülerini yönetme dahil Razor bileşenleri oluşturmayı ve kullanmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/components
ms.openlocfilehash: dbd0879d200061151e8307346adef784967bf123
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878402"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="61f46-103">ASP.NET Core Razor bileşenleri oluşturma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="61f46-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="61f46-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="61f46-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="61f46-105">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61f46-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="61f46-106">Blazor uygulamaları, *bileşenleri*kullanılarak oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="61f46-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="61f46-107">Bir bileşen, bir sayfa, iletişim veya form gibi bir kullanıcı arabirimi (UI) öbekidir.</span><span class="sxs-lookup"><span data-stu-id="61f46-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="61f46-108">Bir bileşen, veri eklemek veya UI olaylarına yanıt vermek için gereken HTML işaretlemesini ve işleme mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="61f46-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="61f46-109">Bileşenler esnek ve hafif.</span><span class="sxs-lookup"><span data-stu-id="61f46-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="61f46-110">Bunlar, iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="61f46-111">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="61f46-111">Component classes</span></span>

<span data-ttu-id="61f46-112">Bileşenler, C# ve HTML Işaretlemesi kullanılarak [Razor](xref:mvc/views/razor) bileşen dosyalarında ( *. Razor*) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="61f46-113">Blazor içindeki bir bileşen, bir *Razor bileşeni*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="61f46-114">Bir bileşenin adı, büyük harfle başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="61f46-115">Örneğin, *mycoolcomponent. Razor* geçerlidir ve *mycoolcomponent. Razor* geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="61f46-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="61f46-116">Dosyalar, `_RazorComponentInclude` MSBuild özelliği kullanılarak Razor bileşen dosyaları olarak tanımlandığı sürece *. cshtml* dosya uzantısı kullanılarak yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-116">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="61f46-117">Örneğin, *Sayfalar* klasörü altındaki tüm *. cshtml* dosyalarının Razor bileşenleri dosyası olarak değerlendirilip değerlendirilmeyeceğini belirten bir uygulama:</span><span class="sxs-lookup"><span data-stu-id="61f46-117">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

<span data-ttu-id="61f46-118">Bir bileşen için Kullanıcı arabirimi HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-118">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="61f46-119">Dinamik işleme mantığı (örneğin, döngüler, koşullar, ifadeler) C# [Razor](xref:mvc/views/razor)adlı gömülü bir sözdizimi kullanılarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-119">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="61f46-120">Bir uygulama derlendiğinde, HTML biçimlendirme ve C# işleme mantığı bir bileşen sınıfına dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="61f46-120">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="61f46-121">Oluşturulan sınıfın adı, dosyanın adıyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="61f46-121">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="61f46-122">Bileşen sınıfının üyeleri bir `@code` blokta tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-122">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="61f46-123">`@code` Bloğunda, bileşen durumu (özellikler, alanlar) olay işleme yöntemleriyle veya diğer bileşen mantığını tanımlamaya yönelik yöntemlerle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-123">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="61f46-124">Birden çok blok izin verilir. `@code`</span><span class="sxs-lookup"><span data-stu-id="61f46-124">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="61f46-125">ASP.NET Core 3,0 ' nin önceki önizlemelerinde `@functions` , bloklar Razor bileşenlerinde bulunan `@code` bloklarla aynı amaçla kullanılmıştı.</span><span class="sxs-lookup"><span data-stu-id="61f46-125">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="61f46-126">`@functions`bloklar Razor bileşenlerinde çalışmaya devam eder, ancak `@code` blok ASP.NET Core 3,0 Preview 6 veya sonraki bir sürümünde kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="61f46-126">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="61f46-127">Bileşen üyeleri, ile C# `@`başlayan ifadeleri kullanarak bileşenin işleme mantığının bir parçası olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-127">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="61f46-128">Örneğin, bir C# alan, alan adının önüne eklenerek `@` işlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-128">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="61f46-129">Aşağıdaki örnek değerlendirilir ve işler:</span><span class="sxs-lookup"><span data-stu-id="61f46-129">The following example evaluates and renders:</span></span>

* <span data-ttu-id="61f46-130">`_headingFontStyle`için CSS özellik değerine `font-style`.</span><span class="sxs-lookup"><span data-stu-id="61f46-130">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="61f46-131">`_headingText``<h1>` öğenin içeriğine.</span><span class="sxs-lookup"><span data-stu-id="61f46-131">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="61f46-132">Bileşen ilk olarak işlendikten sonra, bileşen işleme ağacını olaylara yanıt olarak yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="61f46-132">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="61f46-133">Blazor ardından yeni işleme ağacını önceki bir ile karşılaştırır ve tarayıcının Belge Nesne Modeli (DOM) üzerinde herhangi bir değişiklik uygular.</span><span class="sxs-lookup"><span data-stu-id="61f46-133">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="61f46-134">Bileşenler sıradan C# sınıflardır ve bir proje içinde herhangi bir yere yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-134">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="61f46-135">Web sayfalarını üreten bileşenler genellikle *Sayfalar* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="61f46-135">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="61f46-136">Sayfa olmayan bileşenler sıklıkla *paylaşılan* klasöre veya projeye eklenen özel bir klasöre yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-136">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="61f46-137">Özel bir klasör kullanmak için, özel klasörün ad alanını üst bileşene ya da uygulamanın *_ımports. Razor* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="61f46-137">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="61f46-138">Örneğin, aşağıdaki ad alanı, uygulamanın kök ad alanı `WebApplication`olduğunda bir bileşenler klasöründeki bileşenleri kullanılabilir yapar:</span><span class="sxs-lookup"><span data-stu-id="61f46-138">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="61f46-139">Bileşenleri Razor Pages ve MVC uygulamalarıyla tümleştirme</span><span class="sxs-lookup"><span data-stu-id="61f46-139">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="61f46-140">Mevcut Razor Pages ve MVC uygulamalarıyla bileşenleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-140">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="61f46-141">Razor bileşenleri kullanmak için mevcut sayfaları veya görünümleri yeniden yazmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-141">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="61f46-142">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden işlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-142">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="61f46-143">Bir sayfadan veya görünümden bir bileşeni işlemek için `RenderComponentAsync<TComponent>` HTML yardımcı yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="61f46-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="61f46-144">Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir.</span><span class="sxs-lookup"><span data-stu-id="61f46-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="61f46-145">Bileşenler, kısmi görünümler ve bölümler gibi görüntüleme ve sayfaya özgü senaryolar kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="61f46-146">Bir bileşende kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.</span><span class="sxs-lookup"><span data-stu-id="61f46-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="61f46-147">Bileşenlerin nasıl işlendiği ve bileşen durumunun Blazor sunucu tarafı uygulamalarda nasıl yönetildiği hakkında daha fazla bilgi için <xref:blazor/hosting-models> makalesine bakın.</span><span class="sxs-lookup"><span data-stu-id="61f46-147">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="61f46-148">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="61f46-148">Use components</span></span>

<span data-ttu-id="61f46-149">Bileşenler, HTML öğesi söz dizimini kullanarak bildirerek diğer bileşenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="61f46-150">Bir bileşeni kullanmak için biçimlendirme, etiket adının bileşen türü olduğu bir HTML etiketi gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="61f46-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="61f46-151">Öznitelik bağlama büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-151">Attribute binding is case sensitive.</span></span> <span data-ttu-id="61f46-152">Örneğin, `@bind` geçerlidir ve `@Bind` geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="61f46-152">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="61f46-153">*Index. Razor* dosyasında aşağıdaki biçimlendirme bir `HeadingComponent` örneği işler:</span><span class="sxs-lookup"><span data-stu-id="61f46-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="61f46-154">*Bileşenler/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="61f46-154">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="61f46-155">Bir bileşen bir bileşen adıyla eşleşmeyen büyük harfle yazılmış bir HTML öğesi içeriyorsa, öğenin beklenmeyen bir adı olduğunu gösteren bir uyarı yayınlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="61f46-156">Bileşenin ad `@using` alanı için bir deyimin eklenmesi, bileşenin kullanılabilir olmasını sağlar ve bu da uyarıyı kaldırır.</span><span class="sxs-lookup"><span data-stu-id="61f46-156">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="61f46-157">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="61f46-157">Component parameters</span></span>

<span data-ttu-id="61f46-158">Bileşenler, bileşen sınıfında `[Parameter]` özniteliği ile ortak özellikler kullanılarak tanımlanan *bileşen parametrelerine*sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-158">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="61f46-159">Biçimlendirme içindeki bir bileşenin bağımsız değişkenlerini belirtmek için öznitelikleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-159">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="61f46-160">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="61f46-160">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="61f46-161">Aşağıdaki örnekte `ParentComponent` , öğesinin `Title` özelliğinin değerini ayarlar. `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="61f46-161">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="61f46-162">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="61f46-162">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="61f46-163">Alt içerik</span><span class="sxs-lookup"><span data-stu-id="61f46-163">Child content</span></span>

<span data-ttu-id="61f46-164">Bileşenler, başka bir bileşenin içeriğini ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-164">Components can set the content of another component.</span></span> <span data-ttu-id="61f46-165">Atama bileşeni, alıcı bileşeni belirten Etiketler arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-165">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="61f46-166">Aşağıdaki örnekte `ChildComponent` , öğesinin işlemek için bir kullanıcı arabirimi segmentini temsil `RenderFragment`eden öğesini temsil eden bir `ChildContent` özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="61f46-166">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="61f46-167">Değeri `ChildContent` , bileşenin, içeriğin işlenmesi gereken biçimlendirmesinde konumlandırılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-167">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="61f46-168">Değeri `ChildContent` , ana bileşenden alınır ve önyükleme `panel-body`paneli içinde işlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-168">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="61f46-169">*Bileşenler/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="61f46-169">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="61f46-170">`RenderFragment` İçeriği alan özelliğin kural tarafından adlandırılması `ChildContent` gerekir.</span><span class="sxs-lookup"><span data-stu-id="61f46-170">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="61f46-171">Aşağıdakiler `ParentComponent` `<ChildComponent>` , `ChildComponent` içeriği etiketlerin içine yerleştirerek işleme için içerik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-171">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="61f46-172">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="61f46-172">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="61f46-173">Öznitelik döndürme ve rastgele parametreler</span><span class="sxs-lookup"><span data-stu-id="61f46-173">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="61f46-174">Bileşenler, bileşen tarafından tanımlanan parametrelere ek olarak ek öznitelikler yakalayabilir ve işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-174">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="61f46-175">Ek öznitelikler bir sözlükte yakalanıp *, daha sonra* bileşen [@attributes](xref:mvc/views/razor#attributes) Razor yönergesi kullanılarak işlendiğinde bir öğe üzerine bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-175">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="61f46-176">Bu senaryo, çeşitli özelleştirmeleri destekleyen bir işaretleme öğesi üreten bir bileşen tanımlarken yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-176">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="61f46-177">Örneğin, çok sayıda parametreyi destekleyen bir `<input>` için öznitelikleri ayrı olarak tanımlamak sıkıcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-177">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="61f46-178">Aşağıdaki `<input>` örnekte, ilk öğesi (`id="useIndividualParams"`) bağımsız bileşen parametrelerini kullanır, ancak ikinci `<input>` öğe (`id="useAttributesDict"`) öznitelik splatesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="61f46-178">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="61f46-179">Parametrenin türü dize anahtarlarıyla gerçekleştirmelidir `IEnumerable<KeyValuePair<string, object>>` .</span><span class="sxs-lookup"><span data-stu-id="61f46-179">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="61f46-180">Bu senaryoda ayrıca bir seçenek de vardır.`IReadOnlyDictionary<string, object>`</span><span class="sxs-lookup"><span data-stu-id="61f46-180">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="61f46-181">Her iki `<input>` yaklaşımın de kullanıldığı işlenen öğeler aynıdır:</span><span class="sxs-lookup"><span data-stu-id="61f46-181">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="61f46-182">Rastgele öznitelikleri kabul etmek için `[Parameter]` `CaptureUnmatchedValues` özelliği olarak `true`ayarlanmış özniteliği kullanarak bir bileşen parametresi tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="61f46-182">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="61f46-183">`CaptureUnmatchedValues` Üzerindeki`[Parameter]` özelliği, parametresinin diğer bir parametreyle eşleşmeyen tüm özniteliklerle eşleşmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-183">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="61f46-184">Bir bileşen yalnızca ile `CaptureUnmatchedValues`tek bir parametre tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-184">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="61f46-185">İle `CaptureUnmatchedValues` kullanılan özellik türü dize anahtarlarıyla `Dictionary<string, object>` atanabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-185">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="61f46-186">`IEnumerable<KeyValuePair<string, object>>`Ayrıca `IReadOnlyDictionary<string, object>` , Bu senaryodaki seçenekler de vardır.</span><span class="sxs-lookup"><span data-stu-id="61f46-186">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="61f46-187">Veri bağlama</span><span class="sxs-lookup"><span data-stu-id="61f46-187">Data binding</span></span>

<span data-ttu-id="61f46-188">Hem bileşenlere hem de Dom öğelerine veri bağlama, [@bind](xref:mvc/views/razor#bind) özniteliğiyle birlikte gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-188">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="61f46-189">Aşağıdaki örnek, `_italicsCheck` alanı onay kutusunun işaretli durumuna bağlar:</span><span class="sxs-lookup"><span data-stu-id="61f46-189">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="61f46-190">Onay kutusu seçildiğinde ve kaldırıldığında, özelliğin değeri sırasıyla `true` ve `false`olarak güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-190">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="61f46-191">Onay kutusu kullanıcı arabiriminde, özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-191">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="61f46-192">Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, özellik güncelleştirmeleri genellikle kullanıcı arabirimine hemen yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-192">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="61f46-193">`CurrentValue` Özelliği `@bind` (`<input @bind="CurrentValue" />`) ile kullanmak, temelde aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="61f46-193">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="61f46-194">Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="61f46-194">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="61f46-195">Kullanıcı metin kutusunda yazdığında, `onchange` olay tetiklenir `CurrentValue` ve özellik değiştirilen değere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-195">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="61f46-196">Tür dönüştürmelerinde birkaç durum olduğu için, gerçekte kod oluşturma biraz `@bind` daha karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="61f46-196">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="61f46-197">İlke ' de `@bind` , bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="61f46-197">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="61f46-198">`onchange` Sözdizimi ile [@bind-value](xref:mvc/views/razor#bind) `event` [@bind-value:event](xref:mvc/views/razor#bind)olayları işlemenin yanı sıra, bir özellik veya alan, parametresi () olan bir öznitelik belirtilerek diğer olaylar kullanılarak da bağlanabilir. `@bind`</span><span class="sxs-lookup"><span data-stu-id="61f46-198">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="61f46-199">Aşağıdaki örnek, `oninput` olay için `CurrentValue` özelliği bağlar:</span><span class="sxs-lookup"><span data-stu-id="61f46-199">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="61f46-200">' `onchange`In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-200">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="61f46-201">**Genelleştirme**</span><span class="sxs-lookup"><span data-stu-id="61f46-201">**Globalization**</span></span>

<span data-ttu-id="61f46-202">`@bind`değerler görüntülenmek üzere biçimlendirilir ve geçerli kültürün kuralları kullanılarak ayrıştırılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-202">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="61f46-203">Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> özelliğinden erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-203">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="61f46-204">Aşağıdaki alan türleri için [CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) kullanılır (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="61f46-204">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="61f46-205">Yukarıdaki alan türleri:</span><span class="sxs-lookup"><span data-stu-id="61f46-205">The preceding field types:</span></span>

* <span data-ttu-id="61f46-206">, Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-206">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="61f46-207">Serbest biçimli metin içeremez.</span><span class="sxs-lookup"><span data-stu-id="61f46-207">Can't contain free-form text.</span></span>
* <span data-ttu-id="61f46-208">Tarayıcının uygulamasına göre Kullanıcı etkileşimi özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-208">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="61f46-209">Aşağıdaki alan türleri özel biçimlendirme gereksinimlerine sahiptir ve şu anda tüm büyük tarayıcılarda desteklenmediği için Blazor tarafından desteklenmemektedir:</span><span class="sxs-lookup"><span data-stu-id="61f46-209">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="61f46-210">`@bind`, `@bind:culture` bir değeri ayrıştırmak ve biçimlendirmek <xref:System.Globalization.CultureInfo?displayProperty=fullName> için bir parametresini destekler.</span><span class="sxs-lookup"><span data-stu-id="61f46-210">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="61f46-211">`date` Ve`number` alan türleri kullanılırken bir kültürün belirtilmesi önerilmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-211">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="61f46-212">`date`ve `number` gerekli kültürü sağlayan yerleşik Blazor desteğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="61f46-212">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="61f46-213">Kullanıcının kültürünü ayarlama hakkında daha fazla bilgi için [Yerelleştirme](#localization) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="61f46-213">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="61f46-214">**Biçim dizeleri**</span><span class="sxs-lookup"><span data-stu-id="61f46-214">**Format strings**</span></span>

<span data-ttu-id="61f46-215">Veri bağlama, kullanılarak <xref:System.DateTime> [@bind:format](xref:mvc/views/razor#bind)biçim dizeleriyle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-215">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="61f46-216">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-216">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="61f46-217">Yukarıdaki kodda, `<input>` öğesinin alan türü (`type`) varsayılan olarak `text`olur.</span><span class="sxs-lookup"><span data-stu-id="61f46-217">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="61f46-218">`@bind:format`, aşağıdaki .NET türlerini bağlamak için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-218">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="61f46-219"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="61f46-219"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="61f46-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="61f46-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="61f46-221">`@bind:format` Özniteliği öğesi`<input>` için uygulanacak `value` tarih biçimini belirtir.</span><span class="sxs-lookup"><span data-stu-id="61f46-221">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="61f46-222">Biçim Ayrıca bir `onchange` olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-222">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="61f46-223">Blazor 'in tarihleri biçimlendirmek için `date` yerleşik desteği olduğundan, alan türü için bir biçim belirtilmesi önerilmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-223">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="61f46-224">**Bileşen parametreleri**</span><span class="sxs-lookup"><span data-stu-id="61f46-224">**Component parameters**</span></span>

<span data-ttu-id="61f46-225">Bağlama bileşen parametrelerini tanır, burada `@bind-{property}` bir özellik değeri bileşenler arasında bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-225">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="61f46-226">Aşağıdaki alt bileşende (`ChildComponent`) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:</span><span class="sxs-lookup"><span data-stu-id="61f46-226">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="61f46-227">`EventCallback<T>`, [Eventcallback](#eventcallback) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="61f46-227">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="61f46-228">Aşağıdaki üst bileşen öğesini kullanır `ChildComponent` ve üst `Year` öğeden `ParentYear` parametreyi alt bileşen üzerindeki parametresine bağlar:</span><span class="sxs-lookup"><span data-stu-id="61f46-228">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="61f46-229">Yüklemesi aşağıdaki biçimlendirmeyi üretir: `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="61f46-229">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="61f46-230">`ParentYear` Özelliğin değeri, `ParentComponent` içindekidüğme`ChildComponent` seçilerek değiştirilirse, öğesinin özelliğigüncellenir.`Year`</span><span class="sxs-lookup"><span data-stu-id="61f46-230">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="61f46-231">Yeni değeri `Year` , `ParentComponent` yeniden kullanıldığında kullanıcı arabiriminde işlenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-231">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="61f46-232">Parametrenin türüyle `YearChanged` `Year` eşleşenbiryardımcıolayı`Year` olduğundan parametre bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-232">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="61f46-233">Kurala göre, `<ChildComponent @bind-Year="ParentYear" />` temelde yazmaya eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="61f46-233">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="61f46-234">Genel olarak, bir özellik öznitelik kullanılarak `@bind-property:event` karşılık gelen bir olay işleyicisine bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-234">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="61f46-235">Örneğin, özelliği `MyProp` aşağıdaki iki öznitelik `MyEventHandler` kullanılarak bağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="61f46-235">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="61f46-236">Olay işleme</span><span class="sxs-lookup"><span data-stu-id="61f46-236">Event handling</span></span>

<span data-ttu-id="61f46-237">Razor bileşenleri olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-237">Razor components provide event handling features.</span></span> <span data-ttu-id="61f46-238">Temsilci türü belirtilmiş bir değer ile `on{event}` adlı bir HTML öğesi `onclick` özniteliği `onsubmit`için (örneğin, ve), Razor bileşenleri özniteliğin değerini bir olay işleyicisi olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="61f46-238">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="61f46-239">Özniteliğin adı her zaman [ @on{Event}](xref:mvc/views/razor#onevent)olarak biçimlendirilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-239">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="61f46-240">Aşağıdaki kod, Kullanıcı arabiriminde `UpdateHeading` düğme seçildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="61f46-240">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="61f46-241">Aşağıdaki kod, Kullanıcı arabiriminde `CheckChanged` onay kutusu değiştirildiğinde yöntemini çağırır:</span><span class="sxs-lookup"><span data-stu-id="61f46-241">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="61f46-242">Olay işleyicileri Ayrıca zaman uyumsuz olabilir ve döndürebilir <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="61f46-242">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="61f46-243">El ile çağırmanız `StateHasChanged()`gerekmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-243">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="61f46-244">Özel durumlar oluştuğunda günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-244">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="61f46-245">Aşağıdaki örnekte, `UpdateHeading` düğme seçildiğinde zaman uyumsuz olarak çağrılır:</span><span class="sxs-lookup"><span data-stu-id="61f46-245">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="61f46-246">Olay bağımsız değişken türleri</span><span class="sxs-lookup"><span data-stu-id="61f46-246">Event argument types</span></span>

<span data-ttu-id="61f46-247">Bazı olaylar için olay bağımsız değişkeni türlerine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-247">For some events, event argument types are permitted.</span></span> <span data-ttu-id="61f46-248">Bu olay türlerinden birine erişim gerekmiyorsa, yöntem çağrısında gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="61f46-248">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="61f46-249">Desteklenen [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="61f46-249">Supported [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) are shown in the following table.</span></span>

| <span data-ttu-id="61f46-250">Olay</span><span class="sxs-lookup"><span data-stu-id="61f46-250">Event</span></span> | <span data-ttu-id="61f46-251">örneği</span><span class="sxs-lookup"><span data-stu-id="61f46-251">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="61f46-252">Pano</span><span class="sxs-lookup"><span data-stu-id="61f46-252">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="61f46-253">Sürükle</span><span class="sxs-lookup"><span data-stu-id="61f46-253">Drag</span></span>             | <span data-ttu-id="61f46-254">`DragEventArgs`&ndash; veöğe`DataTransferItem` verilerini sürüklemiş tutun. `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="61f46-254">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="61f46-255">Hata</span><span class="sxs-lookup"><span data-stu-id="61f46-255">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="61f46-256">Çı</span><span class="sxs-lookup"><span data-stu-id="61f46-256">Focus</span></span>            | <span data-ttu-id="61f46-257">`FocusEventArgs`&ndash; İçin`relatedTarget`destek içermez.</span><span class="sxs-lookup"><span data-stu-id="61f46-257">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="61f46-258">`<input>`değişebilir</span><span class="sxs-lookup"><span data-stu-id="61f46-258">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="61f46-259">Klavye</span><span class="sxs-lookup"><span data-stu-id="61f46-259">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="61f46-260">Tığında</span><span class="sxs-lookup"><span data-stu-id="61f46-260">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="61f46-261">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="61f46-261">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="61f46-262">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="61f46-262">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="61f46-263">İlerleme durumu</span><span class="sxs-lookup"><span data-stu-id="61f46-263">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="61f46-264">Dokunma</span><span class="sxs-lookup"><span data-stu-id="61f46-264">Touch</span></span>            | <span data-ttu-id="61f46-265">`TouchEventArgs`&ndash; dokunarakduyarlıbircihazdakitekbir`TouchPoint` iletişim noktasını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="61f46-265">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="61f46-266">Önceki tablodaki olayların özellikleri ve olay işleme davranışı hakkında bilgi için bkz. [başvuru kaynağında EventArgs sınıfları (ASPNET/AspNetCore Release/3.0-preview9 Branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="61f46-266">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0-preview9 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="61f46-267">Lambda ifadeleri</span><span class="sxs-lookup"><span data-stu-id="61f46-267">Lambda expressions</span></span>

<span data-ttu-id="61f46-268">Lambda ifadeleri de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="61f46-268">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="61f46-269">Genellikle, bir dizi öğe üzerinde yineleme yaparken olduğu gibi ek değerlerin üzerinde kapatılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-269">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="61f46-270">Aşağıdaki örnek, her biri `UpdateHeading` Kullanıcı arabiriminde seçildiğinde bir olay bağımsız değişkeni (`UIMouseEventArgs`) ve düğme numarası (`buttonNumber`) geçiren üç düğme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="61f46-270">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="61f46-271">Döngü değişkenini (`i`) bir `for` döngüde doğrudan bir lambda ifadesinde kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="61f46-271">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="61f46-272">Aksi halde, tüm lambda ifadeleri tarafından değeri tüm Lambdalar aynı olmasına neden olan `i`değişken aynı değişken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-272">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="61f46-273">Her zaman değerini yerel bir değişkende (`buttonNumber` önceki örnekte) yakalayın ve sonra kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-273">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="61f46-274">EventCallback</span><span class="sxs-lookup"><span data-stu-id="61f46-274">EventCallback</span></span>

<span data-ttu-id="61f46-275">İç içe bileşenler içeren yaygın bir senaryo, alt bileşen olayı&mdash;olduğunda bir üst bileşenin yöntemini, `onclick` örneğin bir olay gerçekleştiğinde bir olay oluştuğunda çalıştırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="61f46-275">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="61f46-276">Olayları bileşenler genelinde göstermek için bir `EventCallback`kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-276">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="61f46-277">Bir üst bileşen bir alt bileşene `EventCallback`geri çağırma yöntemi atayabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-277">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="61f46-278">Örnek `ChildComponent` uygulamada, bir `onclick` düğmenin işleyicisinin örnek `ParentComponent`tarafından bir `EventCallback` temsilci almak üzere nasıl ayarlandığı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="61f46-278">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="61f46-279">,, Bir çevre `UIMouseEventArgs`cihazından bir `onclick` olay için uygun olan ile öğesine yazılır: `EventCallback`</span><span class="sxs-lookup"><span data-stu-id="61f46-279">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="61f46-280">, `ParentComponent` Alt`ShowMessage` öğenin yöntemine göre ayarlar: `EventCallback<T>`</span><span class="sxs-lookup"><span data-stu-id="61f46-280">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="61f46-281">Düğme ' de `ChildComponent`seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="61f46-281">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="61f46-282">`ParentComponent` Öğesinin`ShowMessage` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-282">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="61f46-283">`messageText`güncelleştirilir ve içinde `ParentComponent`görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-283">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="61f46-284">Geri çağırma yönteminde `StateHasChanged` (`ShowMessage`) bir çağrısı gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="61f46-284">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="61f46-285">`StateHasChanged`alt olaylar, alt öğe içinde yürütülen `ParentComponent`olay işleyicilerinde bileşen rerendering tetiklenmesi için otomatik olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-285">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="61f46-286">`EventCallback`ve `EventCallback<T>` zaman uyumsuz temsilcilere izin verir.</span><span class="sxs-lookup"><span data-stu-id="61f46-286">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="61f46-287">`EventCallback<T>`kesin bir şekilde türdedir ve belirli bir bağımsız değişken türü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="61f46-287">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="61f46-288">`EventCallback`zayıf ve bağımsız değişken türüne izin veriyor.</span><span class="sxs-lookup"><span data-stu-id="61f46-288">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="61f46-289">Bir `EventCallback` veya `EventCallback<T>` ile <xref:System.Threading.Tasks.Task>çağırın ve şunu bekler: `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="61f46-289">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="61f46-290">Olay `EventCallback` işleme `EventCallback<T>` ve bağlama bileşeni parametreleri için ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-290">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="61f46-291">Kesin olarak belirlenmiş `EventCallback<T>` `EventCallback`türü tercih edin.</span><span class="sxs-lookup"><span data-stu-id="61f46-291">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="61f46-292">`EventCallback<T>`bileşenin kullanıcılarına daha iyi hata geri bildirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-292">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="61f46-293">Diğer UI olay işleyicileriyle benzer şekilde, olay parametresini belirtmek isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-293">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="61f46-294">Geri `EventCallback` çağırmaya hiçbir değer geçirilmemişse kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-294">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="61f46-295">Bileşenlere başvuruları yakala</span><span class="sxs-lookup"><span data-stu-id="61f46-295">Capture references to components</span></span>

<span data-ttu-id="61f46-296">Bileşen başvuruları, bir bileşen örneğine başvurmak için bir yol sağlar; böylece, veya `Show` `Reset`gibi komutları bu örneğe verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="61f46-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="61f46-297">Bir bileşen başvurusunu yakalamak için:</span><span class="sxs-lookup"><span data-stu-id="61f46-297">To capture a component reference:</span></span>

* <span data-ttu-id="61f46-298">Alt bileşene [@ref](xref:mvc/views/razor#ref) bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="61f46-298">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="61f46-299">Alt bileşenle aynı türde bir alan tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="61f46-299">Define a field with the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="61f46-300">Bileşen işlendiğinde, `loginDialog` alan `MyLoginDialog` alt bileşen örneğiyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="61f46-300">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="61f46-301">Daha sonra bileşen örneğinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="61f46-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="61f46-302">Değişken yalnızca bileşen işlendikten sonra ve çıktısı `MyLoginDialog` öğesi içerdiğinde doldurulur. `loginDialog`</span><span class="sxs-lookup"><span data-stu-id="61f46-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="61f46-303">Bu noktaya kadar başvurulmasına hiçbir şey yok.</span><span class="sxs-lookup"><span data-stu-id="61f46-303">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="61f46-304">Bileşen işlemesini tamamladıktan sonra bileşen başvurularını işlemek için, `OnAfterRenderAsync` veya `OnAfterRender` yöntemlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-304">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="61f46-305">Bileşen başvurularını yakalama, [öğe başvurularını yakalamak](xref:blazor/javascript-interop#capture-references-to-elements)için benzer bir sözdizimi kullanın, bir [JavaScript birlikte çalışma](xref:blazor/javascript-interop) özelliği değildir.</span><span class="sxs-lookup"><span data-stu-id="61f46-305">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="61f46-306">Bileşen başvuruları yalnızca .net kodunda kullanıldıkları JavaScript&mdash;koduna aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-306">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="61f46-307">Alt bileşenlerin durumunu bulunmamalıdır için bileşen **başvurularını kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="61f46-307">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="61f46-308">Bunun yerine, alt bileşenlere veri geçirmek için normal bildirime dayalı parametreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-308">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="61f46-309">Normal bildirime dayalı parametrelerin kullanımı, otomatik olarak doğru zamanların yeniden yönlendirmesi için alt bileşenlerde oluşur.</span><span class="sxs-lookup"><span data-stu-id="61f46-309">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="61f46-310">Durumu güncelleştirmek için bileşen yöntemlerini dışarıdan çağır</span><span class="sxs-lookup"><span data-stu-id="61f46-310">Invoke component methods externally to update state</span></span>

<span data-ttu-id="61f46-311">Blazor, yürütmenin `SynchronizationContext` tek bir mantıksal iş parçacığını zorlamak için bir kullanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-311">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="61f46-312">Bir bileşenin yaşam döngüsü yöntemleri ve Blazor tarafından oluşturulan tüm olay geri çağırmaları bunun `SynchronizationContext`üzerinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="61f46-312">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="61f46-313">Bir bileşenin bir zamanlayıcı veya diğer bildirimler gibi dış bir olaya göre güncellenmesi gerekir, `InvokeAsync` `SynchronizationContext`Blazor ' a gönderilecek yöntemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-313">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="61f46-314">Örneğin, güncelleştirilmiş durumdaki herhangi bir dinleme bileşenine bildirimde bulunan bir *bildirim hizmeti* düşünün:</span><span class="sxs-lookup"><span data-stu-id="61f46-314">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Action<string, int, Task> Notify;
}
```

<span data-ttu-id="61f46-315">Bir bileşeni güncelleştirmek `NotifierService` için öğesinin kullanımı:</span><span class="sxs-lookup"><span data-stu-id="61f46-315">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="61f46-316">Önceki örnekte, `NotifierService` `OnNotify` bileşen yöntemini Blazor 'in `SynchronizationContext`dışında çağırır.</span><span class="sxs-lookup"><span data-stu-id="61f46-316">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="61f46-317">`InvokeAsync`doğru bağlama geçmek ve bir işlemeyi kuyruğa almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-317">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="61f46-318">Öğe \@ve bileşenlerin korunmasını denetlemek için anahtar kullanın</span><span class="sxs-lookup"><span data-stu-id="61f46-318">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="61f46-319">Bir öğe veya bileşen listesi işlenirken ve öğeler ya da bileşenler daha sonra değiştiğinde, Blazor 'in yayılma algoritması, önceki öğelerin veya bileşenlerin ne zaman tutulacağına ve model nesnelerinin bunlara nasıl eşleneceğine karar vermelidir.</span><span class="sxs-lookup"><span data-stu-id="61f46-319">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="61f46-320">Normalde, bu işlem otomatiktir ve yoksayılabilir, ancak işlemi denetlemek isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="61f46-320">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="61f46-321">Aşağıdaki örnek göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="61f46-321">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="61f46-322">`People` Koleksiyonun içeriği, ekli, silinmiş veya yeniden sıralanmış girdilerle değişebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-322">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="61f46-323">Bileşen yeniden oluşturulduğunda, `<DetailsEditor>` bileşen farklı `Details` parametre değerleri almak için değişebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-323">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="61f46-324">Bu, beklenenden daha karmaşık rerendering oluşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-324">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="61f46-325">Bazı durumlarda rerendering, kayıp öğe odağı gibi görünür davranış farklılıklarına yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-325">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="61f46-326">Eşleme işlemi, `@key` Directive özniteliğiyle denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-326">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="61f46-327">`@key`, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için dağıtılmış algoritmaya neden olur:</span><span class="sxs-lookup"><span data-stu-id="61f46-327">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="61f46-328">Koleksiyon değiştiğinde, yayılma algoritması örnekler ve `person` örnekler arasındaki `<DetailsEditor>` ilişkilendirmeyi korur: `People`</span><span class="sxs-lookup"><span data-stu-id="61f46-328">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="61f46-329">Bir `Person` , `People` listeden silinirse, yalnızca ilgili `<DetailsEditor>` örnek kullanıcı arabiriminden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-329">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="61f46-330">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-330">Other instances are left unchanged.</span></span>
* <span data-ttu-id="61f46-331">Listedeki bir `Person` konuma eklenirse, ilgili konuma bir yeni `<DetailsEditor>` örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-331">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="61f46-332">Diğer örnekler değişmeden bırakılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-332">Other instances are left unchanged.</span></span>
* <span data-ttu-id="61f46-333">Girişler `Person` yeniden Sıralansa, ilgili `<DetailsEditor>` örnekler Kullanıcı arabiriminde korunur ve yeniden sıralanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-333">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="61f46-334">Bazı senaryolarda, kullanımı `@key` rerendering karmaşıklığını en aza indirir ve odak konumu gibi Dom 'ın durum bilgisi olan kısımlarıyla ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="61f46-334">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="61f46-335">Anahtarlar her kapsayıcı öğesi veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="61f46-335">Keys are local to each container element or component.</span></span> <span data-ttu-id="61f46-336">Anahtarlar belge genelinde küresel olarak karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-336">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="61f46-337">Anahtar ne zaman \@kullanılır?</span><span class="sxs-lookup"><span data-stu-id="61f46-337">When to use \@key</span></span>

<span data-ttu-id="61f46-338">Genellikle, bir liste işlendiğinde (örneğin `@key` , bir `@foreach` blokta) ve tanımlamak için uygun bir değer varsa, `@key`bu işlem kullanım açısından mantıklı olur.</span><span class="sxs-lookup"><span data-stu-id="61f46-338">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="61f46-339">Bir nesne değiştiğinde Blazor `@key` 'in bir öğeyi veya bileşen alt ağacını koruma altına almasını engellemek için de kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="61f46-339">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="61f46-340">Değişiklik `@currentPerson` olursa `@key` , öznitelik yönergesi Blazor tüm `<div>` alt öğelerini atmayı ve yeni öğeler ve bileşenlerle Kullanıcı arabiriminde alt ağacı yeniden oluşturmayı zorlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-340">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="61f46-341">Değişiklik sırasında `@currentPerson` hiçbir Kullanıcı arabirimi durumunun korunmayacağını garanti etmeniz gerekirse bu yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-341">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="61f46-342">\@Anahtar ne zaman kullanılmaz?</span><span class="sxs-lookup"><span data-stu-id="61f46-342">When not to use \@key</span></span>

<span data-ttu-id="61f46-343">İle `@key`yayılma yaparken bir performans maliyeti vardır.</span><span class="sxs-lookup"><span data-stu-id="61f46-343">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="61f46-344">Performans maliyeti büyük değildir, ancak yalnızca öğenin veya `@key` bileşen koruma kurallarının denetlenmesi uygulamanın avantajına göre belirleyin.</span><span class="sxs-lookup"><span data-stu-id="61f46-344">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="61f46-345">`@key` Kullanılmasa bile, Blazor alt öğe ve bileşen örneklerini mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="61f46-345">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="61f46-346">Kullanmanın `@key` tek avantajı model örneklerinin, eşlemeyi seçme algoritması yerine, korunan bileşen örneklerine *nasıl* eşlendiğine ilişkin denetimdir.</span><span class="sxs-lookup"><span data-stu-id="61f46-346">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="61f46-347">Anahtar için \@kullanılacak değerler</span><span class="sxs-lookup"><span data-stu-id="61f46-347">What values to use for \@key</span></span>

<span data-ttu-id="61f46-348">Genellikle, için `@key`aşağıdaki değer türlerinden birini sağlamak mantıklı olur:</span><span class="sxs-lookup"><span data-stu-id="61f46-348">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="61f46-349">Model nesne örnekleri (örneğin, `Person` önceki örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="61f46-349">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="61f46-350">Bu, nesne başvurusu eşitliğine göre koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-350">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="61f46-351">Benzersiz tanımlayıcılar (örneğin, veya `int` `Guid`türündeki `string`birincil anahtar değerleri).</span><span class="sxs-lookup"><span data-stu-id="61f46-351">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="61f46-352">Bu değerlerin çakışmayın için `@key` kullanıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="61f46-352">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="61f46-353">Aynı üst öğe içinde çakışan değerler algılanırsa, eski öğeleri veya bileşenleri yeni öğe veya bileşenlere kesin bir şekilde eşlemediğinden Blazor bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="61f46-353">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="61f46-354">Yalnızca nesne örnekleri veya birincil anahtar değerleri gibi farklı değerleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-354">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="61f46-355">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="61f46-355">Lifecycle methods</span></span>

<span data-ttu-id="61f46-356">`OnInitializedAsync`ve `OnInitialized` bileşeni başlatmak için kodu yürütün.</span><span class="sxs-lookup"><span data-stu-id="61f46-356">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="61f46-357">Zaman uyumsuz bir işlem gerçekleştirmek için, `OnInitializedAsync` `await` ve işlem üzerinde anahtar sözcüğünü kullanın:</span><span class="sxs-lookup"><span data-stu-id="61f46-357">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="61f46-358">Zaman uyumlu bir işlem için şunu `OnInitialized`kullanın:</span><span class="sxs-lookup"><span data-stu-id="61f46-358">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="61f46-359">`OnParametersSetAsync`ve `OnParametersSet` bir bileşen üst öğeden parametreler aldığında ve değerler özelliklerine atandığında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-359">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="61f46-360">Bu yöntemler bileşen başlatıldıktan sonra ve bileşen her işlendiğinde yürütülür:</span><span class="sxs-lookup"><span data-stu-id="61f46-360">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="61f46-361">`OnAfterRenderAsync`ve `OnAfterRender` bir bileşen işlemeyi tamamladıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-361">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="61f46-362">Öğe ve bileşen başvuruları bu noktada doldurulur.</span><span class="sxs-lookup"><span data-stu-id="61f46-362">Element and component references are populated at this point.</span></span> <span data-ttu-id="61f46-363">İşlenmiş DOM öğelerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi, işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için bu aşamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="61f46-363">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="61f46-364">`OnAfterRender`*sunucuda prerendering çağrıldığında çağrılmaz.*</span><span class="sxs-lookup"><span data-stu-id="61f46-364">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="61f46-365">Ve `firstRender` için`OnAfterRender` parametresi: `OnAfterRenderAsync`</span><span class="sxs-lookup"><span data-stu-id="61f46-365">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="61f46-366">`true` Bileşen örneği ilk kez çağrıldığında ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-366">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="61f46-367">Başlatma işinin yalnızca bir kez gerçekleştirildiğinden emin olur.</span><span class="sxs-lookup"><span data-stu-id="61f46-367">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="61f46-368">İşleme sırasında tamamlanmamış zaman uyumsuz eylemleri işle</span><span class="sxs-lookup"><span data-stu-id="61f46-368">Handle incomplete async actions at render</span></span>

<span data-ttu-id="61f46-369">Yaşam döngüsü olaylarında gerçekleştirilen zaman uyumsuz eylemler, bileşen işlenmeden önce tamamlanmamış olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-369">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="61f46-370">Yaşam döngüsü yöntemi `null` yürütülürken nesneler, verilerle tamamen doldurulmuş olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-370">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="61f46-371">Nesnelerin başlatıldığını onaylamak için işleme mantığı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="61f46-371">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="61f46-372">Nesneler olduğunda `null`yer tutucu Kullanıcı arabirimi öğelerini (örneğin, bir yükleme iletisi) işleme.</span><span class="sxs-lookup"><span data-stu-id="61f46-372">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="61f46-373">Blazor şablonlarının `OnInitializedAsync`bileşeninde, Asychronously tahmin verileri al (`forecasts`) için geçersiz kılınır. `FetchData`</span><span class="sxs-lookup"><span data-stu-id="61f46-373">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="61f46-374">Ne zaman olduğunda `forecasts` ,kullanıcıyabiryüklemeiletisigörüntülenir.`null`</span><span class="sxs-lookup"><span data-stu-id="61f46-374">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="61f46-375">İşlem tamamlandıktan sonra, bileşen güncelleştirilmiş duruma `Task` `OnInitializedAsync` geri döner.</span><span class="sxs-lookup"><span data-stu-id="61f46-375">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="61f46-376">*Pages/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="61f46-376">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="61f46-377">Parametreler ayarlanmadan önce kodu yürütün</span><span class="sxs-lookup"><span data-stu-id="61f46-377">Execute code before parameters are set</span></span>

<span data-ttu-id="61f46-378">`SetParameters`Parametreler ayarlanmadan önce kodu yürütmek için geçersiz kılınabilir:</span><span class="sxs-lookup"><span data-stu-id="61f46-378">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="61f46-379">Çağrılmadıysa `base.SetParameters` , özel kod gelen parametreler değerini gerekli herhangi bir şekilde yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-379">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="61f46-380">Örneğin, gelen parametrelerin, sınıftaki özelliklere atanması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-380">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="61f46-381">Kullanıcı arabiriminin yenilenmesini gösterme</span><span class="sxs-lookup"><span data-stu-id="61f46-381">Suppress refreshing of the UI</span></span>

<span data-ttu-id="61f46-382">`ShouldRender`Kullanıcı arabiriminin yenilenmesini gizlemek için geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-382">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="61f46-383">Uygulama döndürürse `true`, Kullanıcı arabirimi yenilenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-383">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="61f46-384">`ShouldRender` Geçersiz kılınsa bile, bileşen her zaman ilk olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-384">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="61f46-385">IDisposable ile bileşen atma</span><span class="sxs-lookup"><span data-stu-id="61f46-385">Component disposal with IDisposable</span></span>

<span data-ttu-id="61f46-386">Bir bileşen uygularsa <xref:System.IDisposable>, bileşen kullanıcı arabiriminden kaldırıldığında [Dispose yöntemi](/dotnet/standard/garbage-collection/implementing-dispose) çağırılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-386">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="61f46-387">Aşağıdaki bileşen `@implements IDisposable` `Dispose` ve yöntemini kullanır:</span><span class="sxs-lookup"><span data-stu-id="61f46-387">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="61f46-388">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="61f46-388">Routing</span></span>

<span data-ttu-id="61f46-389">Blazor ' de yönlendirme, uygulamadaki her erişilebilir bileşene bir rota şablonu sunarak elde edilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-389">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="61f46-390">Bir `@page` yönergeyle bir Razor dosyası derlendiğinde, oluşturulan sınıfa yol şablonu <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> belirtilerek verilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-390">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="61f46-391">Çalışma zamanında, yönlendirici bileşen sınıflarını bir `RouteAttribute` ile arar ve hangi bileşenin istenen URL ile eşleşen bir rota şablonuna sahip olduğunu işler.</span><span class="sxs-lookup"><span data-stu-id="61f46-391">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="61f46-392">Birden çok yol şablonu, bir bileşene uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-392">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="61f46-393">Aşağıdaki bileşen ve `/BlazorRoute` `/DifferentBlazorRoute`için isteklere yanıt verir:</span><span class="sxs-lookup"><span data-stu-id="61f46-393">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="61f46-394">Rota parametreleri</span><span class="sxs-lookup"><span data-stu-id="61f46-394">Route parameters</span></span>

<span data-ttu-id="61f46-395">Bileşenler, `@page` yönergede belirtilen yol şablonundan rota parametreleri alabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-395">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="61f46-396">Yönlendirici, karşılık gelen bileşen parametrelerini doldurmak için yol parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-396">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="61f46-397">*Rota parametresi bileşeni*:</span><span class="sxs-lookup"><span data-stu-id="61f46-397">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="61f46-398">İsteğe bağlı parametreler desteklenmez, bu nedenle `@page` Yukarıdaki örnekte iki yönergeler uygulanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-398">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="61f46-399">İlki, bir parametre olmadan bileşene gezinmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="61f46-399">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="61f46-400">İkinci `@page` yönerge, `{text}` yol parametresini alır ve değeri `Text` özelliğine atar.</span><span class="sxs-lookup"><span data-stu-id="61f46-400">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="61f46-401">"Arka plan kod" deneyimi için temel sınıf devralma</span><span class="sxs-lookup"><span data-stu-id="61f46-401">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="61f46-402">Bileşen dosyaları aynı dosyada HTML biçimlendirme C# ve işleme kodu karışımını.</span><span class="sxs-lookup"><span data-stu-id="61f46-402">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="61f46-403">Yönergesi `@inherits` , bileşen işaretlemesini işleme kodundan ayıran "arka plan kod" deneyimiyle Blazor uygulamalar sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-403">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="61f46-404">[Örnek uygulama](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , bileşenin özelliklerini ve yöntemlerini sağlamak için bir bileşenin temel sınıfı `BlazorRocksBase`nasıl devralmasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="61f46-404">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="61f46-405">*Pages/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="61f46-405">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="61f46-406">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="61f46-406">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="61f46-407">Taban sınıfın türevi `ComponentBase`olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="61f46-407">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="61f46-408">Bileşenleri içeri aktar</span><span class="sxs-lookup"><span data-stu-id="61f46-408">Import components</span></span>

<span data-ttu-id="61f46-409">Razor ile yazılmış bir bileşenin ad alanı temel alınarak belirlenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-409">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="61f46-410">Projenin `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="61f46-410">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="61f46-411">Proje kökünden bileşene olan yol.</span><span class="sxs-lookup"><span data-stu-id="61f46-411">The path from the project root to the component.</span></span> <span data-ttu-id="61f46-412">Örneğin, `ComponentsSample/Pages/Index.razor` ad alanı `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="61f46-412">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="61f46-413">Bileşenler ad C# bağlama kurallarını izler.</span><span class="sxs-lookup"><span data-stu-id="61f46-413">Components follow C# name binding rules.</span></span> <span data-ttu-id="61f46-414">*Index. Razor*söz konusu olduğunda, aynı klasördeki, *sayfalardaki*ve üst klasördeki tüm bileşenler ( *componentssample*) kapsamdadır.</span><span class="sxs-lookup"><span data-stu-id="61f46-414">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="61f46-415">Farklı bir ad alanında tanımlanan bileşenler, Razor 'in [ \@using](xref:mvc/views/razor#using) yönergesi kullanılarak kapsama getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-415">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="61f46-416">Klasöründe `NavMenu.razor` `Index.razor` `@using` başka bir bileşen varsa, bileşeni aşağıdaki ifadesiyle birlikte kullanılabilir: `ComponentsSample/Shared/`</span><span class="sxs-lookup"><span data-stu-id="61f46-416">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="61f46-417">Bileşenlere Ayrıca kendi tam adları kullanılarak başvurulabilir, bu da [ \@using](xref:mvc/views/razor#using) yönergesinin gereksinimini ortadan kaldırır:</span><span class="sxs-lookup"><span data-stu-id="61f46-417">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="61f46-418">`global::` Nitelendirme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="61f46-418">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="61f46-419">Diğer ad `using` deyimleri (örneğin, `@using Foo = Bar`) ile bileşenleri içeri aktarma desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="61f46-419">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="61f46-420">Kısmen nitelenmiş adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-420">Partially qualified names aren't supported.</span></span> <span data-ttu-id="61f46-421">Örneğin, ile `@using ComponentsSample` `NavMenu.razor` eklemevebaşvurudesteklenmez`<Shared.NavMenu></Shared.NavMenu>` .</span><span class="sxs-lookup"><span data-stu-id="61f46-421">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="61f46-422">Koşullu HTML öğesi öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="61f46-422">Conditional HTML element attributes</span></span>

<span data-ttu-id="61f46-423">HTML öğesi öznitelikleri, .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-423">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="61f46-424">Değer veya `false` `null`ise, öznitelik işlenmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-424">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="61f46-425">Değer ise `true`, öznitelik küçültülmüş olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-425">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="61f46-426">Aşağıdaki örnekte, `IsCompleted` öğesinin biçimlendirmesinde işlenip işlenmeyeceğini `checked` belirler:</span><span class="sxs-lookup"><span data-stu-id="61f46-426">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="61f46-427">`IsCompleted` İse`true`, onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-427">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="61f46-428">`IsCompleted` İse`false`, onay kutusu şu şekilde işlenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-428">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="61f46-429">Daha fazla bilgi için bkz. <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="61f46-429">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="61f46-430">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="61f46-430">Raw HTML</span></span>

<span data-ttu-id="61f46-431">Dizeler normalde DOM metin düğümleri kullanılarak işlenir. Bu, içerdikleri tüm biçimlendirmenin yok sayıldığı ve değişmez değer olarak kabul edildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="61f46-431">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="61f46-432">Ham HTML işlemek için, HTML içeriğini bir `MarkupString` değerde sarın.</span><span class="sxs-lookup"><span data-stu-id="61f46-432">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="61f46-433">Değer HTML veya SVG olarak ayrıştırılır ve DOM 'a eklenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-433">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="61f46-434">Güvenilmeyen bir kaynaktan oluşturulan ham HTML işleme bir **güvenlik riskidir** ve kaçınılması gerekir!</span><span class="sxs-lookup"><span data-stu-id="61f46-434">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="61f46-435">Aşağıdaki örnek, bir bileşenin işlenmiş `MarkupString` çıktısına statik HTML içeriği bloğunu eklemek için türünün kullanımını gösterir:</span><span class="sxs-lookup"><span data-stu-id="61f46-435">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="61f46-436">Şablonlu bileşenler</span><span class="sxs-lookup"><span data-stu-id="61f46-436">Templated components</span></span>

<span data-ttu-id="61f46-437">Şablonlu bileşenler, bir veya daha fazla UI şablonunu parametre olarak kabul eden bileşenlerdir, daha sonra bileşen işleme mantığının bir parçası olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-437">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="61f46-438">Şablonlu bileşenler, normal bileşenlerden daha yeniden kullanılabilir olan üst düzey bileşenleri yazmanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="61f46-438">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="61f46-439">Birkaç örnek şunlardır:</span><span class="sxs-lookup"><span data-stu-id="61f46-439">A couple of examples include:</span></span>

* <span data-ttu-id="61f46-440">Kullanıcının tablo üst bilgisi, satırları ve altbilgisi için şablon belirtmesini sağlayan tablo bileşeni.</span><span class="sxs-lookup"><span data-stu-id="61f46-440">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="61f46-441">Bir kullanıcının bir listedeki öğeleri işlemek için şablon belirlemesine izin veren bir liste bileşenidir.</span><span class="sxs-lookup"><span data-stu-id="61f46-441">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="61f46-442">Şablon parametreleri</span><span class="sxs-lookup"><span data-stu-id="61f46-442">Template parameters</span></span>

<span data-ttu-id="61f46-443">Şablonlu bir bileşen, veya `RenderFragment` `RenderFragment<T>`türünde bir veya daha fazla bileşen parametresi belirtilerek tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-443">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="61f46-444">Bir işleme parçası, işlenecek Kullanıcı arabiriminin bir kesimini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="61f46-444">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="61f46-445">`RenderFragment<T>`işleme parçası çağrıldığında belirtilebildiği bir tür parametresi alır.</span><span class="sxs-lookup"><span data-stu-id="61f46-445">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="61f46-446">`TableTemplate`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="61f46-446">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="61f46-447">Şablonlu bir bileşen kullanırken, şablon parametreleri parametrelerin adlarıyla (`TableHeader` ve `RowTemplate` aşağıdaki örnekte) eşleşen alt öğeler kullanılarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="61f46-447">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="61f46-448">Şablon bağlam parametreleri</span><span class="sxs-lookup"><span data-stu-id="61f46-448">Template context parameters</span></span>

<span data-ttu-id="61f46-449">Öğe olarak geçirilmiş türdeki `RenderFragment<T>` bileşen bağımsız değişkenleri adlı `context` örtük bir parametreye sahiptir (örneğin, `@context.PetId`Yukarıdaki kod örneğinden), ancak alt öğe `Context` özniteliğini kullanarak parametre adını değiştirebilirsiniz dosyalarında.</span><span class="sxs-lookup"><span data-stu-id="61f46-449">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="61f46-450">Aşağıdaki örnekte, `RowTemplate` `Context` öğesinin özniteliği `pet` parametresini belirtir:</span><span class="sxs-lookup"><span data-stu-id="61f46-450">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="61f46-451">Alternatif olarak, bileşen öğesi üzerinde `Context` özniteliğini de belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="61f46-451">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="61f46-452">Belirtilen `Context` öznitelik, belirtilen tüm şablon parametreleri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="61f46-452">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="61f46-453">Bu, örtük alt içerik (herhangi bir sarmalama alt öğesi olmadan) için içerik parametre adını belirtmek istediğinizde yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-453">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="61f46-454">Aşağıdaki örnekte, `Context` özniteliği `TableTemplate` öğesinde görünür ve tüm şablon parametreleri için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="61f46-454">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="61f46-455">Genel olarak yazılmış bileşenler</span><span class="sxs-lookup"><span data-stu-id="61f46-455">Generic-typed components</span></span>

<span data-ttu-id="61f46-456">Şablonlu bileşenler çoğunlukla genel olarak türdedir.</span><span class="sxs-lookup"><span data-stu-id="61f46-456">Templated components are often generically typed.</span></span> <span data-ttu-id="61f46-457">Örneğin, değerleri işlemek `ListViewTemplate` `IEnumerable<T>` için genel bir bileşen kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-457">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="61f46-458">Genel bir bileşen tanımlamak için, tür parametrelerini `@typeparam` belirtmek için yönergesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="61f46-458">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="61f46-459">Genel türsüz bileşenleri kullanırken tür parametresi mümkünse algılanır:</span><span class="sxs-lookup"><span data-stu-id="61f46-459">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="61f46-460">Aksi halde tür parametresi, tür parametresinin adıyla eşleşen bir öznitelik kullanılarak açıkça belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="61f46-460">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="61f46-461">Aşağıdaki örnekte, `TItem="Pet"` türü belirtir:</span><span class="sxs-lookup"><span data-stu-id="61f46-461">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="61f46-462">Değerleri ve parametreleri basamaklama</span><span class="sxs-lookup"><span data-stu-id="61f46-462">Cascading values and parameters</span></span>

<span data-ttu-id="61f46-463">Bazı senaryolarda, özellikle birden çok bileşen katmanı olduğunda, [bileşen parametreleri](#component-parameters)kullanarak bir üst bileşenden bir alt bileşene veri akışı yapmak uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="61f46-463">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="61f46-464">Değerleri ve parametreleri basamaklama, bir üst bileşenin tüm alt bileşenlerine değer sağlaması için kullanışlı bir yol sağlayarak bu sorunu çözebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-464">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="61f46-465">Basamaklı değerler ve parametreler, bileşenlerin koordinasyonu için bir yaklaşım sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-465">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="61f46-466">Tema örneği</span><span class="sxs-lookup"><span data-stu-id="61f46-466">Theme example</span></span>

<span data-ttu-id="61f46-467">Örnek uygulamadan aşağıdaki örnekte, `ThemeInfo` sınıfı, uygulamanın belirli bir bölümündeki tüm düğmelerin aynı stili paylaştığı şekilde bileşen hiyerarşisinin akışını yapmak için tema bilgilerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="61f46-467">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="61f46-468">*Uıthemeclasses/Themeınfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="61f46-468">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="61f46-469">Bir üst bileşen basamaklı değer bileşeni kullanılarak basamaklı bir değer sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-469">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="61f46-470">`CascadingValue` Bileşen, bileşen hiyerarşisinin bir alt ağacını sarmalanmış ve bu alt ağaçta bulunan tüm bileşenlere tek bir değer sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-470">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="61f46-471">Örneğin, örnek uygulama, uygulamanın düzenleriyle,`ThemeInfo` `@Body` özelliğin düzen gövdesini oluşturan tüm bileşenler için bir geçişli parametre olarak tema bilgilerini () belirtir.</span><span class="sxs-lookup"><span data-stu-id="61f46-471">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="61f46-472">`ButtonClass`öğesinin `btn-success` bir değeri, düzen bileşeninde atanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-472">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="61f46-473">Tüm alt bileşenler bu özelliği `ThemeInfo` basamaklı nesne aracılığıyla kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-473">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="61f46-474">`CascadingValuesParametersLayout`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="61f46-474">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="61f46-475">Basamaklı değerlerin kullanılması için, bileşenler `[CascadingParameter]` özniteliği kullanarak Geçişli Parametreler bildirir.</span><span class="sxs-lookup"><span data-stu-id="61f46-475">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="61f46-476">Basamaklı değerler, türe göre basamaklı parametrelere bağlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-476">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="61f46-477">Örnek uygulamada, `CascadingValuesParametersTheme` bileşen `ThemeInfo` basamaklı değeri basamaklı bir parametreye bağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-477">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="61f46-478">Parametresi, bileşen tarafından görünen düğmelerden birine ait CSS sınıfını ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-478">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="61f46-479">`CascadingValuesParametersTheme`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="61f46-479">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="61f46-480">Aynı alt ağaç içindeki aynı türdeki birden çok değeri basamakla, her `Name` `CascadingValue` bileşene ve karşılık gelen `CascadingParameter`öğesine benzersiz bir dize sağlayın.</span><span class="sxs-lookup"><span data-stu-id="61f46-480">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="61f46-481">Aşağıdaki örnekte, iki `CascadingValue` bileşen adına `MyCascadingType` göre farklı örneklerini basamakla:</span><span class="sxs-lookup"><span data-stu-id="61f46-481">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="61f46-482">Alt bileşende, basamaklı parametreler değerlerini, üst bileşendeki ilgili basamaklı değerleri ada göre alır:</span><span class="sxs-lookup"><span data-stu-id="61f46-482">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="61f46-483">TabSet örneği</span><span class="sxs-lookup"><span data-stu-id="61f46-483">TabSet example</span></span>

<span data-ttu-id="61f46-484">Basamaklı parametreler, bileşenlerin bileşen hiyerarşisinde işbirliği yapmasına de olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-484">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="61f46-485">Örneğin, örnek uygulamada aşağıdaki *Tabset* örneğini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="61f46-485">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="61f46-486">Örnek uygulamada, sekmelerin uygulandığı `ITab` bir arabirim vardır:</span><span class="sxs-lookup"><span data-stu-id="61f46-486">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="61f46-487">Bileşen, `TabSet` çeşitli`Tab` bileşenleri içeren bileşenini kullanır: `CascadingValuesParametersTabSet`</span><span class="sxs-lookup"><span data-stu-id="61f46-487">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="61f46-488">Alt `Tab` bileşenler, `TabSet`öğesine açıkça parametre olarak aktarılmaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-488">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="61f46-489">Bunun yerine, alt `Tab` bileşenleri `TabSet`öğesinin alt içeriğinin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-489">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="61f46-490">Bununla birlikte, `TabSet` üst bilgileri ve etkin sekmeyi işleyebilmesi için her `Tab` bileşen hakkında hala bilmeniz gerekir. Ek kod `TabSet` gerektirmeden bu koordinasyonu etkinleştirmek için bileşen, kendisini alt `Tab` bileşenler tarafından çekilen *basamaklı bir değer olarak sağlayabilir* .</span><span class="sxs-lookup"><span data-stu-id="61f46-490">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="61f46-491">`TabSet`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="61f46-491">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="61f46-492">Alt bileşenler, kapsayan `TabSet` ' i `Tab` basamaklı bir parametre olarak yakalar, bu nedenle bileşenler, bu sekmenin `TabSet` etkin olduğu ve koordinasyonu üzerine eklenir. `Tab`</span><span class="sxs-lookup"><span data-stu-id="61f46-492">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="61f46-493">`Tab`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="61f46-493">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="61f46-494">Razor şablonları</span><span class="sxs-lookup"><span data-stu-id="61f46-494">Razor templates</span></span>

<span data-ttu-id="61f46-495">Oluşturma parçaları Razor şablonu sözdizimi kullanılarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-495">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="61f46-496">Razor şablonları, bir UI parçacığı tanımlamak ve aşağıdaki biçimi varsaymak için bir yoldur:</span><span class="sxs-lookup"><span data-stu-id="61f46-496">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="61f46-497">Aşağıdaki örnek, bir bileşeni doğrudan bir `RenderFragment` bileşende `RenderFragment<T>` nasıl belirtdiğini ve işleneceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="61f46-497">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="61f46-498">Oluşturma parçaları, [şablonlu bileşenlere](#templated-components)bağımsız değişken olarak da geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-498">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="61f46-499">Önceki kodun işlenmiş çıktısı:</span><span class="sxs-lookup"><span data-stu-id="61f46-499">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="61f46-500">El ile RenderTreeBuilder mantığı</span><span class="sxs-lookup"><span data-stu-id="61f46-500">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="61f46-501">`Microsoft.AspNetCore.Components.RenderTree`C# kodu kodda el ile oluşturma dahil olmak üzere bileşenleri ve öğeleri düzenleme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-501">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="61f46-502">Bileşenlerini oluşturmak `RenderTreeBuilder` için kullanımı gelişmiş bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="61f46-502">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="61f46-503">Hatalı biçimlendirilmiş bir bileşen (örneğin, kapatılmamış bir biçimlendirme etiketi) tanımsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-503">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="61f46-504">Aşağıdaki `PetDetails` bileşeni, başka bir bileşende el ile yerleşik olarak kullanılabilecek şekilde göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="61f46-504">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="61f46-505">Aşağıdaki örnekte, `CreateComponent` yöntemindeki döngü üç `PetDetails` bileşen oluşturur.</span><span class="sxs-lookup"><span data-stu-id="61f46-505">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="61f46-506">Bileşenleri ( `RenderTreeBuilder` `OpenComponent` ve`AddAttribute`) oluşturmak için yöntemler çağrılırken, dizi numaraları kaynak kodu satır numaralarıdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-506">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="61f46-507">Blazor fark algoritması, ayrı çağrı etkinleştirmeleri değil ayrı kod satırlarına karşılık gelen sıra numaralarına dayanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-507">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="61f46-508">Yöntemler içeren `RenderTreeBuilder` bir bileşen oluştururken, dizi numaralarına yönelik bağımsız değişkenleri kod olarak kodlayın.</span><span class="sxs-lookup"><span data-stu-id="61f46-508">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="61f46-509">**Sıra numarasını oluşturmak için bir hesaplama veya sayaç kullanmak kötü performansa neden olabilir.**</span><span class="sxs-lookup"><span data-stu-id="61f46-509">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="61f46-510">Daha fazla bilgi için bkz. [kod satırı numaralarıyla Ilgili sıra numaraları ve yürütme sırası çalışmıyor](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) bölümü.</span><span class="sxs-lookup"><span data-stu-id="61f46-510">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="61f46-511">`BuiltContent`bileşeninde</span><span class="sxs-lookup"><span data-stu-id="61f46-511">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="61f46-512">Sıra numaraları, kod satırı numaralarıyla ilgilidir ve yürütme sırası değildir</span><span class="sxs-lookup"><span data-stu-id="61f46-512">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="61f46-513">Blazor `.razor` dosyaları her zaman derlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-513">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="61f46-514">Derleme adımının çalışma zamanında uygulama performansını geliştiren `.razor` bilgileri eklemek için kullanılabilmesi için bu büyük olasılıkla harika bir avantajdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-514">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="61f46-515">Bu geliştirmelerin önemli bir örneği *sıra numarası*içerir.</span><span class="sxs-lookup"><span data-stu-id="61f46-515">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="61f46-516">Sıra numaraları, hangi çıkışların ayrı ve sıralı kod satırlarından geldiğini çalışma zamanına işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="61f46-516">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="61f46-517">Çalışma zamanı, doğrusal bir zamanda, genel ağaç farkı algoritması için genellikle mümkün olandan çok daha hızlı olan etkili ağaç SLA 'ları oluşturmak için bu bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-517">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="61f46-518">Aşağıdaki basit `.razor` dosyayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="61f46-518">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="61f46-519">Yukarıdaki kod, aşağıdakine benzer şekilde derlenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-519">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="61f46-520">Kod ilk kez `someFlag` `true`çalıştırıldığında, Oluşturucu şunları alır:</span><span class="sxs-lookup"><span data-stu-id="61f46-520">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="61f46-521">Sequence</span><span class="sxs-lookup"><span data-stu-id="61f46-521">Sequence</span></span> | <span data-ttu-id="61f46-522">Tür</span><span class="sxs-lookup"><span data-stu-id="61f46-522">Type</span></span>      | <span data-ttu-id="61f46-523">Veri</span><span class="sxs-lookup"><span data-stu-id="61f46-523">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="61f46-524">0</span><span class="sxs-lookup"><span data-stu-id="61f46-524">0</span></span>        | <span data-ttu-id="61f46-525">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="61f46-525">Text node</span></span> | <span data-ttu-id="61f46-526">adı</span><span class="sxs-lookup"><span data-stu-id="61f46-526">First</span></span>  |
| <span data-ttu-id="61f46-527">1\.</span><span class="sxs-lookup"><span data-stu-id="61f46-527">1</span></span>        | <span data-ttu-id="61f46-528">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="61f46-528">Text node</span></span> | <span data-ttu-id="61f46-529">Saniye</span><span class="sxs-lookup"><span data-stu-id="61f46-529">Second</span></span> |

<span data-ttu-id="61f46-530">`someFlag` Olduğunu`false`düşünün ve biçimlendirme yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="61f46-530">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="61f46-531">Bu kez, Oluşturucu şunları alır:</span><span class="sxs-lookup"><span data-stu-id="61f46-531">This time, the builder receives:</span></span>

| <span data-ttu-id="61f46-532">Sequence</span><span class="sxs-lookup"><span data-stu-id="61f46-532">Sequence</span></span> | <span data-ttu-id="61f46-533">Tür</span><span class="sxs-lookup"><span data-stu-id="61f46-533">Type</span></span>       | <span data-ttu-id="61f46-534">Veri</span><span class="sxs-lookup"><span data-stu-id="61f46-534">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="61f46-535">1\.</span><span class="sxs-lookup"><span data-stu-id="61f46-535">1</span></span>        | <span data-ttu-id="61f46-536">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="61f46-536">Text node</span></span>  | <span data-ttu-id="61f46-537">Saniye</span><span class="sxs-lookup"><span data-stu-id="61f46-537">Second</span></span> |

<span data-ttu-id="61f46-538">Çalışma zamanı bir fark gerçekleştirdiğinde, sıradaki `0` öğenin kaldırıldığını görür, bu nedenle aşağıdaki önemsiz *düzenleme betiğini*oluşturur:</span><span class="sxs-lookup"><span data-stu-id="61f46-538">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="61f46-539">İlk metin düğümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="61f46-539">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="61f46-540">Program aracılığıyla sıra numaraları oluşturursanız ne yanlış gider</span><span class="sxs-lookup"><span data-stu-id="61f46-540">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="61f46-541">Aşağıdaki işleme Ağacı Oluşturucusu mantığını yazmanız yerine düşünün:</span><span class="sxs-lookup"><span data-stu-id="61f46-541">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="61f46-542">Şimdi ilk çıktı:</span><span class="sxs-lookup"><span data-stu-id="61f46-542">Now, the first output is:</span></span>

| <span data-ttu-id="61f46-543">Sequence</span><span class="sxs-lookup"><span data-stu-id="61f46-543">Sequence</span></span> | <span data-ttu-id="61f46-544">Tür</span><span class="sxs-lookup"><span data-stu-id="61f46-544">Type</span></span>      | <span data-ttu-id="61f46-545">Veri</span><span class="sxs-lookup"><span data-stu-id="61f46-545">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="61f46-546">0</span><span class="sxs-lookup"><span data-stu-id="61f46-546">0</span></span>        | <span data-ttu-id="61f46-547">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="61f46-547">Text node</span></span> | <span data-ttu-id="61f46-548">adı</span><span class="sxs-lookup"><span data-stu-id="61f46-548">First</span></span>  |
| <span data-ttu-id="61f46-549">1\.</span><span class="sxs-lookup"><span data-stu-id="61f46-549">1</span></span>        | <span data-ttu-id="61f46-550">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="61f46-550">Text node</span></span> | <span data-ttu-id="61f46-551">Saniye</span><span class="sxs-lookup"><span data-stu-id="61f46-551">Second</span></span> |

<span data-ttu-id="61f46-552">Bu sonuç önceki bir durum ile aynıdır, bu nedenle olumsuz bir sorun yoktur.</span><span class="sxs-lookup"><span data-stu-id="61f46-552">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="61f46-553">`someFlag``false` ikinci işleme ve çıktı:</span><span class="sxs-lookup"><span data-stu-id="61f46-553">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="61f46-554">Sequence</span><span class="sxs-lookup"><span data-stu-id="61f46-554">Sequence</span></span> | <span data-ttu-id="61f46-555">Tür</span><span class="sxs-lookup"><span data-stu-id="61f46-555">Type</span></span>      | <span data-ttu-id="61f46-556">Veri</span><span class="sxs-lookup"><span data-stu-id="61f46-556">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="61f46-557">0</span><span class="sxs-lookup"><span data-stu-id="61f46-557">0</span></span>        | <span data-ttu-id="61f46-558">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="61f46-558">Text node</span></span> | <span data-ttu-id="61f46-559">Saniye</span><span class="sxs-lookup"><span data-stu-id="61f46-559">Second</span></span> |

<span data-ttu-id="61f46-560">Bu kez, fark algoritması *iki* değişikliğin oluştuğunu görür ve algoritma aşağıdaki düzenleme betiğini üretir:</span><span class="sxs-lookup"><span data-stu-id="61f46-560">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="61f46-561">İlk metin düğümünün değerini olarak `Second`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="61f46-561">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="61f46-562">İkinci metin düğümünü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="61f46-562">Remove the second text node.</span></span>

<span data-ttu-id="61f46-563">Sıra numaralarının oluşturulması, `if/else` dal ve döngülerin orijinal kodda bulunduğu yer hakkındaki tüm yararlı bilgileri kaybetti.</span><span class="sxs-lookup"><span data-stu-id="61f46-563">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="61f46-564">Bu, daha önce olduğu gibi bir fark **ile iki kez** sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-564">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="61f46-565">Bu, önemsiz bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="61f46-565">This is a trivial example.</span></span> <span data-ttu-id="61f46-566">Karmaşık ve derin iç içe yapıları ve özellikle döngülerle daha gerçekçi durumlarda performans maliyeti daha önemlidir.</span><span class="sxs-lookup"><span data-stu-id="61f46-566">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="61f46-567">Hangi döngü bloklarının veya dallarının eklendiğini veya kaldırıldığını hemen belirlemek yerine, fark algoritmasının işleme ağaçlarına katmaları ve genellikle eski ve yeni yapıların nasıl olduğu hakkında yanlış bir biçimde düzenleme betikleri oluşturması birbirleriyle ilişkilendir.</span><span class="sxs-lookup"><span data-stu-id="61f46-567">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="61f46-568">Kılavuz ve ekibinizle</span><span class="sxs-lookup"><span data-stu-id="61f46-568">Guidance and conclusions</span></span>

* <span data-ttu-id="61f46-569">Sıra numaraları dinamik olarak oluşturulursa uygulama performansı de vardır.</span><span class="sxs-lookup"><span data-stu-id="61f46-569">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="61f46-570">Altyapı, derleme zamanında yakalanmadığı takdirde gerekli bilgiler bulunmadığından, çalışma zamanında kendi sıra numaralarını otomatik olarak oluşturamaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-570">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="61f46-571">El ile uygulanan `RenderTreeBuilder` mantık uzun blokları yazmayın.</span><span class="sxs-lookup"><span data-stu-id="61f46-571">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="61f46-572">Dosyaları `.razor` tercih edin ve derleyicinin sıra numaralarıyla uğraşmak için izin verin.</span><span class="sxs-lookup"><span data-stu-id="61f46-572">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="61f46-573">Dizi numaraları sabit kodluysa, fark algoritması yalnızca değer değerinde sıra numaralarının artırılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="61f46-573">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="61f46-574">İlk değer ve boşluklar ilgisiz.</span><span class="sxs-lookup"><span data-stu-id="61f46-574">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="61f46-575">Tek bir seçenek, kod satırı numarasını sıra numarası olarak kullanmak veya sıfırdan başlayıp bir ya da yüzlerce (ya da tercih edilen aralığa) artırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="61f46-575">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="61f46-576">Blazor, sıra numaralarını kullanır, diğer ağaç dağıtma Kullanıcı arabirimi çerçeveleri bunları kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-576">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="61f46-577">Dizi numaraları kullanıldığında ve Blazor, geliştiricilerin yazma `.razor` dosyaları için otomatik olarak sıra numaralarıyla ilgilenen bir derleme adımının avantajına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="61f46-577">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="61f46-578">Yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="61f46-578">Localization</span></span>

<span data-ttu-id="61f46-579">Blazor sunucu tarafı uygulamalar, [Yerelleştirme ara yazılımı](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-579">Blazor server-side apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="61f46-580">Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.</span><span class="sxs-lookup"><span data-stu-id="61f46-580">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="61f46-581">Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="61f46-581">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="61f46-582">Çerezler</span><span class="sxs-lookup"><span data-stu-id="61f46-582">Cookies</span></span>](#cookies)
* [<span data-ttu-id="61f46-583">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="61f46-583">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="61f46-584">Daha fazla bilgi ve örnek için bkz <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="61f46-584">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="61f46-585">Özgü</span><span class="sxs-lookup"><span data-stu-id="61f46-585">Cookies</span></span>

<span data-ttu-id="61f46-586">Yerelleştirme kültürü tanımlama bilgisi kullanıcının kültürünü kalıcı hale getirebilirler.</span><span class="sxs-lookup"><span data-stu-id="61f46-586">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="61f46-587">Tanımlama bilgisi, uygulamanın ana bilgisayar `OnGet` sayfası (*Pages/Host. cshtml. cs*) yöntemi tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="61f46-587">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="61f46-588">Yerelleştirme ara yazılımı, sonraki isteklerde Kullanıcı kültürünü ayarlamak için tanımlama bilgilerini okur.</span><span class="sxs-lookup"><span data-stu-id="61f46-588">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="61f46-589">Tanımlama bilgisinin kullanımı, WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-589">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="61f46-590">Yerelleştirme şemaları URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışmayabilir, bu nedenle kültürü kalıcı hale getiremeyebilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-590">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="61f46-591">Bu nedenle, yerelleştirme kültürü tanımlama bilgisinin kullanılması önerilen yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="61f46-591">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="61f46-592">Kültür bir yerelleştirme tanımlama bilgisinde kalıcı hale getirilir kültür atamak için herhangi bir teknik kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-592">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="61f46-593">Uygulamanın zaten sunucu tarafı ASP.NET Core için bir yerelleştirme şeması varsa, uygulamanın var olan yerelleştirme altyapısını kullanmaya devam edin ve uygulamanın şeması içinde yerelleştirme kültür tanımlama bilgisini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="61f46-593">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="61f46-594">Aşağıdaki örnekte, yerelleştirme ara yazılımı tarafından okunabilen bir tanımlama bilgisinde geçerli kültürün nasıl ayarlanacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="61f46-594">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="61f46-595">Blazor Server-Side uygulamasında aşağıdaki içeriklerle bir *Pages/Host. cshtml. cs* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="61f46-595">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor server-side app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="61f46-596">Yerelleştirme uygulamada işlenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-596">Localization is handled in the app:</span></span>

1. <span data-ttu-id="61f46-597">Tarayıcı, uygulamaya bir ilk HTTP isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="61f46-597">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="61f46-598">Kültür, yerelleştirme ara yazılımı tarafından atanır.</span><span class="sxs-lookup"><span data-stu-id="61f46-598">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="61f46-599">*_Host. cshtml. cs* dosyasındaki yöntemi,yanıtınbirparçasıolarakbirtanımlamabilgisindekültürüdevamettirir.`OnGet`</span><span class="sxs-lookup"><span data-stu-id="61f46-599">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="61f46-600">Tarayıcı, etkileşimli bir Blazor sunucu tarafı oturumu oluşturmak için bir WebSocket bağlantısı açar.</span><span class="sxs-lookup"><span data-stu-id="61f46-600">The browser opens a WebSocket connection to create an interactive Blazor server-side session.</span></span>
1. <span data-ttu-id="61f46-601">Yerelleştirme ara yazılımı tanımlama bilgisini okur ve kültürü atar.</span><span class="sxs-lookup"><span data-stu-id="61f46-601">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="61f46-602">Blazor sunucu tarafı oturumu doğru kültür ile başlar.</span><span class="sxs-lookup"><span data-stu-id="61f46-602">The Blazor server-side session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="61f46-603">Kültürü seçmek için Kullanıcı arabirimi sağlama</span><span class="sxs-lookup"><span data-stu-id="61f46-603">Provide UI to choose the culture</span></span>

<span data-ttu-id="61f46-604">Bir kullanıcının bir kültür seçmesine izin vermek için kullanıcı ARABIRIMI sağlamak üzere bir *yeniden yönlendirme tabanlı yaklaşım* önerilir.</span><span class="sxs-lookup"><span data-stu-id="61f46-604">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="61f46-605">Kullanıcı, kullanıcının oturum açma sayfasına yeniden yönlendirildiği ve sonra özgün kaynağa yeniden yönlendirildiği güvenli bir kaynağa&mdash;erişmeyi denediğinde, Web uygulamasında gerçekleşmelere benzer.</span><span class="sxs-lookup"><span data-stu-id="61f46-605">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="61f46-606">Uygulama, bir denetleyiciye yeniden yönlendirme yoluyla kullanıcının seçili kültürünü devam ettirir.</span><span class="sxs-lookup"><span data-stu-id="61f46-606">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="61f46-607">Denetleyici kullanıcının seçili kültürünü bir tanımlama bilgisine ayarlar ve kullanıcıyı özgün URI 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="61f46-607">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="61f46-608">Bir tanımlama bilgisinde kullanıcının seçili kültürünü ayarlamak ve özgün URI 'ye yeniden yönlendirmeyi gerçekleştirmek için sunucuda bir HTTP uç noktası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="61f46-608">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="61f46-609">Açık yeniden yönlendirme saldırılarını engellemek için eylemsonucunukullanın.`LocalRedirect`</span><span class="sxs-lookup"><span data-stu-id="61f46-609">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="61f46-610">Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="61f46-610">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="61f46-611">Aşağıdaki bileşen, Kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirileceği hakkında bir örnek göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="61f46-611">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(UIChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="61f46-612">Blazor uygulamalarında .NET yerelleştirme senaryolarını kullanma</span><span class="sxs-lookup"><span data-stu-id="61f46-612">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="61f46-613">Blazor uygulamaları içinde, aşağıdaki .NET yerelleştirme ve genelleştirme senaryoları kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="61f46-613">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="61f46-614">. NET ' in kaynak sistemi</span><span class="sxs-lookup"><span data-stu-id="61f46-614">.NET's resources system</span></span>
* <span data-ttu-id="61f46-615">Kültüre özgü sayı ve Tarih biçimlendirmesi</span><span class="sxs-lookup"><span data-stu-id="61f46-615">Culture-specific number and date formatting</span></span>

<span data-ttu-id="61f46-616">Blazor 'ın `@bind` işlevselliği, kullanıcının geçerli kültürüne göre Genelleştirme gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="61f46-616">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="61f46-617">Daha fazla bilgi için [veri bağlama](#data-binding) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="61f46-617">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="61f46-618">Sınırlı bir ASP.NET Core yerelleştirme senaryosu kümesi şu anda desteklenmektedir:</span><span class="sxs-lookup"><span data-stu-id="61f46-618">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="61f46-619">`IStringLocalizer<>`Blazor uygulamalarında *desteklenir* .</span><span class="sxs-lookup"><span data-stu-id="61f46-619">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="61f46-620">`IHtmlLocalizer<>`, `IViewLocalizer<>`ve veri ek açıklamaları yerelleştirme ASP.NET Core MVC senaryolardır ve Blazor uygulamalarında **desteklenmez** .</span><span class="sxs-lookup"><span data-stu-id="61f46-620">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="61f46-621">Daha fazla bilgi için bkz. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="61f46-621">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="61f46-622">Ölçeklenebilir vektör grafik (SVG) görüntüleri</span><span class="sxs-lookup"><span data-stu-id="61f46-622">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="61f46-623">Blazor tarafından oluşturulduğundan, ölçeklenebilir vektör grafik (SVG) görüntüleri ( *. SVG*) dahil olmak üzere tarayıcıda desteklenen görüntüler, `<img>` etiketi aracılığıyla desteklenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-623">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="61f46-624">Benzer şekilde, SVG görüntüleri bir stil sayfası dosyasının ( *. css*) CSS kurallarında desteklenir:</span><span class="sxs-lookup"><span data-stu-id="61f46-624">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="61f46-625">Ancak, satır içi SVG işaretlemesi tüm senaryolarda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="61f46-625">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="61f46-626">Bir `<svg>` etiketi doğrudan bir bileşen dosyasına ( *. Razor*) yerleştirirseniz, temel görüntü işleme desteklenir, ancak birçok gelişmiş senaryo desteklenmemiştir.</span><span class="sxs-lookup"><span data-stu-id="61f46-626">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="61f46-627">Örneğin, `<use>` Etiketler Şu anda dikkate alınamaz ve `@bind` bazı SVG etiketleriyle kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="61f46-627">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="61f46-628">Gelecekteki bir sürümde bu sınırlamaları ele almayı bekliyoruz.</span><span class="sxs-lookup"><span data-stu-id="61f46-628">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61f46-629">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="61f46-629">Additional resources</span></span>

* <span data-ttu-id="61f46-630"><xref:security/blazor/server-side>&ndash; Kaynak tükenmesi ile Çekişmek zorunda olması gereken Blazor sunucu tarafı uygulamalar oluşturmaya yönelik yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="61f46-630"><xref:security/blazor/server-side> &ndash; Includes guidance on building Blazor server-side apps that must contend with resource exhaustion.</span></span>
