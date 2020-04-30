---
title: ASP.NET Core Razor bileşenlerini Razor Pages ve MVC uygulamalarıyla tümleştirin
author: guardrex
description: Blazor Uygulamalarda BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 4e2103b7e8b65478808093d7a31e8cfe29b04984
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558909"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="08749-103">ASP.NET Core Razor bileşenlerini Razor Pages ve MVC uygulamalarıyla tümleştirin</span><span class="sxs-lookup"><span data-stu-id="08749-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="08749-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="08749-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="08749-105">Razor bileşenleri, Razor Pages ve MVC uygulamalarıyla tümleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="08749-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="08749-106">Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.</span><span class="sxs-lookup"><span data-stu-id="08749-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="08749-107">[Uygulamayı hazırladıktan](#prepare-the-app)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerde yer alan kılavuzu kullanın:</span><span class="sxs-lookup"><span data-stu-id="08749-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="08749-108">Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenler için yönlendirilebilir bileşenler &ndash; .</span><span class="sxs-lookup"><span data-stu-id="08749-108">Routable components &ndash; For components that are directly routable from user requests.</span></span> <span data-ttu-id="08749-109">Ziyaretçilerin, bir [`@page`](xref:mvc/views/razor#page) yönergesi olan bir bileşen için TARAYıCıLARıNDA bir http isteği yapabilmeleri gerektiğinde bu kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="08749-110">Razor Pages uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="08749-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="08749-111">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="08749-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="08749-112">Kullanıcı isteklerinden doğrudan yönlendirilemeyen bileşenler için &ndash; [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="08749-112">[Render components from a page or view](#render-components-from-a-page-or-view) &ndash; For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="08749-113">Uygulama bileşenleri [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile var olan sayfalara ve görünümlere eklerken bu kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="08749-114">Uygulamayı hazırlama</span><span class="sxs-lookup"><span data-stu-id="08749-114">Prepare the app</span></span>

<span data-ttu-id="08749-115">Mevcut bir Razor Pages veya MVC uygulaması, Razor bileşenlerini sayfalarla ve görünümleriyle tümleştirebilir:</span><span class="sxs-lookup"><span data-stu-id="08749-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="08749-116">Uygulamanın düzen dosyasında (*_Layout. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="08749-116">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="08749-117">Aşağıdaki `<base>` etiketi `<head>` öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="08749-118">Yukarıdaki `href` örnekte yer alan değer ( *uygulama temel yolu*), uygulamanın kök URL yolunda (`/`) bulunduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="08749-118">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="08749-119">Uygulama bir alt uygulama ise, <xref:host-and-deploy/blazor/index#app-base-path> makalenin *uygulama temel yolu* bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="08749-120">*_Layout. cshtml* dosyası, bir MVC uygulamasında bir Razor Pages uygulamasının veya *görünümlerinin/paylaşılan* klasörünün *Sayfalar/paylaşılan* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="08749-120">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="08749-121">Kapanış `</body>` etiketinden `<script>` hemen önce *blazor. Server. js* betiği için bir etiket ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-121">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="08749-122">Framework, *blazor. Server. js* betiğini uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="08749-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="08749-123">Betiği uygulamaya el ile eklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="08749-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="08749-124">Aşağıdaki içerikle projenin kök klasörüne bir *_Imports. Razor* dosyası ekleyin (son ad alanını `MyAppNamespace`uygulamanın ad alanına değiştirin):</span><span class="sxs-lookup"><span data-stu-id="08749-124">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="08749-125">İçinde `Startup.ConfigureServices`, Blazor sunucu hizmetini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="08749-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="08749-126">İçinde `Startup.Configure`, Blazor hub uç noktasını şu şekilde `app.UseEndpoints`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="08749-127">Bileşenleri herhangi bir sayfa veya görünümle tümleştirin.</span><span class="sxs-lookup"><span data-stu-id="08749-127">Integrate components into any page or view.</span></span> <span data-ttu-id="08749-128">Daha fazla bilgi için, [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="08749-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="08749-129">Razor Pages uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="08749-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="08749-130">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="08749-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="08749-131">Razor Pages uygulamalarda yönlendirilebilir Razor bileşenlerini desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="08749-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="08749-132">[Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="08749-133">Aşağıdaki içeriğe sahip proje köküne bir *app. Razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-133">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="08749-134">*Sayfalar* klasörüne aşağıdaki içeriğe sahip bir *_Host. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-134">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="08749-135">Bileşenler, düzeni için paylaşılan *_Layout. cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="08749-135">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="08749-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>`App` bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="08749-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="08749-137">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="08749-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="08749-138">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="08749-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="08749-139">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="08749-139">Render Mode</span></span> | <span data-ttu-id="08749-140">Açıklama</span><span class="sxs-lookup"><span data-stu-id="08749-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="08749-141">`App` BILEŞENI statik HTML olarak işler ve bir Blazor Server uygulaması için işaret içerir.</span><span class="sxs-lookup"><span data-stu-id="08749-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="08749-142">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasını önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="08749-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="08749-143">Bir Blazor sunucu uygulaması için işaretleyici işler.</span><span class="sxs-lookup"><span data-stu-id="08749-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="08749-144">`App` Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="08749-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="08749-145">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasını önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="08749-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="08749-146">`App` BILEŞENI statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="08749-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="08749-147">Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>..</span><span class="sxs-lookup"><span data-stu-id="08749-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="08749-148">*_Host. cshtml* sayfasına yönelik düşük öncelikli bir yolu, içindeki `Startup.Configure`uç nokta yapılandırmasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-148">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="08749-149">Uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-149">Add routable components to the app.</span></span> <span data-ttu-id="08749-150">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="08749-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="08749-151">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="08749-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="08749-152">MVC uygulamasında yönlendirilebilir bileşenleri kullanma</span><span class="sxs-lookup"><span data-stu-id="08749-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="08749-153">*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*</span><span class="sxs-lookup"><span data-stu-id="08749-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="08749-154">MVC uygulamalarında yönlendirilebilir Razor bileşenlerini desteklemek için:</span><span class="sxs-lookup"><span data-stu-id="08749-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="08749-155">[Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="08749-156">Aşağıdaki içeriğe sahip projenin köküne bir *app. Razor* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-156">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="08749-157">Aşağıdaki içeriğe sahip *Görünümler/giriş* klasörüne bir *_Host. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-157">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="08749-158">Bileşenler, düzeni için paylaşılan *_Layout. cshtml* dosyasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="08749-158">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="08749-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>`App` bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="08749-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="08749-160">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="08749-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="08749-161">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="08749-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="08749-162">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="08749-162">Render Mode</span></span> | <span data-ttu-id="08749-163">Açıklama</span><span class="sxs-lookup"><span data-stu-id="08749-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="08749-164">`App` BILEŞENI statik HTML olarak işler ve Blazor sunucu uygulaması için bir işaret içerir.</span><span class="sxs-lookup"><span data-stu-id="08749-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="08749-165">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="08749-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="08749-166">Blazor Sunucu uygulaması için bir işaret oluşturur.</span><span class="sxs-lookup"><span data-stu-id="08749-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="08749-167">`App` Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="08749-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="08749-168">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="08749-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="08749-169">`App` BILEŞENI statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="08749-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="08749-170">Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>..</span><span class="sxs-lookup"><span data-stu-id="08749-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="08749-171">Ana denetleyiciye bir eylem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="08749-172">İçindeki `Startup.Configure`uç nokta yapılandırmasına *_Host. cshtml* görünümünü döndüren denetleyici eylemi için düşük öncelikli bir yol ekleyin:</span><span class="sxs-lookup"><span data-stu-id="08749-172">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="08749-173">Bir *Sayfalar* klasörü oluşturun ve uygulamaya yönlendirilebilir bileşenler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-173">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="08749-174">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="08749-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="08749-175">Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="08749-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="08749-176">Bir sayfadan veya görünümden bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="08749-176">Render components from a page or view</span></span>

<span data-ttu-id="08749-177">*Bu bölüm, bileşenlerin Kullanıcı isteklerinden doğrudan yönlendirilemeyen sayfalara veya görünümlere bileşen eklenmesine aittir.*</span><span class="sxs-lookup"><span data-stu-id="08749-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="08749-178">Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.</span><span class="sxs-lookup"><span data-stu-id="08749-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="08749-179">Durum bilgisi olan etkileşimli bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="08749-179">Render stateful interactive components</span></span>

<span data-ttu-id="08749-180">Durum bilgisi olan etkileşimli bileşenler Razor sayfasına veya görünümüne eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="08749-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="08749-181">Sayfa veya görünüm şunları işler:</span><span class="sxs-lookup"><span data-stu-id="08749-181">When the page or view renders:</span></span>

* <span data-ttu-id="08749-182">Bileşen sayfa veya görünümle birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="08749-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="08749-183">Prerendering için kullanılan ilk bileşen durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="08749-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="08749-184">SignalR Bağlantı kurulduunda yeni bileşen durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="08749-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="08749-185">Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:</span><span class="sxs-lookup"><span data-stu-id="08749-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="08749-186">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="08749-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="08749-187">Etkileşimsiz bileşenleri işle</span><span class="sxs-lookup"><span data-stu-id="08749-187">Render noninteractive components</span></span>

<span data-ttu-id="08749-188">Aşağıdaki Razor sayfasında, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="08749-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="08749-189">Bileşen statik olarak işlendiğinden, bileşen etkileşimli değildir:</span><span class="sxs-lookup"><span data-stu-id="08749-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="08749-190">Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="08749-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="08749-191">Bileşen ad alanları</span><span class="sxs-lookup"><span data-stu-id="08749-191">Component namespaces</span></span>

<span data-ttu-id="08749-192">Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü/görünümü veya *_ViewImports. cshtml* dosyasını temsil eden ad alanını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="08749-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="08749-193">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="08749-193">In the following example:</span></span>

* <span data-ttu-id="08749-194">`MyAppNamespace` Uygulamanın ad alanına geçin.</span><span class="sxs-lookup"><span data-stu-id="08749-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="08749-195">Bileşenleri tutmak için *bileşen* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre geçin `Components` .</span><span class="sxs-lookup"><span data-stu-id="08749-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="08749-196">*_ViewImports. cshtml* dosyası, bir Razor Pages uygulamasının *Sayfalar* klasöründe veya bir MVC uygulamasının *views* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="08749-196">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="08749-197">Daha fazla bilgi için bkz. <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="08749-197">For more information, see <xref:blazor/components#import-components>.</span></span>
