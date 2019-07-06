---
title: Oluşturma ve ASP.NET Core Razor bileşenleri kullanma
author: guardrex
description: Oluşturma ve bileşen ömürleri yönetme verilere bağlayın ve olayları işlemek nasıl dahil olmak üzere, Razor bileşenlerini kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: blazor/components
ms.openlocfilehash: ca715457604f08e50628d1c1189ea3c570321112
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596104"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="b7b43-103">Oluşturma ve ASP.NET Core Razor bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="b7b43-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="b7b43-104">Tarafından [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b7b43-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b7b43-105">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7b43-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b7b43-106">Blazor uygulamaları kullanılarak oluşturulur *bileşenleri*.</span><span class="sxs-lookup"><span data-stu-id="b7b43-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="b7b43-107">Bir bileşen, kullanıcı arabirimi (UI), sayfa, iletişim veya form gibi kendi içinde bir öbektir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="b7b43-108">Bir bileşeni, HTML biçimlendirmesi ve veri ekleme veya UI olaylarına yanıt vermek için gereken işleme mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="b7b43-109">Esnek ve basit bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="b7b43-110">Bunlar iç içe geçmiş, yeniden kullanılabilir ve projeler arasında paylaşılan.</span><span class="sxs-lookup"><span data-stu-id="b7b43-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="b7b43-111">Bileşen sınıfları</span><span class="sxs-lookup"><span data-stu-id="b7b43-111">Component classes</span></span>

<span data-ttu-id="b7b43-112">Bileşenleri içinde uygulanan [Razor](xref:mvc/views/razor) bileşen dosyaları ( *.razor*) bir birleşimi kullanılarak C# ve HTML biçimlendirmesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="b7b43-113">Bir bileşenin Blazor, resmi olarak adlandırılır bir *Razor bileşen*.</span><span class="sxs-lookup"><span data-stu-id="b7b43-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="b7b43-114">Bileşenlerini kullanarak yazarı olduğu *.cshtml* dosyaları kullanarak Razor bileşen dosyaları tanımlanmış olduğu sürece dosya uzantısı `_RazorComponentInclude` MSBuild özelliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-114">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="b7b43-115">Örneğin, belirten bir uygulamayı tüm *.cshtml* altında dosyaları *sayfaları* klasör Razor bileşenleri dosyaları kabul:</span><span class="sxs-lookup"><span data-stu-id="b7b43-115">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="b7b43-116">Bir bileşen için kullanıcı Arabirimi, HTML kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="b7b43-117">(Örneğin, döngü, koşullular, ifadeleri) dinamik işleme mantığı, katıştırılmış kullanarak eklenir C# adlı söz dizimi [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b7b43-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="b7b43-118">Bir uygulamanın ne zaman derlenir, HTML biçimlendirmesi ve C# işleme mantığı, bir bileşen sınıfı dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="b7b43-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="b7b43-119">Oluşturulan sınıfın adı dosya adıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="b7b43-120">Bileşen sınıfı üyeleri tanımlanmış bir `@code` blok.</span><span class="sxs-lookup"><span data-stu-id="b7b43-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="b7b43-121">İçinde `@code` blok, bileşen durumu (Özellikler, alanlar) olay işleme için veya başka bir bileşen mantığı tanımlamak için yöntemleri ile belirtilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="b7b43-122">Birden fazla `@code` bloğu izin verilebilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="b7b43-123">ASP.NET Core, önceki sürümlerinde `@functions` blokları için aynı amaca kullanılmış `@code` engeller.</span><span class="sxs-lookup"><span data-stu-id="b7b43-123">In previous versions of ASP.NET Core, `@functions` blocks were used for the same purpose as `@code` blocks.</span></span> <span data-ttu-id="b7b43-124">`@functions` çalışmaya devam blokları, ancak kullanmanızı öneririz `@code` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-124">`@functions` blocks continue to work, but we recommend using the `@code` directive.</span></span>

<span data-ttu-id="b7b43-125">Bileşen üyeleri, ardından bileşenin parçası mantığı kullanarak işleme olarak kullanılabilir C# ile başlayan ifadeleri `@`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-125">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="b7b43-126">Örneğin, bir C# alan ekleyerek işlenen `@` alan adı.</span><span class="sxs-lookup"><span data-stu-id="b7b43-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="b7b43-127">Aşağıdaki örnek, değerlendirir ve işler:</span><span class="sxs-lookup"><span data-stu-id="b7b43-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="b7b43-128">`_headingFontStyle` CSS özellik değerini `font-style`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="b7b43-129">`_headingText` içeriği için `<h1>` öğesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="b7b43-130">Bileşen, bileşen başlangıçta işlenen sonra olaylara yanıt olarak, işleme ağacında yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="b7b43-131">Blazor yeni bir işleme ağacı Öncekine karşı karşılaştırır ve herhangi bir değişiklik tarayıcının belge nesne modeli (DOM) için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="b7b43-132">Bileşenleri sıradan C# sınıfları ve bir projesi içinde her yerden yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="b7b43-133">Web sayfaları genellikle üreten bileşenler bulunan *sayfaları* klasör.</span><span class="sxs-lookup"><span data-stu-id="b7b43-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="b7b43-134">Sayfası olmayan bileşenleri yerleştirildiğinde sık *paylaşılan* klasör veya özel bir klasör projeye eklendi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="b7b43-135">Özel bir klasör kullanmayı ekleyin ya da özel klasör ad alanı ana bileşen veya uygulamanın *_Imports.razor* dosya.</span><span class="sxs-lookup"><span data-stu-id="b7b43-135">To use a custom folder, either add the custom folder's namespace to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="b7b43-136">Örneğin, aşağıdaki ad alanı bileşenlerde yapar bir *bileşenleri* klasör uygulamanın kök ad alanı olduğunda kullanılabilen `WebApplication`:</span><span class="sxs-lookup"><span data-stu-id="b7b43-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="b7b43-137">Bileşenleri Razor sayfaları ve MVC uygulamalarla tümleştirin</span><span class="sxs-lookup"><span data-stu-id="b7b43-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="b7b43-138">Bileşenleri ile mevcut Razor sayfaları ve MVC uygulamaları kullanın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="b7b43-139">Var olan sayfaları veya Razor bileşenler kullanmaya görünümleri yeniden gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="b7b43-140">Sayfa veya Görünüm işlendiğinde bileşenleri aynı anda prerendered.</span><span class="sxs-lookup"><span data-stu-id="b7b43-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="b7b43-141">Bir bileşenden bir sayfa ya da Görünüm işlemek için `RenderComponentAsync<TComponent>` HTML yardımcı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="b7b43-142">Sayfalar ve görünümler bileşenleri kullanabilirsiniz, ancak listesiyse true değil.</span><span class="sxs-lookup"><span data-stu-id="b7b43-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="b7b43-143">Bileşenler, kısmi görünümleri ve bölümler gibi görünümü ve sayfa belirli senaryoları kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="b7b43-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="b7b43-144">Bir bileşene dönüştürerek bir bileşende kısmi görünümü, kısmi görünümü mantıksal çarpanını mantığı kullanılacak.</span><span class="sxs-lookup"><span data-stu-id="b7b43-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="b7b43-145">Blazor sunucu tarafı uygulamalar yönetilen bileşenleri işlenmiş ve bileşen durumunu şeklini hakkında daha fazla bilgi için bkz: <xref:blazor/hosting-models> makalesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-145">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="using-components"></a><span data-ttu-id="b7b43-146">Bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="b7b43-146">Using components</span></span>

<span data-ttu-id="b7b43-147">Bileşenleri, diğer bileşenlerin bunları bildirerek içerebilir HTML öğesi söz dizimini kullanarak.</span><span class="sxs-lookup"><span data-stu-id="b7b43-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="b7b43-148">Bir bileşen kullanma için işaretleme, etiketin adını bileşen türü olduğu gibi HTML etiketleri arar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="b7b43-149">Aşağıdaki biçimlendirmede *Index.razor* işleyen bir `HeadingComponent` örneği:</span><span class="sxs-lookup"><span data-stu-id="b7b43-149">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="b7b43-150">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-150">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

## <a name="component-parameters"></a><span data-ttu-id="b7b43-151">Bileşen parametreleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-151">Component parameters</span></span>

<span data-ttu-id="b7b43-152">Bileşenleri olabilir *bileşeni parametreleri*, hangi özellikleri kullanılarak tanımlanır (genellikle *genel olmayan*) ile bileşen sınıfı üzerinde `[Parameter]` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-152">Components can have *component parameters*, which are defined using properties (usually *non-public*) on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="b7b43-153">Öznitelikleri bir bileşen için bağımsız değişken biçimlendirme içinde belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-153">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="b7b43-154">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-154">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="b7b43-155">Aşağıdaki örnekte, `ParentComponent` değerini ayarlar `Title` özelliği `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-155">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="b7b43-156">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-156">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="b7b43-157">Alt içeriğin</span><span class="sxs-lookup"><span data-stu-id="b7b43-157">Child content</span></span>

<span data-ttu-id="b7b43-158">Bileşenleri başka bir bileşen içeriğini ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b7b43-158">Components can set the content of another component.</span></span> <span data-ttu-id="b7b43-159">Atama bileşen alıcı bileşeni belirtin etiketleri arasında içerik sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-159">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="b7b43-160">Aşağıdaki örnekte, `ChildComponent` sahip bir `ChildContent` temsil eden özellik bir `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-160">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="b7b43-161">Değerini `ChildContent` bileşenin işaretlemede içeriği burada işleneceğini konumlandırıldı.</span><span class="sxs-lookup"><span data-stu-id="b7b43-161">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="b7b43-162">Değerini `ChildContent` ana bileşenden alınan ve önyükleme bölmenin içinde işlenen `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-162">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="b7b43-163">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-163">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="b7b43-164">Özellik Alma `RenderFragment` içeriği adlı `ChildContent` kural tarafından.</span><span class="sxs-lookup"><span data-stu-id="b7b43-164">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="b7b43-165">Aşağıdaki `ParentComponent` içerik işleme için sağlayabilir `ChildComponent` içeriğini yerleştirerek `<ChildComponent>` etiketler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-165">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="b7b43-166">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-166">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="data-binding"></a><span data-ttu-id="b7b43-167">Veri bağlama</span><span class="sxs-lookup"><span data-stu-id="b7b43-167">Data binding</span></span>

<span data-ttu-id="b7b43-168">Veri bağlama bileşenleri hem DOM öğeleri ile gerçekleştirilir `@bind` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-168">Data binding to both components and DOM elements is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="b7b43-169">Aşağıdaki örnek bağlar `_italicsCheck` alan için onay kutusunun işaretli durumu:</span><span class="sxs-lookup"><span data-stu-id="b7b43-169">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="b7b43-170">Onay kutusunu işaretli ve seçildiğinde özelliğin değerini şekilde güncelleştirilir `true` ve `false`sırasıyla.</span><span class="sxs-lookup"><span data-stu-id="b7b43-170">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="b7b43-171">Yalnızca bileşen, özelliğin değerinin değiştirilmesi için değil yanıt oluşturulduğunda onay kutusunu kullanıcı Arabiriminde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-171">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="b7b43-172">Olay işleyici kodu yürütüldükten sonra bileşenleri kendilerini işleme olduğundan, özellik güncelleştirmeleri genellikle kullanıcı Arabiriminde hemen yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-172">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="b7b43-173">Kullanarak `@bind` ile bir `CurrentValue` özelliği (`<input @bind="CurrentValue" />`) aslında aşağıdakine eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-173">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="b7b43-174">Bileşen işlendiğinde `value` giriş öğesinin geldiği `CurrentValue` özelliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-174">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="b7b43-175">Kullanıcı, metin kutusuna yazdığında `onchange` olay tetiklenir ve `CurrentValue` özelliği değiştirilmiş değerine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-175">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="b7b43-176">Gerçekte, kod oluşturma biraz daha karmaşık olduğundan `@bind` tür dönüştürmeleri gerçekleştirildiği birkaç durum işler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-176">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="b7b43-177">Giren İlkesi `@bind` geçerli değerini bir ifade ile ilişkilendirir bir `value` kayıtlı işleyici kullanarak öznitelik ve işleyicilerini değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-177">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="b7b43-178">İşleme yanı sıra `onchange` olaylarıyla `@bind` söz dizimi, özelliği veya alanı belirterek diğer olayları kullanarak bağlanabilir bir `@bind-value` özniteliğini bir `event` parametresi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-178">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an `@bind-value` attribute with an `event` parameter.</span></span> <span data-ttu-id="b7b43-179">Aşağıdaki örnek bağlar `CurrentValue` özelliği `oninput` olay:</span><span class="sxs-lookup"><span data-stu-id="b7b43-179">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="b7b43-180">Farklı `onchange`, öğe odağından çıktığında tetiklenen `oninput` değeri metin kutusunda değiştiğinde harekete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-180">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="b7b43-181">**Biçim dizeleri**</span><span class="sxs-lookup"><span data-stu-id="b7b43-181">**Format strings**</span></span>

<span data-ttu-id="b7b43-182">Veri bağlama ile birlikte çalışır <xref:System.DateTime> biçim dizeleri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-182">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="b7b43-183">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="b7b43-183">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="b7b43-184">`@bind:format` Özniteliği uygulamak için tarih biçimini belirtir `value` , `<input>` öğesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-184">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="b7b43-185">Biçimi de değer ayrıştırmak için kullanılan zaman bir `onchange` olayı oluşur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-185">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="b7b43-186">**Bileşen parametreleri**</span><span class="sxs-lookup"><span data-stu-id="b7b43-186">**Component parameters**</span></span>

<span data-ttu-id="b7b43-187">Bağlama bileşeni parametreleri de tanır burada `@bind-{property}` bir özellik değeri bileşenlerinde bağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b7b43-187">Binding also recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="b7b43-188">Aşağıdaki alt bileşen (`ChildComponent`) sahip bir `Year` bileşen parametresi ve `YearChanged` geri çağırma:</span><span class="sxs-lookup"><span data-stu-id="b7b43-188">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="b7b43-189">`EventCallback<T>` açıklanan [EventCallback](#eventcallback) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b7b43-189">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="b7b43-190">Aşağıdaki ana bileşen kullanan `ChildComponent` ve bağlar `ParentYear` üst parametresinden `Year` alt bileşen parametresi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-190">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="b7b43-191">Yükleme `ParentComponent` aşağıdaki biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b7b43-191">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="b7b43-192">Varsa değerini `ParentYear` düğmesini seçerek özelliği değiştirildiğinde `ParentComponent`, `Year` özelliği `ChildComponent` güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-192">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="b7b43-193">Öğesinin yeni değeri `Year` Arabiriminde işlenen olduğunda `ParentComponent` rerendered:</span><span class="sxs-lookup"><span data-stu-id="b7b43-193">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="b7b43-194">`Year` Bir yardımcı olduğundan parametre bağlanabilir `YearChanged` türüyle eşleşen olay `Year` parametresi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-194">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="b7b43-195">Kural olarak, `<ChildComponent @bind-Year="ParentYear" />` yazma temelde eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-195">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="b7b43-196">Genel olarak, bir özelliği karşılık gelen olay işleyicisi kullanarak bir bağlanabilir `@bind-property:event` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-196">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="b7b43-197">Örneğin, özellik `MyProp` bağlanabilir `MyEventHandler` aşağıdaki iki öznitelikleri kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b7b43-197">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="b7b43-198">Olay işleme</span><span class="sxs-lookup"><span data-stu-id="b7b43-198">Event handling</span></span>

<span data-ttu-id="b7b43-199">Razor bileşenleri olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-199">Razor components provide event handling features.</span></span> <span data-ttu-id="b7b43-200">İçin bir HTML öğesi öznitelik adlı `on<event>` (örneğin, `onclick` ve `onsubmit`) temsilci türü belirtilmiş bir değer ile Razor bileşenlerini değerlendirir özniteliğin değeri bir olay işleyicisi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-200">For an HTML element attribute named `on<event>` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="b7b43-201">Özniteliğin adı her zaman ile başlayan `@on`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-201">The attribute's name always starts with `@on`.</span></span>

<span data-ttu-id="b7b43-202">Aşağıdaki kod çağrıları `UpdateHeading` Arabiriminde düğme seçildiğinde yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-202">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="@UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="b7b43-203">Aşağıdaki kod çağrıları `CheckChanged` onay kutusunu kullanıcı Arabiriminde değiştirildiğinde yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-203">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="@CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="b7b43-204">Olay işleyicileri zaman uyumsuz ve dönüş ayrıca olabilir bir <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="b7b43-204">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="b7b43-205">El ile çağırmaya gerek yoktur `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-205">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="b7b43-206">Ortaya çıkan özel durumlar günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-206">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="b7b43-207">Aşağıdaki örnekte, `UpdateHeading` düğmesi seçili olduğunda zaman uyumsuz olarak adlandırılır:</span><span class="sxs-lookup"><span data-stu-id="b7b43-207">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="@UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="b7b43-208">Bazı olaylar için olaya özgü olay bağımsız değişken türleri de izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-208">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="b7b43-209">Bu olay türleri birine erişimi gerekli değilse, yöntem çağrısında gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-209">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="b7b43-210">Desteklenen [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-210">Supported [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) are shown in the following table.</span></span>

| <span data-ttu-id="b7b43-211">Olay</span><span class="sxs-lookup"><span data-stu-id="b7b43-211">Event</span></span> | <span data-ttu-id="b7b43-212">örneği</span><span class="sxs-lookup"><span data-stu-id="b7b43-212">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="b7b43-213">Pano</span><span class="sxs-lookup"><span data-stu-id="b7b43-213">Clipboard</span></span> | `UIClipboardEventArgs` |
| <span data-ttu-id="b7b43-214">Sürükle</span><span class="sxs-lookup"><span data-stu-id="b7b43-214">Drag</span></span>  | <span data-ttu-id="b7b43-215">`UIDragEventArgs` &ndash; `DataTransfer` bir Sürükle ve bırak işlemi sırasında sürüklenen verileri tutmak için kullanılır ve bir veya daha fazla tutabilir `UIDataTransferItem`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-215">`UIDragEventArgs` &ndash; `DataTransfer` is used to hold the dragged data during a drag and drop operation and may hold one or more `UIDataTransferItem`.</span></span> <span data-ttu-id="b7b43-216">`UIDataTransferItem` temsil eder bir veri öğesi sürükleyin.</span><span class="sxs-lookup"><span data-stu-id="b7b43-216">`UIDataTransferItem` represents one drag data item.</span></span> |
| <span data-ttu-id="b7b43-217">Hata</span><span class="sxs-lookup"><span data-stu-id="b7b43-217">Error</span></span> | `UIErrorEventArgs` |
| <span data-ttu-id="b7b43-218">Odağı</span><span class="sxs-lookup"><span data-stu-id="b7b43-218">Focus</span></span> | <span data-ttu-id="b7b43-219">`UIFocusEventArgs` &ndash; İçin destek içermez `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-219">`UIFocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="b7b43-220">`<input>` Değişiklik</span><span class="sxs-lookup"><span data-stu-id="b7b43-220">`<input>` change</span></span> | `UIChangeEventArgs` |
| <span data-ttu-id="b7b43-221">Klavye</span><span class="sxs-lookup"><span data-stu-id="b7b43-221">Keyboard</span></span> | `UIKeyboardEventArgs` |
| <span data-ttu-id="b7b43-222">Fare</span><span class="sxs-lookup"><span data-stu-id="b7b43-222">Mouse</span></span> | `UIMouseEventArgs` |
| <span data-ttu-id="b7b43-223">Fare işaretçisi</span><span class="sxs-lookup"><span data-stu-id="b7b43-223">Mouse pointer</span></span> | `UIPointerEventArgs` |
| <span data-ttu-id="b7b43-224">Fare tekerleği</span><span class="sxs-lookup"><span data-stu-id="b7b43-224">Mouse wheel</span></span> | `UIWheelEventArgs` |
| <span data-ttu-id="b7b43-225">İlerleme durumu</span><span class="sxs-lookup"><span data-stu-id="b7b43-225">Progress</span></span> | `UIProgressEventArgs` |
| <span data-ttu-id="b7b43-226">Dokunma</span><span class="sxs-lookup"><span data-stu-id="b7b43-226">Touch</span></span> | <span data-ttu-id="b7b43-227">`UITouchEventArgs` &ndash; `UITouchPoint` dokunmaya duyarlı cihazda tek bir iletişim noktası temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b7b43-227">`UITouchEventArgs` &ndash; `UITouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="b7b43-228">Özellikleri ve olay davranışını önceki tabloda olayları işleme hakkında daha fazla bilgi için bkz: [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) başvuru kaynak.</span><span class="sxs-lookup"><span data-stu-id="b7b43-228">For information on the properties and event handling behavior of the events in the preceding table, see [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) in the reference source.</span></span>
  
<span data-ttu-id="b7b43-229">Lambda ifadeleri de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-229">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="b7b43-230">Genellikle gibi ek değerler kapatmak uygun olan öğeleri kümesi yineleme olduğunda.</span><span class="sxs-lookup"><span data-stu-id="b7b43-230">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="b7b43-231">Aşağıdaki örnek, üç oluşturur düğmeler, her biri çağıran `UpdateHeading` olay bağımsız değişken geçirme (`UIMouseEventArgs`) ve düğme sayısı (`buttonNumber`) kullanıcı Arabiriminde seçili olduğunda:</span><span class="sxs-lookup"><span data-stu-id="b7b43-231">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="b7b43-232">Yapmak **değil** Döngü değişkeninin kullanın (`i`) içinde bir `for` bir lambda ifadesinde doğrudan döngü.</span><span class="sxs-lookup"><span data-stu-id="b7b43-232">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="b7b43-233">Aksi takdirde aynı değişkene neden tüm lambda ifadeleri tarafından kullanılan `i`değerini tüm lambda içinde ile aynı.</span><span class="sxs-lookup"><span data-stu-id="b7b43-233">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="b7b43-234">Yerel bir değişkende değeri her zaman yakalama (`buttonNumber` önceki örnekte) ve ardından kullanın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-234">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="b7b43-235">EventCallback</span><span class="sxs-lookup"><span data-stu-id="b7b43-235">EventCallback</span></span>

<span data-ttu-id="b7b43-236">İç içe geçmiş bileşen sık karşılaşılan bir senaryodur arzusu bir alt bileşen olay gerçekleştiğinde bir ana bileşenin yöntemini çalıştırılacak olan&mdash;Örneğin, bir `onclick` olay alt gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-236">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="b7b43-237">Bileşenlerinde olaylar oluşturmak için kullanmak bir `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-237">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="b7b43-238">Ana bileşenin bir alt bileşen için bir geri çağırma yöntemi atayabilirsiniz `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-238">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="b7b43-239">`ChildComponent` Örnekte bir düğme nasıl ait uygulama gösterilmektedir `onclick` işleyici ayarlandığından almak için bir `EventCallback` temsilci örnekten 's `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-239">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="b7b43-240">`EventCallback` İle yazılan `UIMouseEventArgs`, uygun olduğu bir `onclick` çevre cihazı olay:</span><span class="sxs-lookup"><span data-stu-id="b7b43-240">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="b7b43-241">`ParentComponent` Çocuğun ayarlar `EventCallback<T>` için kendi `ShowMessage` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-241">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="b7b43-242">İçinde düğme seçildiğinde `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="b7b43-242">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="b7b43-243">`ParentComponent`'S `ShowMessage` yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-243">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="b7b43-244">`messageText` Güncelleştirilen ve görüntülenen `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-244">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="b7b43-245">Bir çağrı `StateHasChanged` geri çağırma'nın yöntemi gerekli değildir (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="b7b43-245">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="b7b43-246">`StateHasChanged` rerender için otomatik olarak çağrılan `ParentComponent`, bileşen içinde alt yürütme olay işleyicilerinde rerendering alt olaylarını tetiklemek gibi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-246">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="b7b43-247">`EventCallback` ve `EventCallback<T>` zaman uyumsuz temsilciler izin verir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-247">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="b7b43-248">`EventCallback<T>` türü kesin olarak belirtilmiş ve belirli bir bağımsız değişken türü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-248">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="b7b43-249">`EventCallback` zayıf yazılmış ve herhangi bir bağımsız değişken türü sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-249">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="b7b43-250">Çağırma bir `EventCallback` veya `EventCallback<T>` ile `InvokeAsync` ve await <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="b7b43-250">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="b7b43-251">Kullanım `EventCallback` ve `EventCallback<T>` olay işleme ve bileşen parametre bağlama için.</span><span class="sxs-lookup"><span data-stu-id="b7b43-251">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="b7b43-252">Kesin olarak belirlenmiş tercih `EventCallback<T>` üzerinden `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-252">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="b7b43-253">`EventCallback<T>` Bileşen kullanıcıları için daha iyi hata geri bildirim sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-253">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="b7b43-254">Diğer UI olay işleyicilerine benzer, olay parametresi belirten isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-254">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="b7b43-255">Kullanım `EventCallback` çağırma işlemine geçirilen değer olduğunda.</span><span class="sxs-lookup"><span data-stu-id="b7b43-255">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="b7b43-256">Bileşenleri başvurular yakalama</span><span class="sxs-lookup"><span data-stu-id="b7b43-256">Capture references to components</span></span>

<span data-ttu-id="b7b43-257">Bileşen başvurularını komutları gibi bu örneğe verebilir böylece bileşen örneğinin başvurmak için bir yol sağlar `Show` veya `Reset`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-257">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="b7b43-258">Bir bileşen başvurusunu yakalamak için ekleme bir `@ref` özniteliği alt bileşen ve aynı ada ve aynı türe sahip bir alan alt bileşeni olarak tanımlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b7b43-258">To capture a component reference, add a `@ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

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

<span data-ttu-id="b7b43-259">Bileşen işlendiğinde `loginDialog` alanı ile doldurulur `MyLoginDialog` alt bileşen örneği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-259">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="b7b43-260">Ardından bileşen örneği üzerinde .NET yöntemlerini çağırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b7b43-260">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b7b43-261">`loginDialog` Değişkeni bileşeni işlenir ve çıktısını içeren sonra yalnızca doldurulmuş `MyLoginDialog` öğesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-261">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="b7b43-262">O noktaya kadar hiçbir şey yoktur başvurmak için.</span><span class="sxs-lookup"><span data-stu-id="b7b43-262">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="b7b43-263">Bileşen oluşturma işlemini tamamladıktan sonra bileşenleri başvurularını değiştirmek üzere `OnAfterRenderAsync` veya `OnAfterRender` yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-263">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="b7b43-264">Bileşen başvurularını yakalarken kullanmak için benzer bir sözdizimi [öğesi başvuruları yakalama](xref:blazor/javascript-interop#capture-references-to-elements), öyle bir [JavaScript birlikte çalışma](xref:blazor/javascript-interop) özelliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-264">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="b7b43-265">Bileşen başvurularını JavaScript kodunu geçirilen olmayan&mdash;yalnızca .NET kodda kullanılırlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-265">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="b7b43-266">Yapmak **değil** alt bileşenlerin durumunu kesilecek bileşen başvuruları kullanın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-266">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="b7b43-267">Bunun yerine, normal bildirim temelli parametreler alt bileşenler için veri aktarmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-267">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="b7b43-268">Normal bildirim temelli parametreler sonucunda otomatik olarak doğru zamanlarda rerender alt bileşenlerle kullanma.</span><span class="sxs-lookup"><span data-stu-id="b7b43-268">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="b7b43-269">Kullanım @key öğeleri ve bileşenleri korunması denetlemek için</span><span class="sxs-lookup"><span data-stu-id="b7b43-269">Use @key to control the preservation of elements and components</span></span>

<span data-ttu-id="b7b43-270">Öğeleri veya bileşenleri ve öğeleri veya bileşenleri listesini sonradan işleme değiştirdiğinizde Blazor'ın ayırırken algoritması, önceki öğeleri veya bileşenleri tutulabilir ve model nesneleri için bunları nasıl eşlemelisiniz karar vermeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-270">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="b7b43-271">Genellikle bu işlemi otomatik olarak yüklenir ve göz ardı edilebilir, ancak işlemini denetlemek için burada isteyebileceğiniz durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-271">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="b7b43-272">Aşağıdaki örnek göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="b7b43-272">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b7b43-273">İçeriğini `People` koleksiyon değişebilir ile eklenen, silinen veya Girişleri'yeniden sıralanabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-273">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="b7b43-274">Bileşen rerenders, `<DetailsEditor>` bileşeni farklı almak için değişebilir `Details` parametre değerleri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-274">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="b7b43-275">Bu işlem, beklenenden daha karmaşık rerendering neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-275">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="b7b43-276">Bazı durumlarda, rerendering kayıp öğesi odak gibi görünür davranış farklılıklarının neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-276">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="b7b43-277">Eşleme işlemi ile denetlenebilir `@key` yönerge özniteliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-277">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="b7b43-278">`@key` öğeleri veya bileşenleri anahtarın değerine göre korunmasını sağlamak fark alma işlemini algoritması neden olur:</span><span class="sxs-lookup"><span data-stu-id="b7b43-278">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="b7b43-279">Zaman `People` koleksiyonu değişiklikleri ayırırken algoritması korur arasındaki ilişkiyi `<DetailsEditor>` örnekleri ve `person` örnekleri:</span><span class="sxs-lookup"><span data-stu-id="b7b43-279">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="b7b43-280">Varsa bir `Person` hizmetinden silinir `People` listesi, yalnızca ilgili `<DetailsEditor>` örneği, kullanıcı Arabiriminden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-280">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="b7b43-281">Diğer örnekleri sol değişmez.</span><span class="sxs-lookup"><span data-stu-id="b7b43-281">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b7b43-282">Varsa bir `Person` listesinde yeni bir tane bazı konumunda eklenen `<DetailsEditor>` örneğine karşılık gelen o konumdan eklenir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-282">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="b7b43-283">Diğer örnekleri sol değişmez.</span><span class="sxs-lookup"><span data-stu-id="b7b43-283">Other instances are left unchanged.</span></span>
* <span data-ttu-id="b7b43-284">Varsa `Person` girişleri yeniden sıralanır, karşılık gelen `<DetailsEditor>` örnekleri korunur ve kullanıcı Arabiriminde yeniden sıralanabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-284">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="b7b43-285">Bazı senaryolarda kullanım `@key` rerendering karmaşıklığını en aza indirir ve durum bilgisi olan bölümleri DOM, odak konumu gibi değiştirme ile ilgili olası sorunları önler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-285">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b7b43-286">Anahtarlar, her bir kapsayıcı öğe veya bileşeni için yereldir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-286">Keys are local to each container element or component.</span></span> <span data-ttu-id="b7b43-287">Anahtarları genel belge boyunca karşılaştırıldığında değildir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-287">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="b7b43-288">Ne zaman kullanılır? @key</span><span class="sxs-lookup"><span data-stu-id="b7b43-288">When to use @key</span></span>

<span data-ttu-id="b7b43-289">Genellikle, kullanılacak mantıklıdır `@key` listesini işlenen her (örneğin, bir `@foreach` bloğu) ve tanımlamak için uygun bir değer var. `@key`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-289">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="b7b43-290">Ayrıca `@key` bir nesne değiştirildiğinde bir öğe veya bileşen alt koruma gelen Blazor önlemek için:</span><span class="sxs-lookup"><span data-stu-id="b7b43-290">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="b7b43-291">Varsa `@currentPerson` değişiklikleri `@key` özniteliği yönergesi, tüm atmak Blazor zorlar `<div>` ve alt öğeler ve alt ağacı içinde yeni öğeler ve bileşenleri ile kullanıcı arabirimini yeniden oluşturma.</span><span class="sxs-lookup"><span data-stu-id="b7b43-291">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="b7b43-292">Bu, hiçbir kullanıcı Arabirimi durumu ne zaman korunduğunu garantilemeye ihtiyacınız varsa yararlı olabilir `@currentPerson` değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-292">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="b7b43-293">Kullanılmayacağı zaman @key</span><span class="sxs-lookup"><span data-stu-id="b7b43-293">When not to use @key</span></span>

<span data-ttu-id="b7b43-294">Performans maliyetine ne zaman yoktur ile ayırırken `@key`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-294">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="b7b43-295">Performans maliyetini büyük değildir, ancak yalnızca belirtin `@key` öğe veya bileşen korunması denetleme kuralları yararlı uygulama ise.</span><span class="sxs-lookup"><span data-stu-id="b7b43-295">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="b7b43-296">Bile `@key` değil kullanılan Blazor alt öğe ve bileşen örnekleri mümkün olduğunca korur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-296">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="b7b43-297">Yalnızca önyüklenebildiği `@key` üzerinden denetimdir *nasıl* modeli örnek eşleme seçme ayırırken algoritması yerine korunan bileşen örneklerine eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-297">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="b7b43-298">Hangi için kullanılacak değerleri @key</span><span class="sxs-lookup"><span data-stu-id="b7b43-298">What values to use for @key</span></span>

<span data-ttu-id="b7b43-299">Aşağıdaki tür için değer birini sağlamak mantıklı genellikle `@key`:</span><span class="sxs-lookup"><span data-stu-id="b7b43-299">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="b7b43-300">Model nesne örnekleri (örneğin, bir `Person` örneği önceki örnekte olduğu gibi).</span><span class="sxs-lookup"><span data-stu-id="b7b43-300">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="b7b43-301">Bu, üzerinde nesne başvuru eşitliğine dayalı korunmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-301">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="b7b43-302">Benzersiz tanımlayıcıları (örneğin, birincil anahtar değerlerini türü `int`, `string`, veya `Guid`).</span><span class="sxs-lookup"><span data-stu-id="b7b43-302">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="b7b43-303">Beklenmedik bir şekilde bırakmayan bir değeri sağlama kaçının.</span><span class="sxs-lookup"><span data-stu-id="b7b43-303">Avoid supplying a value that can clash unexpectedly.</span></span> <span data-ttu-id="b7b43-304">Varsa `@key="@someObject.GetHashCode()"` karma kodları ilgisiz nesnelerin aynı olabileceğinden, beklenmeyen çakışmaları oluşabilir sağlanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-304">If `@key="@someObject.GetHashCode()"` is supplied, unexpected clashes may occur because the hash codes of unrelated objects can be the same.</span></span> <span data-ttu-id="b7b43-305">Çakışan varsa `@key` değerleri aynı üst öğe içinde istenen `@key` değerleri olmaz dikkate alınır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-305">If clashing `@key` values are requested within the same parent, the `@key` values won't be honored.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="b7b43-306">Yaşam döngüsü yöntemleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-306">Lifecycle methods</span></span>

<span data-ttu-id="b7b43-307">`OnInitAsync` ve `OnInit` bileşeni başlatmak için kod yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-307">`OnInitAsync` and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="b7b43-308">Zaman uyumsuz bir işlemi gerçekleştirmek için `OnInitAsync` ve `await` anahtar sözcüğü, işlemi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-308">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="b7b43-309">Eşzamanlı bir işlem için kullanmak `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="b7b43-309">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="b7b43-310">`OnParametersSetAsync` ve `OnParametersSet` bir bileşen üst öğesinden parametreleri aldı ve özelliklerine değerler atanır çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-310">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="b7b43-311">Bu yöntemler bileşen başlatmadan sonra yürütülür ve her bileşenin oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="b7b43-311">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="b7b43-312">`OnAfterRenderAsync` ve `OnAfterRender` bir bileşen oluşturma tamamlandıktan sonra çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-312">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="b7b43-313">Öğe ve bileşen başvuruları bu noktada doldurulur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-313">Element and component references are populated at this point.</span></span> <span data-ttu-id="b7b43-314">Bu aşama, işlenmiş DOM öğeleri üzerinde çalışan üçüncü taraf JavaScript kitaplıklarını etkinleştirme gibi işlenmiş içeriği kullanarak ek başlatma adımları gerçekleştirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-314">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="b7b43-315">Tamamlanmamış zaman uyumsuz işleme eylemlerini işlemek</span><span class="sxs-lookup"><span data-stu-id="b7b43-315">Handle incomplete async actions at render</span></span>

<span data-ttu-id="b7b43-316">Yaşam döngüsü olayları zaman uyumsuz eylemlerin bileşeni işlenmeden önce tamamlanmamış olabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-316">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="b7b43-317">Nesneleri olabilir `null` veya yaşam döngüsü metodu yürütülürken verilerle doldurulmuş önişlemcinin.</span><span class="sxs-lookup"><span data-stu-id="b7b43-317">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="b7b43-318">Nesneleri başlatıldığını onaylamak için işleme mantığı sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-318">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="b7b43-319">Yer tutucu nesneleri sırasında kullanıcı Arabirimi öğeleri (örneğin, bir yükleme iletisi) işleme olan `null`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-319">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="b7b43-320">İçinde `FetchData` Blazor şablonlarının bileşen `OnInitAsync` kopyalanmamasına için geçersiz kılındı tahmin veri alma (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="b7b43-320">In the `FetchData` component of the Blazor templates, `OnInitAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="b7b43-321">Zaman `forecasts` olduğu `null`, kullanıcıya yüklenirken bir ileti görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-321">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="b7b43-322">Sonra `Task` tarafından döndürülen `OnInitAsync` tamamlandıktan, bileşen, güncelleştirilmiş durumuyla rerendered.</span><span class="sxs-lookup"><span data-stu-id="b7b43-322">After the `Task` returned by `OnInitAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="b7b43-323">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-323">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="b7b43-324">Parametreleri ayarlamadan önce kod yürütme</span><span class="sxs-lookup"><span data-stu-id="b7b43-324">Execute code before parameters are set</span></span>

<span data-ttu-id="b7b43-325">`SetParameters` parametreleri ayarlamadan önce kodu çalıştırmak için geçersiz kılınabilir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-325">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="b7b43-326">Varsa `base.SetParameters` değilse çağrılır, özel kod yolu gerekli gelen parametre değeri yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-326">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="b7b43-327">Örneğin, gelen parametreleri sınıfındaki özellikleri atanması için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-327">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="b7b43-328">Kullanıcı Arabiriminde yenileme Gizle</span><span class="sxs-lookup"><span data-stu-id="b7b43-328">Suppress refreshing of the UI</span></span>

<span data-ttu-id="b7b43-329">`ShouldRender` Kullanıcı Arabiriminde yenilemeyi engellemek için geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-329">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="b7b43-330">Uygulama döndürürse `true`, UI yenilenir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-330">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="b7b43-331">Bile `ShouldRender` olan geçersiz kılınan, bileşen her zaman başlangıçta işlenir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-331">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="b7b43-332">IDisposable ile bileşen elden çıkarma</span><span class="sxs-lookup"><span data-stu-id="b7b43-332">Component disposal with IDisposable</span></span>

<span data-ttu-id="b7b43-333">Bir bileşen uyguluyorsa <xref:System.IDisposable>, [Dispose yöntemini](/dotnet/standard/garbage-collection/implementing-dispose) bileşeni Arabiriminden kaldırıldığında çağrılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-333">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="b7b43-334">Aşağıdaki bileşen `@implements IDisposable` ve `Dispose` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-334">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="b7b43-335">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="b7b43-335">Routing</span></span>

<span data-ttu-id="b7b43-336">İçinde Blazor yönlendirme uygulamasında erişilebilir her bileşeni için bir rota şablonu sağlayarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-336">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="b7b43-337">Bir Razor dosya ile bir `@page` yönergesi derlendiğinde, oluşturulan sınıfın belirli bir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> belirten rota şablonu.</span><span class="sxs-lookup"><span data-stu-id="b7b43-337">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="b7b43-338">Çalışma zamanında bileşen sınıfları ile yönlendirici arar bir `RouteAttribute` ve hangi bileşen istenen URL ile eşleşen bir rota şablonuna sahip işler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-338">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="b7b43-339">Bir bileşenin birden çok yol şablonu uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-339">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="b7b43-340">Aşağıdaki bileşen isteklerine yanıt veren `/BlazorRoute` ve `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="b7b43-340">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="b7b43-341">Yol parametreleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-341">Route parameters</span></span>

<span data-ttu-id="b7b43-342">Sağlanan yol şablondan, rota parametrelerinin bileşenleri alabilir `@page` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-342">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="b7b43-343">Yönlendirici, karşılık gelen bileşen parametreleri doldurmak için rota parametrelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-343">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="b7b43-344">*Rota parametresinin bileşen*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-344">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="b7b43-345">İsteğe bağlı parametreler desteklenmez, bu nedenle iki `@page` yönergeleri, yukarıdaki örnekte uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-345">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="b7b43-346">İlk Gezinti parametresi olmadan bileşenine izin verir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-346">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="b7b43-347">İkinci `@page` yönergesi gereken `{text}` rota parametresi ve değeri atar `Text` özelliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-347">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="b7b43-348">Bir "code-behind" deneyimi için temel sınıf devralma</span><span class="sxs-lookup"><span data-stu-id="b7b43-348">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="b7b43-349">Bileşen dosyaları karıştırmak HTML biçimlendirmesi ve C# aynı dosyada kod işleme.</span><span class="sxs-lookup"><span data-stu-id="b7b43-349">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="b7b43-350">`@inherits` Yönergesi, bileşen biçimlendirme işleme koddan ayıran bir "code-behind" deneyimiyle Blazor uygulamaları sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-350">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="b7b43-351">[Örnek uygulaması](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) nasıl bir bileşen bir temel sınıf devralma işlemi yapabileceğini gösterir `BlazorRocksBase`, bileşenin özellikleri ve yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-351">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="b7b43-352">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-352">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="b7b43-353">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-353">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="b7b43-354">Temel sınıfın türetilmesi `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-354">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="b7b43-355">İçeri aktarma bileşenleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-355">Import components</span></span>

<span data-ttu-id="b7b43-356">Ad alanı, Razor ile yazılmış bir bileşenin dayanır:</span><span class="sxs-lookup"><span data-stu-id="b7b43-356">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="b7b43-357">Projenin `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-357">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="b7b43-358">Bileşen yolu proje kök.</span><span class="sxs-lookup"><span data-stu-id="b7b43-358">The path from the project root to the component.</span></span> <span data-ttu-id="b7b43-359">Örneğin, `ComponentsSample/Pages/Index.razor` ad alanındaki `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-359">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="b7b43-360">Bileşenleri izleyin C# bağlama kurallarını olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-360">Components follow C# name binding rules.</span></span> <span data-ttu-id="b7b43-361">Durumunda, *Index.razor*, tüm bileşenleri aynı klasörde *sayfaları*ve üst klasör *ComponentsSample*, kapsam içindedir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-361">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="b7b43-362">Farklı bir ad alanında tanımlanan bileşenleri Razor'ın kullanarak kapsamına alınabilir [ \@kullanarak](xref:mvc/views/razor#using) yönergesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-362">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="b7b43-363">Başka bir bileşen `NavMenu.razor`, klasörde mevcut `ComponentsSample/Shared/`, bileşen kullanılabilir `Index.razor` aşağıdaki `@using` deyimi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-363">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="b7b43-364">Bileşenleri de başvurabilir bunların tam nitelikli adlarını kullanarak gereksinimini kaldıran [ \@kullanarak](xref:mvc/views/razor#using) yönergesi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-364">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="b7b43-365">`global::` Nitelik desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b7b43-365">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="b7b43-366">Diğer adlı bileşenleriyle alma `using` deyimleri (örneğin, `@using Foo = Bar`) desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b7b43-366">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="b7b43-367">Kısmen nitelenmiş adlar desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b7b43-367">Partially qualified names aren't supported.</span></span> <span data-ttu-id="b7b43-368">Örneğin, ekleme `@using ComponentsSample` ve bunlara başvurma `NavMenu.razor` ile `<Shared.NavMenu></Shared.NavMenu>` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b7b43-368">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="razor-support"></a><span data-ttu-id="b7b43-369">Razor desteği</span><span class="sxs-lookup"><span data-stu-id="b7b43-369">Razor support</span></span>

<span data-ttu-id="b7b43-370">**Razor yönergesi**</span><span class="sxs-lookup"><span data-stu-id="b7b43-370">**Razor directives**</span></span>

<span data-ttu-id="b7b43-371">Razor yönergeleri, aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-371">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="b7b43-372">Yönergesi</span><span class="sxs-lookup"><span data-stu-id="b7b43-372">Directive</span></span> | <span data-ttu-id="b7b43-373">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b7b43-373">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="b7b43-374">\@Kod</span><span class="sxs-lookup"><span data-stu-id="b7b43-374">\@code</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="b7b43-375">Ekler bir C# bileşenine kod bloğu.</span><span class="sxs-lookup"><span data-stu-id="b7b43-375">Adds a C# code block to a component.</span></span> <span data-ttu-id="b7b43-376">`@code` bir diğer adıdır `@functions`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-376">`@code` is an alias of `@functions`.</span></span> <span data-ttu-id="b7b43-377">`@code` üzerinden önerilen `@functions`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-377">`@code` is recommended over `@functions`.</span></span> <span data-ttu-id="b7b43-378">Birden fazla `@code` bloğu izin verilebilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-378">More than one `@code` block is permissible.</span></span> |
| [<span data-ttu-id="b7b43-379">\@İşlevleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-379">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="b7b43-380">Ekler bir C# bileşenine kod bloğu.</span><span class="sxs-lookup"><span data-stu-id="b7b43-380">Adds a C# code block to a component.</span></span> <span data-ttu-id="b7b43-381">Seçin `@code` üzerinden `@functions` için C# kod blokları.</span><span class="sxs-lookup"><span data-stu-id="b7b43-381">Choose `@code` over `@functions` for C# code blocks.</span></span> |
| `@implements` | <span data-ttu-id="b7b43-382">Oluşturulan bileşen sınıfı için bir arabirim uygular.</span><span class="sxs-lookup"><span data-stu-id="b7b43-382">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="b7b43-383">\@Devralan</span><span class="sxs-lookup"><span data-stu-id="b7b43-383">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="b7b43-384">Bileşen devralan sınıf tam denetim sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-384">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="b7b43-385">\@ekleme</span><span class="sxs-lookup"><span data-stu-id="b7b43-385">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="b7b43-386">Hizmet ekleme gelen etkinleştirir [hizmet kapsayıcı](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b7b43-386">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b7b43-387">Daha fazla bilgi için [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b7b43-387">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="b7b43-388">Bir düzen bileşeni belirtir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-388">Specifies a layout component.</span></span> <span data-ttu-id="b7b43-389">Düzen bileşenleri, kod yinelemesi ve tutarsızlık önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-389">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="b7b43-390">\@Sayfa</span><span class="sxs-lookup"><span data-stu-id="b7b43-390">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="b7b43-391">Bileşen doğrudan istekleri işleyeceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-391">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="b7b43-392">`@page` Yönergesi, bir rota ve isteğe bağlı parametreler ile belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-392">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="b7b43-393">Razor sayfaları aksine `@page` yönergesi üst dosyanın ilk yönerge olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b7b43-393">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="b7b43-394">Daha fazla bilgi için [yönlendirme](xref:blazor/routing).</span><span class="sxs-lookup"><span data-stu-id="b7b43-394">For more information, see [Routing](xref:blazor/routing).</span></span> |
| [<span data-ttu-id="b7b43-395">\@kullanma</span><span class="sxs-lookup"><span data-stu-id="b7b43-395">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="b7b43-396">Ekler C# `using` yönergesini oluşturulan bileşen sınıfı.</span><span class="sxs-lookup"><span data-stu-id="b7b43-396">Adds the C# `using` directive to the generated component class.</span></span> <span data-ttu-id="b7b43-397">Bu kapsamın içine o ad alanında tanımlanan tüm bileşenleri de getirir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-397">This also brings all the components defined in that namespace into scope.</span></span> |
| [<span data-ttu-id="b7b43-398">\@Namespace</span><span class="sxs-lookup"><span data-stu-id="b7b43-398">\@namespace</span></span>](xref:mvc/views/razor#section-6) | <span data-ttu-id="b7b43-399">Ad alanı oluşturulan bileşen sınıfının ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-399">Sets the namespace of the generated component class.</span></span> |
| [<span data-ttu-id="b7b43-400">\@Özniteliği</span><span class="sxs-lookup"><span data-stu-id="b7b43-400">\@attribute</span></span>](xref:mvc/views/razor#section-7) | <span data-ttu-id="b7b43-401">Bir öznitelik için oluşturulan bileşen sınıfı ekler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-401">Adds an attribute to the generated component class.</span></span> |

<span data-ttu-id="b7b43-402">**Koşullu HTML öğesi özniteliklerini**</span><span class="sxs-lookup"><span data-stu-id="b7b43-402">**Conditional HTML element attributes**</span></span>

<span data-ttu-id="b7b43-403">HTML öğesi özniteliklerini .NET değerine göre koşullu olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-403">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="b7b43-404">Değer ise `false` veya `null`, öznitelik işlenen değil.</span><span class="sxs-lookup"><span data-stu-id="b7b43-404">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="b7b43-405">Değer ise `true`, öznitelik işlenen simge.</span><span class="sxs-lookup"><span data-stu-id="b7b43-405">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="b7b43-406">Aşağıdaki örnekte, `IsCompleted` belirler `checked` öğenin biçimlendirmesi içinde işlenir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-406">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="b7b43-407">Varsa `IsCompleted` olduğu `true`, onay kutusunu olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-407">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="b7b43-408">Varsa `IsCompleted` olduğu `false`, onay kutusunu olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-408">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="b7b43-409">**Razor hakkında daha fazla bilgi**</span><span class="sxs-lookup"><span data-stu-id="b7b43-409">**Additional information on Razor**</span></span>

<span data-ttu-id="b7b43-410">Razor hakkında daha fazla bilgi için bkz. [Razor söz dizimi başvurusu](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="b7b43-410">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="b7b43-411">Ham HTML</span><span class="sxs-lookup"><span data-stu-id="b7b43-411">Raw HTML</span></span>

<span data-ttu-id="b7b43-412">Dizeler genellikle DOM metin düğümleri kullanarak içeriyor olabilir herhangi bir biçimlendirme yok sayıldı ve düz metin kabul yani işlenir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-412">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="b7b43-413">Ham HTML oluşturmak için bir HTML içeriği kaydırma bir `MarkupString` değeri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-413">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="b7b43-414">Değer HTML veya SVG ayrıştırılması ve DOM'a eklenmesi</span><span class="sxs-lookup"><span data-stu-id="b7b43-414">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="b7b43-415">Herhangi bir yapıda ham HTML'yi işlemeye güvenilmeyen kaynak bir **güvenlik riski** ve kaçınılmalıdır!</span><span class="sxs-lookup"><span data-stu-id="b7b43-415">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="b7b43-416">Aşağıdaki örnek, gösterir kullanarak `MarkupString` bir bileşenin işlenen çıkışı için bir statik HTML içeriği bloğunu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b7b43-416">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="b7b43-417">Şablonlu bileşenleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-417">Templated components</span></span>

<span data-ttu-id="b7b43-418">Şablonlu bileşenleri bileşenin işleme mantığı bir parçası olarak kullanılabilir bir parametre olarak bir veya daha fazla kullanıcı Arabirimi şablonları kabul bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-418">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="b7b43-419">Şablonlu bileşenleri normal bileşenleri daha fazla yeniden kullanılabilir olan üst düzey bileşeni yazmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-419">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="b7b43-420">Birkaç örnek şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b7b43-420">A couple of examples include:</span></span>

* <span data-ttu-id="b7b43-421">Bir tablonun üst bilgi, satırları ve alt bilgisi için şablonları belirtmesine imkan tanıyan bir tablo bileşeni.</span><span class="sxs-lookup"><span data-stu-id="b7b43-421">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="b7b43-422">Bir listedeki öğeleri işleme için bir şablon belirtmesine imkan tanıyan bir liste bileşeni.</span><span class="sxs-lookup"><span data-stu-id="b7b43-422">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="b7b43-423">Şablon parametreleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-423">Template parameters</span></span>

<span data-ttu-id="b7b43-424">Şablonlu bir bileşen türünde bir veya daha fazla bileşen parametreleri belirterek tanımlanmış `RenderFragment` veya `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-424">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="b7b43-425">Bir işleme parça bileşen tarafından işlenen kullanıcı arabiriminin bir kesimi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="b7b43-425">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="b7b43-426">Bir işleme parça, isteğe bağlı olarak işleme parça çağrıldığında belirtilebilecek bir parametre alır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-426">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="b7b43-427">`TableTemplate` Bileşen:</span><span class="sxs-lookup"><span data-stu-id="b7b43-427">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="b7b43-428">Şablonlu bir bileşen kullanırken, şablon parametrelerinin parametrelerinin adları eşleşen alt öğeleri kullanılarak belirtilebilir (`TableHeader` ve `RowTemplate` aşağıdaki örnekte):</span><span class="sxs-lookup"><span data-stu-id="b7b43-428">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="b7b43-429">Şablon bağlam parametreleri</span><span class="sxs-lookup"><span data-stu-id="b7b43-429">Template context parameters</span></span>

<span data-ttu-id="b7b43-430">Bileşen Türü bağımsız değişkenleri `RenderFragment<T>` öğeleri adlı örtük bir parametre olarak geçirilen `context` (örneğin önceki kod örneğinde,'den `@context.PetId`), ancak parametre adını kullanarak değiştirebilirsiniz `Context` alt özniteliği öğe.</span><span class="sxs-lookup"><span data-stu-id="b7b43-430">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="b7b43-431">Aşağıdaki örnekte, `RowTemplate` öğenin `Context` özniteliği belirtir `pet` parametresi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-431">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="b7b43-432">Alternatif olarak, belirtebileceğiniz `Context` bileşen öğesindeki özniteliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-432">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="b7b43-433">Belirtilen `Context` özniteliği için tüm belirtilen şablon parametreleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-433">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="b7b43-434">Örtük alt içeriğin içerik parametresi adını belirlemek istediğinizde bu kullanışlı olabilir (herhangi bir sarmalama olmadan alt öğesi).</span><span class="sxs-lookup"><span data-stu-id="b7b43-434">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="b7b43-435">Aşağıdaki örnekte, `Context` özniteliği görünür `TableTemplate` öğenin ve tüm şablon parametreleri için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-435">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="b7b43-436">Genel yazılmış bileşenler</span><span class="sxs-lookup"><span data-stu-id="b7b43-436">Generic-typed components</span></span>

<span data-ttu-id="b7b43-437">Şablonlu bileşenleri çoğunlukla genel olarak yazılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-437">Templated components are often generically typed.</span></span> <span data-ttu-id="b7b43-438">Örneğin, genel `ListViewTemplate` bileşeni oluşturmak için kullanılabilir `IEnumerable<T>` değerleri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-438">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="b7b43-439">Genel bileşen tanımlamak için `@typeparam` tür parametreleri belirtmek için:</span><span class="sxs-lookup"><span data-stu-id="b7b43-439">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="b7b43-440">Tür parametresi, genel yazılmış bileşenler kullanırken, mümkünse algılanır:</span><span class="sxs-lookup"><span data-stu-id="b7b43-440">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="b7b43-441">Aksi takdirde, tür parametresi açık tür parametresinin adıyla eşleşen bir özniteliği kullanılarak belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-441">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="b7b43-442">Aşağıdaki örnekte, `TItem="Pet"` türünü belirtir:</span><span class="sxs-lookup"><span data-stu-id="b7b43-442">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="b7b43-443">Geçişli değerleri ve parametreler</span><span class="sxs-lookup"><span data-stu-id="b7b43-443">Cascading values and parameters</span></span>

<span data-ttu-id="b7b43-444">Bazı senaryolarda, akış verileri kullanarak bir alt bileşen bir üst bileşeninden kullanışsız [bileşeni parametreleri](#component-parameters), özellikle birçok bileşen katmanları olduğunda.</span><span class="sxs-lookup"><span data-stu-id="b7b43-444">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="b7b43-445">Geçişli değerleri ve parametre bir üst bileşeni tüm alt öğe bileşenleri için bir değer sağlamak için kullanışlı bir yol sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="b7b43-445">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="b7b43-446">Ayrıca basamaklı değerleri ve parametreler koordine etmek, bileşenler için bir yaklaşım sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-446">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="b7b43-447">Tema örnek</span><span class="sxs-lookup"><span data-stu-id="b7b43-447">Theme example</span></span>

<span data-ttu-id="b7b43-448">Aşağıdaki örnekte, örnek uygulamadan `ThemeInfo` sınıfı, böylece tüm düğmelerin belirli bir uygulamanın parçası içinde aynı stili paylaşır bileşen hiyerarşisi akış için tema bilgileri belirtir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-448">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="b7b43-449">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-449">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="b7b43-450">Üst bileşeni, geçişli değeri bileşenini kullanarak basamaklı bir değer sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b7b43-450">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="b7b43-451">`CascadingValue` Bileşeni bileşen hiyerarşisi bir alt ağacı sarmalar ve o alt ağacı içinde tüm bileşenleri tek bir değer sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-451">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="b7b43-452">Örneğin, örnek uygulamayı tema bilgileri belirtir (`ThemeInfo`) bir düzen gövdesini oluşturan tüm bileşenlerin basamaklı bir parametre olarak uygulamanın düzenleri `@Body` özelliği.</span><span class="sxs-lookup"><span data-stu-id="b7b43-452">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="b7b43-453">`ButtonClass` bir değeri atanır `btn-success` Düzen bileşende.</span><span class="sxs-lookup"><span data-stu-id="b7b43-453">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="b7b43-454">Bu özelliği üzerinden herhangi bir alt bileşen tüketebileceği `ThemeInfo` basamaklı nesnesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-454">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="b7b43-455">`CascadingValuesParametersLayout` Bileşen:</span><span class="sxs-lookup"><span data-stu-id="b7b43-455">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="b7b43-456">Yapmak için geçişli değerlerini kullanmak, bileşenlerini kullanarak basamaklı parametreleri bildirmek `[CascadingParameter]` özniteliği veya bir dize adı değerine göre:</span><span class="sxs-lookup"><span data-stu-id="b7b43-456">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="b7b43-457">Bir dize adı değeri bağlamayla aynı türde birden fazla basamaklı değerler varsa ve aynı alt ağacı içinde ayrılmaları gerekiyorsa geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-457">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="b7b43-458">Basamaklı değerler türüne göre geçişli parametrelerine bağlı.</span><span class="sxs-lookup"><span data-stu-id="b7b43-458">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="b7b43-459">Örnek uygulamada `CascadingValuesParametersTheme` bileşeni bağlar `ThemeInfo` basamaklı basamaklı bir parametre değeri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-459">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="b7b43-460">Parametre için bir bileşen tarafından görüntülenen düğmeleri CSS sınıfının ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-460">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="b7b43-461">`CascadingValuesParametersTheme` Bileşen:</span><span class="sxs-lookup"><span data-stu-id="b7b43-461">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="@IncrementCount">
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

### <a name="tabset-example"></a><span data-ttu-id="b7b43-462">TabSet örneği</span><span class="sxs-lookup"><span data-stu-id="b7b43-462">TabSet example</span></span>

<span data-ttu-id="b7b43-463">Geçişli parametreleri, bileşen hiyerarşi işbirliği yapmak bileşenler de olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-463">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="b7b43-464">Örneğin, aşağıdakileri dikkate alın *TabSet* örnek uygulamada örnek.</span><span class="sxs-lookup"><span data-stu-id="b7b43-464">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="b7b43-465">Örnek uygulamanın bir `ITab` uygulama sekme arabirimi:</span><span class="sxs-lookup"><span data-stu-id="b7b43-465">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="b7b43-466">`CascadingValuesParametersTabSet` Bileşen `TabSet` birkaç içeren bileşen `Tab` bileşenler:</span><span class="sxs-lookup"><span data-stu-id="b7b43-466">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="b7b43-467">Alt `Tab` bileşenleri olmayan açıkça için parametre olarak geçirilen `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-467">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="b7b43-468">Bunun yerine, alt `Tab` bileşenlerdir içeriği alt parçası `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-468">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="b7b43-469">Ancak, `TabSet` yine her biri hakkında bilmesi gereken `Tab` bileşen böylece üstbilgileri ve etkin sekmede işleyebilirsiniz. Ek kod gerek kalmadan bu koordinasyon sağlamak `TabSet` bileşen *kendisini basamaklı bir değer sağlayabilirsiniz* , ardından alındığından descendent `Tab` bileşenleri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-469">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="b7b43-470">`TabSet` Bileşen:</span><span class="sxs-lookup"><span data-stu-id="b7b43-470">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="b7b43-471">Bloğun `Tab` bileşenleri yakalama içeren `TabSet` basamaklı bir parametre olarak bu nedenle `Tab` bileşenleri eklemek için kendilerini `TabSet` ve hangi sekmesi etkin olduğu koordinat.</span><span class="sxs-lookup"><span data-stu-id="b7b43-471">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="b7b43-472">`Tab` Bileşen:</span><span class="sxs-lookup"><span data-stu-id="b7b43-472">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="b7b43-473">Razor şablonları</span><span class="sxs-lookup"><span data-stu-id="b7b43-473">Razor templates</span></span>

<span data-ttu-id="b7b43-474">İşleme parçaları, Razor şablon söz dizimi kullanılarak tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-474">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="b7b43-475">Razor şablonları UI parçacık tanımlayın ve aşağıdaki biçimi varsayar için bir yol sağlar:</span><span class="sxs-lookup"><span data-stu-id="b7b43-475">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="b7b43-476">Aşağıdaki örnek nasıl belirtileceğini göstermektedir `RenderFragment` ve `RenderFragment<T>` değerleri ve doğrudan bir bileşen şablonlarını işlemesi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-476">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="b7b43-477">İşleme parçaları de geçirilebilir bağımsız değişkenleri olarak [şablonlu bileşenleri](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="b7b43-477">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="b7b43-478">Önceki kodun çıktısı oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="b7b43-478">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="b7b43-479">El ile RenderTreeBuilder mantığı</span><span class="sxs-lookup"><span data-stu-id="b7b43-479">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="b7b43-480">`Microsoft.AspNetCore.Components.RenderTree` bileşenleri ve bileşenleri el ile oluşturma da dahil olmak üzere öğeleri işlemek için yöntemler sağlar C# kod.</span><span class="sxs-lookup"><span data-stu-id="b7b43-480">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="b7b43-481">Kullanım `RenderTreeBuilder` bileşenler oluşturmak için Gelişmiş bir senaryodur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-481">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="b7b43-482">Hatalı biçimlendirilmiş bir bileşen (örneğin, bir kapatılmamış biçimlendirme etiketi) tanımsız davranışlara neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-482">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="b7b43-483">Aşağıdakileri göz önünde bulundurun `PetDetails` el ile başka bir bileşene dönüştürerek yerleşik bileşeni:</span><span class="sxs-lookup"><span data-stu-id="b7b43-483">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    private string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="b7b43-484">Aşağıdaki örnekte, bir döngüde `CreateComponent` yöntemi oluşturur üç `PetDetails` bileşenleri.</span><span class="sxs-lookup"><span data-stu-id="b7b43-484">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="b7b43-485">Çağrılırken `RenderTreeBuilder` bileşenler oluşturmak için yöntemleri (`OpenComponent` ve `AddAttribute`), sıra numaraları olan kaynak kodu satır numaraları.</span><span class="sxs-lookup"><span data-stu-id="b7b43-485">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="b7b43-486">Kod, değil ayrı çağrı çağrılarını ayrı satırlara karşılık gelen sıra numaraları Blazor fark algoritması kullanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-486">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="b7b43-487">Bir bileşen ile oluştururken `RenderTreeBuilder` yöntemleri, sabit kodlamayın seri numaraları için bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="b7b43-487">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="b7b43-488">**Sıra numarası oluşturmak için bir hesaplama veya sayaç kullanarak düşük performansa neden olabilir.**</span><span class="sxs-lookup"><span data-stu-id="b7b43-488">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="b7b43-489">Daha fazla bilgi için [sıra numaraları ilişkilendirmek için kod satır numaraları ve değil yürütme sırası](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b7b43-489">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="b7b43-490">`BuiltContent` Bileşen:</span><span class="sxs-lookup"><span data-stu-id="b7b43-490">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="@RenderComponent">
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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="b7b43-491">Seri numaraları için kod satır numaraları ve değil yürütme sırası arasında bir ilişki</span><span class="sxs-lookup"><span data-stu-id="b7b43-491">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="b7b43-492">Blazor `.razor` dosyaları her zaman derlenir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-492">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="b7b43-493">Bu büyük olasılıkla için harika bir avantajı, `.razor` çünkü derleme adımı çalışma zamanında uygulama performansını bilgi eklenmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-493">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="b7b43-494">Bu geliştirmeler anahtar bir örnek içeren *sıra numaraları*.</span><span class="sxs-lookup"><span data-stu-id="b7b43-494">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="b7b43-495">Sıra numaraları, hangi kod ayrı ve sıralı satırlarından çıkışları gelen çalışma zamanı gösterir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-495">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="b7b43-496">Çalışma zamanı, genel ağaç fark algoritması için normalde mümkün olandan çok daha hızlı doğrusal zamanda verimli ağaç farkları oluşturmak için bu bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-496">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="b7b43-497">Aşağıdakileri göz önünde bulundurun basit `.razor` dosyası:</span><span class="sxs-lookup"><span data-stu-id="b7b43-497">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="b7b43-498">Yukarıdaki kod, aşağıdaki gibi bir şey derler:</span><span class="sxs-lookup"><span data-stu-id="b7b43-498">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="b7b43-499">Ne zaman kodu yürütür, ilk kez, `someFlag` olduğu `true`, oluşturucu alır:</span><span class="sxs-lookup"><span data-stu-id="b7b43-499">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="b7b43-500">Sequence</span><span class="sxs-lookup"><span data-stu-id="b7b43-500">Sequence</span></span> | <span data-ttu-id="b7b43-501">Tür</span><span class="sxs-lookup"><span data-stu-id="b7b43-501">Type</span></span>      | <span data-ttu-id="b7b43-502">Veri</span><span class="sxs-lookup"><span data-stu-id="b7b43-502">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="b7b43-503">0</span><span class="sxs-lookup"><span data-stu-id="b7b43-503">0</span></span>        | <span data-ttu-id="b7b43-504">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="b7b43-504">Text node</span></span> | <span data-ttu-id="b7b43-505">ilk</span><span class="sxs-lookup"><span data-stu-id="b7b43-505">First</span></span>  |
| <span data-ttu-id="b7b43-506">1\.</span><span class="sxs-lookup"><span data-stu-id="b7b43-506">1</span></span>        | <span data-ttu-id="b7b43-507">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="b7b43-507">Text node</span></span> | <span data-ttu-id="b7b43-508">Saniye</span><span class="sxs-lookup"><span data-stu-id="b7b43-508">Second</span></span> |

<span data-ttu-id="b7b43-509">Bu Imagine `someFlag` olur `false`, ve biçimlendirme yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-509">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="b7b43-510">Bu kez, oluşturucu alır:</span><span class="sxs-lookup"><span data-stu-id="b7b43-510">This time, the builder receives:</span></span>

| <span data-ttu-id="b7b43-511">Sequence</span><span class="sxs-lookup"><span data-stu-id="b7b43-511">Sequence</span></span> | <span data-ttu-id="b7b43-512">Tür</span><span class="sxs-lookup"><span data-stu-id="b7b43-512">Type</span></span>       | <span data-ttu-id="b7b43-513">Veri</span><span class="sxs-lookup"><span data-stu-id="b7b43-513">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="b7b43-514">1\.</span><span class="sxs-lookup"><span data-stu-id="b7b43-514">1</span></span>        | <span data-ttu-id="b7b43-515">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="b7b43-515">Text node</span></span>  | <span data-ttu-id="b7b43-516">Saniye</span><span class="sxs-lookup"><span data-stu-id="b7b43-516">Second</span></span> |

<span data-ttu-id="b7b43-517">Çalışma zamanı bir fark gerçekleştirdiğinde, görür öğe dizisi `0` kaldırıldı, aşağıdaki Önemsiz oluşturmasını sağlayacak şekilde *betiğini Düzenle*:</span><span class="sxs-lookup"><span data-stu-id="b7b43-517">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="b7b43-518">İlk metin düğümü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-518">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="b7b43-519">Sıra numaraları programlı olarak oluşturursanız yanlış unsurları</span><span class="sxs-lookup"><span data-stu-id="b7b43-519">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="b7b43-520">Bunun yerine aşağıdaki ağaç Oluşturucu mantığı işlemek yazdığınız varsayalım:</span><span class="sxs-lookup"><span data-stu-id="b7b43-520">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="b7b43-521">Şimdi ilk çıktı.</span><span class="sxs-lookup"><span data-stu-id="b7b43-521">Now, the first output is:</span></span>

<span data-ttu-id="b7b43-522">| Dizisi | Tür | Veri || :------: | --------- | :--- : | | 0 | Metin düğümü | İlk || 1 | Metin düğümü | İkinci |</span><span class="sxs-lookup"><span data-stu-id="b7b43-522">| Sequence | Type      | Data   | | :------: | --------- | :--- : | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="b7b43-523">Bu sonuç için önceki durum, aynı olduğundan negatif hiçbir sorun yoktur.</span><span class="sxs-lookup"><span data-stu-id="b7b43-523">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="b7b43-524">`someFlag` olan `false` işleme ve çıktısını ikinci olan:</span><span class="sxs-lookup"><span data-stu-id="b7b43-524">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="b7b43-525">Sequence</span><span class="sxs-lookup"><span data-stu-id="b7b43-525">Sequence</span></span> | <span data-ttu-id="b7b43-526">Tür</span><span class="sxs-lookup"><span data-stu-id="b7b43-526">Type</span></span>      | <span data-ttu-id="b7b43-527">Veri</span><span class="sxs-lookup"><span data-stu-id="b7b43-527">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="b7b43-528">0</span><span class="sxs-lookup"><span data-stu-id="b7b43-528">0</span></span>        | <span data-ttu-id="b7b43-529">Metin düğümü</span><span class="sxs-lookup"><span data-stu-id="b7b43-529">Text node</span></span> | <span data-ttu-id="b7b43-530">Saniye</span><span class="sxs-lookup"><span data-stu-id="b7b43-530">Second</span></span> |

<span data-ttu-id="b7b43-531">Fark algoritması, bu kez görür *iki* değişiklikleri oluşmuş ve algoritma aşağıdaki düzenleme komut dosyası oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b7b43-531">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="b7b43-532">İlk metin düğümü değiştirin `Second`.</span><span class="sxs-lookup"><span data-stu-id="b7b43-532">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="b7b43-533">İkinci metin düğümü kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b7b43-533">Remove the second text node.</span></span>

<span data-ttu-id="b7b43-534">Sıra numaraları oluşturma tüm hakkında yararlı bilgiler nerede kaybetti `if/else` dallar ve döngüler özgün koda mevcut.</span><span class="sxs-lookup"><span data-stu-id="b7b43-534">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="b7b43-535">Bu bir fark sonuçlarını **iki kez sürece** önceki gibi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-535">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="b7b43-536">Bu basit bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-536">This is a trivial example.</span></span> <span data-ttu-id="b7b43-537">Karmaşık ve iç içe yapılar ve özellikle döngüler daha gerçekçi durumda da, performans maliyeti daha ciddi.</span><span class="sxs-lookup"><span data-stu-id="b7b43-537">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="b7b43-538">Hemen hangi döngü blokları veya dallar eklenen veya kaldırılan tanımlamak yerine, derin bir şekilde işleme ağaçlara recurse ve onu hakkında misinformed çünkü genellikle derleme çok uzun düzenleme betiklerini fark algoritmasına sahip eski ve yeni yapılar birbiriyle ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-538">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="b7b43-539">Yönergeler ve sonuçları</span><span class="sxs-lookup"><span data-stu-id="b7b43-539">Guidance and conclusions</span></span>

* <span data-ttu-id="b7b43-540">Sıra numaraları dinamik olarak üretilen uygulama performans düşebilir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-540">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="b7b43-541">Framework'te derleme zamanında yakalanır sürece gerekli bilgileri mevcut olmadığından kendi sıra numaraları çalışma zamanında otomatik olarak oluşturulamıyor.</span><span class="sxs-lookup"><span data-stu-id="b7b43-541">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="b7b43-542">El ile uygulanan uzun bloklarını yazmayın `RenderTreeBuilder` mantığı.</span><span class="sxs-lookup"><span data-stu-id="b7b43-542">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="b7b43-543">Tercih ettiğiniz `.razor` dosyaları ve sıra numaraları ile dağıtılacak derleyici sağlar.</span><span class="sxs-lookup"><span data-stu-id="b7b43-543">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="b7b43-544">Sıra numaraları sabittir, fark algoritması yalnızca bir sıra numaraları değeri artırmak gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-544">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="b7b43-545">İlk değer ve boşluklar önemli değildir.</span><span class="sxs-lookup"><span data-stu-id="b7b43-545">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="b7b43-546">Yasal bir seçenek olan sıra numarası kod satır numarasını kullanın veya sıfırdan başlayın ve olanlara ya da yüz artırmak için (veya herhangi bir tercih edilen aralığı).</span><span class="sxs-lookup"><span data-stu-id="b7b43-546">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="b7b43-547">Ağaç ayırırken diğer UI çerçeveleri bunları kullanmayın Blazor seri numaraları kullanır.</span><span class="sxs-lookup"><span data-stu-id="b7b43-547">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="b7b43-548">Sıra numaraları kullanılır ve Blazor sıra numaraları ile otomatik olarak yazma geliştiriciler için ilgilenen bir derleme adımı avantajlarından fark alma işlemini çok daha hızlı `.razor` dosyaları.</span><span class="sxs-lookup"><span data-stu-id="b7b43-548">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
