---
title: ASP.NET Core Blazor düzenleri
author: guardrex
description: Blazor uygulamaları için yeniden kullanılabilir düzen bileşenleri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/layouts
ms.openlocfilehash: 064ffafb8e7f3e76e5bd7bde0e06ef271fe92542
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211589"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="b8e2b-103">ASP.NET Core Blazor düzenleri</span><span class="sxs-lookup"><span data-stu-id="b8e2b-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="b8e2b-104">Tarafından [Rainer Stropek](https://www.timecockpit.com) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b8e2b-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b8e2b-105">Menüler, telif hakkı iletileri ve şirket logoları gibi bazı uygulama öğeleri genellikle uygulamanın genel düzeninin parçasıdır ve uygulamadaki her bileşen tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="b8e2b-106">Bu öğelerin kodunu bir uygulamanın tüm bileşenlerine kopyalamak, öğelerden biri bir güncelleştirme gerektirdiğinde her bileşenin güncelleştirilmesi gereken&mdash;etkili bir yaklaşım değildir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="b8e2b-107">Bu tür çoğaltmaya devam etmek zordur ve zaman içinde tutarsız içeriğe yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="b8e2b-108">*Düzenler* bu sorunu çözüyor.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="b8e2b-109">Teknik olarak, düzen yalnızca başka bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="b8e2b-110">Bir düzen Razor şablonunda veya C# kodda tanımlanır ve [veri bağlama](xref:blazor/components#data-binding), [bağımlılık ekleme](xref:blazor/dependency-injection)ve diğer bileşen senaryolarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="b8e2b-111">Bir *bileşeni* bir *düzene*dönüştürmek için bileşen:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="b8e2b-112">Düzen içindeki `LayoutComponentBase`işlenmiş içerik için bir `Body` özellik tanımlayan öğesinden devralır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="b8e2b-113">İçeriğin işlendiği yerleşim `@Body` biçimlendirmesinde konumu belirtmek için Razor söz dizimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="b8e2b-114">Aşağıdaki kod örneğinde, *mainlayout. Razor*olan bir düzen bileşeninin Razor şablonu gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="b8e2b-115">Düzen, gezinti `LayoutComponentBase` çubuğu ve alt `@Body` bilgi arasında devralır ve Ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="b8e2b-116">Blazor uygulama şablonlarından birini temel alan bir uygulamada, `MainLayout` bileşen (*mainlayout. Razor*) uygulamanın *paylaşılan* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="b8e2b-117">Varsayılan düzen</span><span class="sxs-lookup"><span data-stu-id="b8e2b-117">Default layout</span></span>

<span data-ttu-id="b8e2b-118">Uygulamanın App `Router` *. Razor* dosyasındaki bileşende varsayılan uygulama yerleşimini belirtin.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="b8e2b-119">Varsayılan Blazor `Router` şablonları tarafından belirtilen aşağıdaki bileşen, `MainLayout` bileşene varsayılan düzeni ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="b8e2b-120">İçerik için `NotFound` varsayılan bir düzen sağlamak üzere `NotFound` bir `LayoutView` içerik belirtin:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="b8e2b-121">`Router` Bileşen hakkında daha fazla bilgi için bkz <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="b8e2b-122">Düzen bir varsayılan düzen olarak belirtildiğinde, bileşen başına veya klasör temelinde geçersiz kılınabileceğinden, yararlı bir uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="b8e2b-123">En genel teknik olduğundan, uygulamanın varsayılan yerleşimini ayarlamak için yönlendiriciyi kullanmayı tercih edin.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="b8e2b-124">Bir bileşende düzen belirtme</span><span class="sxs-lookup"><span data-stu-id="b8e2b-124">Specify a layout in a component</span></span>

<span data-ttu-id="b8e2b-125">Bir bileşene düzen uygulamak `@layout` için Razor yönergesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="b8e2b-126">Derleyici, bileşen `@layout` sınıfına uygulanan `LayoutAttribute`öğesine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="b8e2b-127">Aşağıdaki `MasterList` bileşenin içeriği `MasterLayout` konumuna`@Body`öğesine eklenir:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="b8e2b-128">Düzen doğrudan bir bileşen içinde belirtildiğinde, yönlendiricide ayarlanan *varsayılan bir düzen* veya `@layout` *_ımports. Razor*öğesinden içeri aktarılan bir yönerge geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="b8e2b-129">Merkezi düzen seçimi</span><span class="sxs-lookup"><span data-stu-id="b8e2b-129">Centralized layout selection</span></span>

<span data-ttu-id="b8e2b-130">Bir uygulamanın her klasörü, isteğe bağlı olarak *_ımports. Razor*adlı bir şablon dosyası içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="b8e2b-131">Derleyici tüm Razor şablonlarındaki içeri aktarmalar dosyasında belirtilen yönergeleri aynı klasörde ve özyinelemeli olarak tüm alt klasörlerinde içerir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="b8e2b-132">Bu nedenle, bir *_ımports. Razor* dosyası `@layout MyCoolLayout` , bir klasördeki tüm bileşenlerin kullanımını `MyCoolLayout`sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="b8e2b-133">Klasör ve alt klasörlerdeki tüm `@layout MyCoolLayout` *. Razor* dosyalarına tekrar tekrar ekleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="b8e2b-134">`@using`yönergeler aynı zamanda bileşenlere aynı şekilde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="b8e2b-135">Aşağıdaki *_ımports. Razor* dosyası içeri aktarmaları:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="b8e2b-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="b8e2b-137">Aynı klasörde ve alt klasörlerde bulunan tüm Razor bileşenleri.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="b8e2b-138">`BlazorApp1.Data` Ad alanı.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="b8e2b-139">*_Imports. Razor* dosyası, [Razor görünümleri ve sayfaları Için _Viewwimports. cshtml dosyasına](xref:mvc/views/layout#importing-shared-directives) benzer ancak özellikle Razor bileşen dosyalarına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="b8e2b-140">*_Imports. Razor* içinde bir düzen belirtme, yönlendiricinin *varsayılan düzeni*olarak belirtilen bir düzeni geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="b8e2b-141">İç içe düzenleri</span><span class="sxs-lookup"><span data-stu-id="b8e2b-141">Nested layouts</span></span>

<span data-ttu-id="b8e2b-142">Uygulamalar iç içe düzenleri içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="b8e2b-143">Bir bileşen, daha sonra başka bir düzene başvuruda bulunan bir düzene başvurabilir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="b8e2b-144">Örneğin, iç içe düzenleri çok düzeyli bir menü yapısı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="b8e2b-145">Aşağıdaki örnek, iç içe düzenleri nasıl kullanacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="b8e2b-146">*Epısodescomponent. Razor* dosyası görüntülenecek bileşendir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="b8e2b-147">Bileşen öğesine başvurur `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-147">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="b8e2b-148">*Masterlistlayout. Razor* dosyası sağlar `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="b8e2b-149">Düzen, nerede işlendiği başka bir `MasterLayout`düzene başvurur.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="b8e2b-150">`EpisodesComponent`, görüntülendiği yerde `@Body` işlenir:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="b8e2b-151">Son olarak `MasterLayout` , *masterlayout. Razor* içinde üstbilgi, ana menü ve altbilgi gibi en üst düzey düzen öğelerini içerir.</span><span class="sxs-lookup"><span data-stu-id="b8e2b-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="b8e2b-152">`MasterListLayout`ile birlikte `EpisodesComponent` `@Body` işlendiğinde, şu şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="b8e2b-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="b8e2b-153">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b8e2b-153">Additional resources</span></span>

* <xref:mvc/views/layout>
