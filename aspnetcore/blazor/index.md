---
title: ASP.NET Core Blazor 'e giriş
author: guardrex
description: ASP.NET Core uygulamasında .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için bir yol olan ASP.NET Core Blazor 'i gezin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 10/15/2019
uid: blazor/index
ms.openlocfilehash: abf631b5e1cf762eaef4bd85a6b85802c9899291
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391152"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="e782a-103">ASP.NET Core Blazor 'e giriş</span><span class="sxs-lookup"><span data-stu-id="e782a-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="e782a-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="e782a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e782a-105">*Blazor 'e hoş geldiniz!*</span><span class="sxs-lookup"><span data-stu-id="e782a-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="e782a-106">Blazor, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmaya yönelik bir çerçevedir:</span><span class="sxs-lookup"><span data-stu-id="e782a-106">Blazor is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="e782a-107">JavaScript yerine zengin etkileşimli Uıusing C# oluşturma.</span><span class="sxs-lookup"><span data-stu-id="e782a-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="e782a-108">.NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e782a-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="e782a-109">Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.</span><span class="sxs-lookup"><span data-stu-id="e782a-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="e782a-110">İstemci tarafı web geliştirme için .NET kullanmak aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="e782a-110">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="e782a-111">JavaScript C# yerine kodu yazın.</span><span class="sxs-lookup"><span data-stu-id="e782a-111">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="e782a-112">.NET kitaplıklarının mevcut .NET ekosisteminden yararlanın.</span><span class="sxs-lookup"><span data-stu-id="e782a-112">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="e782a-113">Sunucu ve istemci arasında uygulama mantığını paylaşma.</span><span class="sxs-lookup"><span data-stu-id="e782a-113">Share app logic across server and client.</span></span>
* <span data-ttu-id="e782a-114">Avantajı. NET ' in performans, güvenilirlik ve güvenlik.</span><span class="sxs-lookup"><span data-stu-id="e782a-114">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="e782a-115">Windows, Linux ve macOS 'ta Visual Studio ile üretken olun.</span><span class="sxs-lookup"><span data-stu-id="e782a-115">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="e782a-116">Kararlı, özellik açısından zengin ve kullanımı kolay olan ortak diller, çerçeveler ve araçlar kümesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e782a-116">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="e782a-117">Bileşenler</span><span class="sxs-lookup"><span data-stu-id="e782a-117">Components</span></span>

<span data-ttu-id="e782a-118">Blazor uygulamaları, *bileşenleri*temel alır.</span><span class="sxs-lookup"><span data-stu-id="e782a-118">Blazor apps are based on *components*.</span></span> <span data-ttu-id="e782a-119">Blazor içindeki bir bileşen, bir sayfa, iletişim veya veri girişi formu gibi bir kullanıcı arabirimi öğesidir.</span><span class="sxs-lookup"><span data-stu-id="e782a-119">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="e782a-120">Bileşenler, .NET Derlemeleriyle yerleşik olarak bulunan .NET sınıflarıdır:</span><span class="sxs-lookup"><span data-stu-id="e782a-120">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="e782a-121">Esnek kullanıcı arabirimi işleme mantığını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="e782a-121">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="e782a-122">Kullanıcı olaylarını işleyin.</span><span class="sxs-lookup"><span data-stu-id="e782a-122">Handle user events.</span></span>
* <span data-ttu-id="e782a-123">İç içe ve yeniden kullanılabilir olabilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-123">Can be nested and reused.</span></span>
* <span data-ttu-id="e782a-124">, [Razor sınıfı kitaplıkları](xref:razor-pages/ui-class) veya [NuGet paketleri](/nuget/what-is-nuget)olarak paylaşılabilir ve dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-124">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="e782a-125">Bileşen sınıfı genellikle *. Razor* dosya uzantısına sahip bir [Razor](xref:mvc/views/razor) biçimlendirme sayfası biçiminde yazılır.</span><span class="sxs-lookup"><span data-stu-id="e782a-125">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="e782a-126">Blazor içindeki bileşenler, resmi olarak *Razor bileşenleri*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="e782a-126">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="e782a-127">Razor, geliştirici üretkenliği için tasarlanan C# kodla HTML işaretlemesini birleştirmek için bir sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="e782a-127">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="e782a-128">Razor, IntelliSense desteğiyle aynı dosyada HTML işaretlemesi ve C# arasında geçiş yapmanıza olanak sağlar [](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="e782a-128">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="e782a-129">Razor Pages ve MVC de Razor kullanır.</span><span class="sxs-lookup"><span data-stu-id="e782a-129">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="e782a-130">İstek/yanıt modeli etrafında oluşturulan Razor Pages ve MVC 'nin aksine, bileşenler özellikle istemci tarafı UI mantığı ve bileşimi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e782a-130">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="e782a-131">Aşağıdaki Razor biçimlendirmesi, başka bir bileşen içinde iç içe kullanılabilecek bir bileşeni (*Iletişim kutusu. Razor*) gösterir:</span><span class="sxs-lookup"><span data-stu-id="e782a-131">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="e782a-132">İletişim kutusunun gövde içeriği (`ChildContent`) ve başlığı (`Title`), Kullanıcı arabiriminde bu bileşeni kullanan bileşen tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e782a-132">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="e782a-133">`OnYes`, düğmenin C# `onclick` olayı tarafından tetiklenen bir yöntemdir.</span><span class="sxs-lookup"><span data-stu-id="e782a-133">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

<span data-ttu-id="e782a-134">Blazor, UI bileşimi için doğal HTML etiketleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="e782a-134">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="e782a-135">HTML öğeleri, bileşenleri belirtir ve bir etiketin öznitelikleri değerleri bir bileşenin özelliklerine iletir.</span><span class="sxs-lookup"><span data-stu-id="e782a-135">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="e782a-136">Aşağıdaki örnekte `Index` bileşeni `Dialog` bileşenini kullanır.</span><span class="sxs-lookup"><span data-stu-id="e782a-136">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="e782a-137">`ChildContent` ve `Title` `<Dialog>` öğesinin öznitelikleri ve içeriği tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e782a-137">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="e782a-138">*Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e782a-138">*Index.razor*:</span></span>

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="e782a-139">Üst öğeye (*Index. Razor*) bir tarayıcıda erişildiğinde iletişim kutusu işlenir:</span><span class="sxs-lookup"><span data-stu-id="e782a-139">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Tarayıcıda işlenen iletişim kutusu bileşeni](index/_static/dialog.png)

<span data-ttu-id="e782a-141">Bu bileşen uygulamada kullanıldığında, [Visual Studio](/visualstudio/ide/using-intellisense) 'da ıntellisense ve [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) , sözdizimi ve parametre tamammasıyla geliştirmeyi hızlandırır.</span><span class="sxs-lookup"><span data-stu-id="e782a-141">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="e782a-142">Bileşenler, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılan bir *işleme ağacı*adlı, tarayıcı belge nesne MODELI (DOM) ' ın bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="e782a-142">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="e782a-143">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e782a-143">Blazor WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e782a-144">Blazor WebAssembly, .NET ile etkileşimli istemci tarafı Web uygulamaları oluşturmaya yönelik tek sayfalı bir uygulama çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="e782a-144">Blazor WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="e782a-145">Blazor WebAssembly, eklentiler veya Code transpilation olmadan açık Web standartları kullanır ve mobil tarayıcılar dahil tüm modern web tarayıcılarında kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-145">Blazor WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="e782a-146">Web tarayıcıları içinde .NET kodu çalıştırmak, [Webassembly](https://webassembly.org) *(kısaltılmış)* tarafından mümkün hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="e782a-147">WebAssembly hızlı indirme ve en yüksek yürütme hızı için iyileştirilmiş bir sıkıştırma kodu biçimidir.</span><span class="sxs-lookup"><span data-stu-id="e782a-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="e782a-148">WebAssembly, açık bir web standardıdır ve eklentileri olmayan Web tarayıcılarında desteklenir.</span><span class="sxs-lookup"><span data-stu-id="e782a-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="e782a-149">WebAssembly Code, JavaScript ile *birlikte çalışabilirlik* (veya *JavaScript birlikte çalışma*) olarak adlandırılan JavaScript aracılığıyla tarayıcının tüm işlevlerine erişebilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="e782a-150">Tarayıcıda WebAssembly aracılığıyla yürütülen .NET kodu, sanal makinenin istemci makinesindeki kötü amaçlı eylemlere karşı sağladığı korumalar ile tarayıcının JavaScript korumalı alanında çalışır.</span><span class="sxs-lookup"><span data-stu-id="e782a-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor WebAssembly, WebAssembly ile tarayıcıda .NET kodu çalıştırır.](index/_static/blazor-webassembly.png)

<span data-ttu-id="e782a-152">Bir Blazor WebAssembly uygulaması bir tarayıcıda oluşturulup çalıştırıldığında:</span><span class="sxs-lookup"><span data-stu-id="e782a-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="e782a-153">C#kod dosyaları ve Razor dosyaları .NET Derlemeleriyle derlenir.</span><span class="sxs-lookup"><span data-stu-id="e782a-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="e782a-154">Derlemeler ve .NET çalışma zamanı tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="e782a-155">.NET çalışma zamanı Blazor WebAssembly önyükleme ve çalışma zamanını uygulamanın derlemelerini yükleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e782a-155">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="e782a-156">Blazor WebAssembly çalışma zamanı, DOM işleme ve tarayıcı API çağrılarını işlemek için JavaScript birlikte çalışabilirliği kullanır.</span><span class="sxs-lookup"><span data-stu-id="e782a-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="e782a-157">Yayınlanan uygulamanın boyutu, *Yük boyutu*, uygulamanın useyeteneğinin önemli bir performans etkendir.</span><span class="sxs-lookup"><span data-stu-id="e782a-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="e782a-158">Büyük bir uygulamanın tarayıcıya indirmesi oldukça uzun sürer ve bu da Kullanıcı deneyimini azaltabilecek.</span><span class="sxs-lookup"><span data-stu-id="e782a-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="e782a-159">Blazor WebAssembly indirme sürelerini azaltmak için yük boyutunu iyileştirir:</span><span class="sxs-lookup"><span data-stu-id="e782a-159">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="e782a-160">Kullanılmayan kod, [ara dil (IL) bağlayıcı](xref:host-and-deploy/blazor/configure-linker)tarafından yayımlandığında uygulamadan çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="e782a-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="e782a-161">HTTP yanıtları sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="e782a-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="e782a-162">.NET çalışma zamanı ve derlemeler tarayıcıda önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="e782a-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="e782a-163">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="e782a-163">Blazor Server</span></span>

<span data-ttu-id="e782a-164">Blazor, Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır.</span><span class="sxs-lookup"><span data-stu-id="e782a-164">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="e782a-165">Blazor Server, Razor bileşenlerini bir ASP.NET Core uygulamasındaki sunucuda barındırmak için destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="e782a-165">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="e782a-166">Kullanıcı Arabirimi güncelleştirmeleri bir [SignalR](xref:signalr/introduction) bağlantısı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="e782a-166">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="e782a-167">Çalışma zamanı, tarayıcıdan sunucuya kullanıcı arabirimi olayları göndermeyi ve bileşenleri çalıştırdıktan sonra sunucu tarafından tarayıcıya geri gönderilen Kullanıcı arabirimi güncelleştirmelerini uygular.</span><span class="sxs-lookup"><span data-stu-id="e782a-167">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="e782a-168">Blazor sunucusu tarafından tarayıcıyla iletişim kurmak için kullanılan bağlantı, JavaScript birlikte çalışma çağrılarını işlemek için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e782a-168">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor Server, sunucuda .NET kodu çalıştırır ve bir SignalR bağlantısı üzerinden istemcideki Belge Nesne Modeli etkileşime girer](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="e782a-170">JavaScript ile birlikte çalışma</span><span class="sxs-lookup"><span data-stu-id="e782a-170">JavaScript interop</span></span>

<span data-ttu-id="e782a-171">Üçüncü taraf JavaScript kitaplıklarını ve tarayıcı API 'Lerine erişimi gerektiren uygulamalar için, bileşenler JavaScript ile birlikte çalışır.</span><span class="sxs-lookup"><span data-stu-id="e782a-171">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="e782a-172">Bileşenler, JavaScript 'in kullanabileceği herhangi bir kitaplığı veya API kullanma yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="e782a-172">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="e782a-173">C#kod JavaScript kodunu çağırabilir ve JavaScript kodu C# koda çağrı yapabilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-173">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="e782a-174">Daha fazla bilgi için bkz. <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="e782a-174">For more information, see <xref:blazor/javascript-interop>.</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="e782a-175">Kod paylaşımı ve .NET Standard</span><span class="sxs-lookup"><span data-stu-id="e782a-175">Code sharing and .NET Standard</span></span>

<span data-ttu-id="e782a-176">Blazor [2,0 uygular .NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="e782a-176">Blazor implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="e782a-177">.NET Standard, .NET uygulamaları genelinde ortak olan .NET API 'lerinin resmi bir belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="e782a-177">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="e782a-178">.NET Standard sınıf kitaplıkları, Blazor, .NET Framework, .NET Core, Xamarin, mono ve Unity gibi farklı .NET platformları arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="e782a-178">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="e782a-179">Bir Web tarayıcısı içinde geçerli olmayan API 'Ler (örneğin, dosya sistemine erişmek, bir yuva açmak ve iş parçacığı açmak) <xref:System.PlatformNotSupportedException> oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e782a-179">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e782a-180">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e782a-180">Additional resources</span></span>

* [<span data-ttu-id="e782a-181">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e782a-181">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [<span data-ttu-id="e782a-182">C# Kılavuzu</span><span class="sxs-lookup"><span data-stu-id="e782a-182">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="e782a-183">'SI</span><span class="sxs-lookup"><span data-stu-id="e782a-183">HTML</span></span>](https://www.w3.org/html/)
