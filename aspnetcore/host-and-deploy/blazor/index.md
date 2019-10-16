---
title: ASP.NET Core Blazor barındırma ve dağıtma
author: guardrex
description: Blazor uygulamalarının nasıl barındırılacağını ve dağıtılacağını öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 271135a0ebe67d31fd8e2bcf672e723814727147
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391335"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="694a3-103">ASP.NET Core Blazor barındırma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="694a3-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="694a3-104">, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="694a3-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="694a3-105">Uygulamayı yayımlama</span><span class="sxs-lookup"><span data-stu-id="694a3-105">Publish the app</span></span>

<span data-ttu-id="694a3-106">Uygulamalar yayın yapılandırmasında dağıtım için yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="694a3-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="694a3-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="694a3-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="694a3-108">Gezinti çubuğundan **derleme** >  **{uygulama} Yayımla** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="694a3-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="694a3-109">*Yayımla hedefini*seçin.</span><span class="sxs-lookup"><span data-stu-id="694a3-109">Select the *publish target*.</span></span> <span data-ttu-id="694a3-110">Yerel olarak yayımlamak için **klasör**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="694a3-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="694a3-111">**Klasör seçin** alanında varsayılan konumu kabul edin veya farklı bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="694a3-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="694a3-112">**Yayımla** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="694a3-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="694a3-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="694a3-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="694a3-114">Uygulamayı bir sürüm yapılandırmasıyla yayımlamak için [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="694a3-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="694a3-115">Uygulamanın yayımlanması, projenin bağımlılıklarını [geri yüklemeyi](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="694a3-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="694a3-116">Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan Yöntemler ve derlemeler kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="694a3-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="694a3-117">Blazor WebAssembly uygulaması */BIN/Release/{Target Framework}/publish/{ASSEMBLY Name}/Dist* klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="694a3-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="694a3-118">Blazor sunucu uygulaması */BIN/Release/{Target Framework}/Publish* klasörüne yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="694a3-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="694a3-119">Klasördeki varlıklar Web sunucusuna dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="694a3-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="694a3-120">Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir süreç olabilir.</span><span class="sxs-lookup"><span data-stu-id="694a3-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="694a3-121">Uygulama temel yolu</span><span class="sxs-lookup"><span data-stu-id="694a3-121">App base path</span></span>

<span data-ttu-id="694a3-122">*Uygulama temel yolu* , UYGULAMANıN kök URL yoludur.</span><span class="sxs-lookup"><span data-stu-id="694a3-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="694a3-123">Aşağıdaki ana uygulamayı ve Blazor uygulamasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="694a3-123">Consider the following main app and Blazor app:</span></span>

* <span data-ttu-id="694a3-124">Ana uygulama @no__t çağrıldı-0:</span><span class="sxs-lookup"><span data-stu-id="694a3-124">The main app is called `MyApp`:</span></span>
  * <span data-ttu-id="694a3-125">Uygulama fiziksel olarak *d: \\MyApp*konumunda bulunur.</span><span class="sxs-lookup"><span data-stu-id="694a3-125">The app physically resides at *d:\\MyApp*.</span></span>
  * <span data-ttu-id="694a3-126">İstekler `https://www.contoso.com/{MYAPP RESOURCE}` ' a alınır.</span><span class="sxs-lookup"><span data-stu-id="694a3-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="694a3-127">@No__t-0 adlı bir Blazor uygulaması, `MyApp` ' in bir alt uygulamasıdır:</span><span class="sxs-lookup"><span data-stu-id="694a3-127">A Blazor app called `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="694a3-128">Alt uygulama fiziksel olarak *d: \\MyApp @ no__t-2CoolApp*konumunda bulunur.</span><span class="sxs-lookup"><span data-stu-id="694a3-128">The sub-app physically resides at *d:\\MyApp\\CoolApp*.</span></span>
  * <span data-ttu-id="694a3-129">İstekler `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` ' a alınır.</span><span class="sxs-lookup"><span data-stu-id="694a3-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="694a3-130">@No__t-0 için ek yapılandırma belirtmeden, Bu senaryodaki alt uygulama, sunucuda nerede bulunduğu konusunda hiçbir bilgiye sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="694a3-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="694a3-131">Örneğin, uygulama ilgili URL yolunda `/CoolApp/` ' da olduğunu bilmeden, kaynaklarına doğru göreli URL 'Ler oluşturamıyoruz.</span><span class="sxs-lookup"><span data-stu-id="694a3-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="694a3-132">Blazor uygulamasının temel yolu olan `https://www.contoso.com/CoolApp/` için yapılandırma sağlamak üzere, `<base>` etiketinin `href` özniteliği *Wwwroot/index.html* dosyasındaki göreli kök yoluna ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="694a3-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *wwwroot/index.html* file:</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="694a3-133">Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL 'Ler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="694a3-133">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="694a3-134">Farklı dizin yapısı düzeylerindeki bileşenler, uygulama genelinde konumlardaki diğer kaynakların bağlantılarını oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="694a3-134">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="694a3-135">Uygulama temel yolu Ayrıca, bağlantının `href` hedefinin uygulama temel yolu URI alanı içinde olduğu yerlerde köprü tıklamalarını (@ no__t-1Blazor yönlendiricisi iç gezintiyi işler.</span><span class="sxs-lookup"><span data-stu-id="694a3-135">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="694a3-136">Birçok barındırma senaryosunda, uygulamanın göreli URL yolu uygulamanın köküdür.</span><span class="sxs-lookup"><span data-stu-id="694a3-136">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="694a3-137">Bu durumlarda, uygulamanın göreli URL taban yolu, bir Blazor uygulamasının varsayılan yapılandırması olan bir eğik çizgi (`<base href="/" />`) olur.</span><span class="sxs-lookup"><span data-stu-id="694a3-137">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="694a3-138">GitHub sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu, sunucunun uygulamanın göreli URL 'SI yolu olarak ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="694a3-138">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path to the app.</span></span>

<span data-ttu-id="694a3-139">Uygulamanın temel yolunu ayarlamak için, *Wwwroot/index.html* dosyasının `<head>` etiket öğeleri içindeki `<base>` etiketini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="694a3-139">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="694a3-140">@No__t-0 öznitelik değerini `/{RELATIVE URL PATH}/` olarak ayarlayın (sondaki eğik çizgi gereklidir); burada `{RELATIVE URL PATH}`, uygulamanın tam göreli URL yoludur.</span><span class="sxs-lookup"><span data-stu-id="694a3-140">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="694a3-141">Kök olmayan göreli URL yoluna (örneğin, `<base href="/CoolApp/">`) sahip bir uygulama için, uygulama *yerel olarak çalıştırıldığında*kaynaklarını bulamaz.</span><span class="sxs-lookup"><span data-stu-id="694a3-141">For an app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="694a3-142">Yerel geliştirme ve test sırasında bu sorunu aşmak için, çalışma zamanında `<base>` etiketinin `href` değeriyle eşleşen bir *yol temel* bağımsız değişkeni sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="694a3-142">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="694a3-143">Uygulamayı yerel olarak çalıştırırken yol temel bağımsız değişkenini geçirmek için, `--pathbase` seçeneğiyle uygulamanın dizininden `dotnet run` komutunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="694a3-143">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="694a3-144">@No__t-0 (`<base href="/CoolApp/">`) göreli URL yoluna sahip bir uygulama için, komut şu olur:</span><span class="sxs-lookup"><span data-stu-id="694a3-144">For an app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="694a3-145">Uygulama, `http://localhost:port/CoolApp` ' da yerel olarak yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="694a3-145">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="694a3-146">Dağıtım</span><span class="sxs-lookup"><span data-stu-id="694a3-146">Deployment</span></span>

<span data-ttu-id="694a3-147">Dağıtım Kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="694a3-147">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
