---
title: Razor bileşenleri modelleri barındırma
author: guardrex
description: İstemci tarafı Blazor ve sunucu tarafı ASP.NET Core Razor modelleri barındırma bileşenlerini anlayın.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/hosting-models
ms.openlocfilehash: d1e0c472d7d10eeb4cef0da735cf703c98dd1645
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159506"
---
# <a name="razor-components-hosting-models"></a><span data-ttu-id="0966b-103">Razor bileşenleri modelleri barındırma</span><span class="sxs-lookup"><span data-stu-id="0966b-103">Razor Components hosting models</span></span>

<span data-ttu-id="0966b-104">Tarafından [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0966b-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="0966b-105">Razor bileşenleri, istemci tarafı çalışmak üzere tasarlanmış bir web çerçevesi olan WebAssembly tabanlı .NET çalışma zamanı tarayıcıda (*Blazor*) veya sunucu tarafı ASP.NET Core (*ASP.NET Core Razor bileşenleri*).</span><span class="sxs-lookup"><span data-stu-id="0966b-105">Razor Components is a web framework designed to run client-side in the browser on a WebAssembly-based .NET runtime (*Blazor*) or server-side in ASP.NET Core (*ASP.NET Core Razor Components*).</span></span> <span data-ttu-id="0966b-106">Barındırma modeli, uygulama ve bileşen modelleri bakılmaksızın *aynı kalması*.</span><span class="sxs-lookup"><span data-stu-id="0966b-106">Regardless of the hosting model, the app and component models *remain the same*.</span></span> <span data-ttu-id="0966b-107">Bu makalede, kullanılabilen barındırma modelleri açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0966b-107">This article discusses the available hosting models.</span></span>

## <a name="client-side-hosting-model"></a><span data-ttu-id="0966b-108">İstemci tarafı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="0966b-108">Client-side hosting model</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="0966b-109">Asıl barındırma Blazor için tarayıcıda çalışan istemci-tarafı modelidir.</span><span class="sxs-lookup"><span data-stu-id="0966b-109">The principal hosting model for Blazor is running client-side in the browser.</span></span> <span data-ttu-id="0966b-110">Bu modelde, tarayıcıya .NET çalışma zamanı Blazor uygulamayı ve bağımlılıkları indirilir.</span><span class="sxs-lookup"><span data-stu-id="0966b-110">In this model, the Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="0966b-111">Uygulamayı doğrudan tarayıcıda kullanıcı Arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="0966b-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="0966b-112">Tüm kullanıcı Arabirimi güncelleştirmeleri ve olay işleme, aynı işlem içinde gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="0966b-112">All UI updates and event handling happens within the same process.</span></span> <span data-ttu-id="0966b-113">Uygulama varlıkları ne olursa olsun web sunucusunu tercih edilen kullanarak statik dosya olarak dağıtılabilir (bkz [konak dağıtıp](xref:host-and-deploy/razor-components/index)).</span><span class="sxs-lookup"><span data-stu-id="0966b-113">The app assets can be deployed as static files using whatever web server is preferred (see [Host and deploy](xref:host-and-deploy/razor-components/index)).</span></span>

![Blazor istemci-tarafı: Tarayıcı içinde bir kullanıcı Arabirimi iş parçacığında Blazor uygulama çalışır.](hosting-models/_static/client-side.png)

<span data-ttu-id="0966b-115">İstemci tarafı barındırma modeli kullanarak bir Blazor uygulaması oluşturmak için kullanın **Blazor** veya **Blazor (ASP.NET Core barındırılan)** proje şablonları (`blazor` veya `blazorhosted` kullanırkenşablonu[yeni dotnet](/dotnet/core/tools/dotnet-new) komutu bir komut isteminde).</span><span class="sxs-lookup"><span data-stu-id="0966b-115">To create a Blazor app using the client-side hosting model, use the **Blazor** or **Blazor (ASP.NET Core Hosted)** project templates (`blazor` or `blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command at a command prompt).</span></span> <span data-ttu-id="0966b-116">Dahil edilen *blazor.webassembly.js* betik tanıtıcıları:</span><span class="sxs-lookup"><span data-stu-id="0966b-116">The included *blazor.webassembly.js* script handles:</span></span>

* <span data-ttu-id="0966b-117">.NET çalışma zamanı, uygulama ve onun bağımlılıklarını karşıdan yükleniyor.</span><span class="sxs-lookup"><span data-stu-id="0966b-117">Downloading the .NET runtime, the app, and its dependencies.</span></span>
* <span data-ttu-id="0966b-118">Uygulamayı çalıştırmak için çalışma zamanı başlatma.</span><span class="sxs-lookup"><span data-stu-id="0966b-118">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="0966b-119">İstemci tarafı barındırma modeli, çeşitli avantajlar sunar.</span><span class="sxs-lookup"><span data-stu-id="0966b-119">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="0966b-120">İstemci tarafı Blazor:</span><span class="sxs-lookup"><span data-stu-id="0966b-120">Client-side Blazor:</span></span>

* <span data-ttu-id="0966b-121">.NET sunucu tarafı bağımlılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="0966b-121">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="0966b-122">Bir zengin etkileşimli kullanıcı Arabirimi var.</span><span class="sxs-lookup"><span data-stu-id="0966b-122">Has a rich interactive UI.</span></span>
* <span data-ttu-id="0966b-123">İstemci kaynakları ve özellikleri tam olarak yararlanır.</span><span class="sxs-lookup"><span data-stu-id="0966b-123">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="0966b-124">Yük boşaltma, sunucudan istemciye çalışır.</span><span class="sxs-lookup"><span data-stu-id="0966b-124">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="0966b-125">Çevrimdışı senaryolarını destekler.</span><span class="sxs-lookup"><span data-stu-id="0966b-125">Supports offline scenarios.</span></span>

<span data-ttu-id="0966b-126">İstemci tarafı barındırma downsides vardır.</span><span class="sxs-lookup"><span data-stu-id="0966b-126">There are downsides to client-side hosting.</span></span> <span data-ttu-id="0966b-127">İstemci tarafı Blazor:</span><span class="sxs-lookup"><span data-stu-id="0966b-127">Client-side Blazor:</span></span>

* <span data-ttu-id="0966b-128">Uygulama, tarayıcının yeteneklerini kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="0966b-128">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="0966b-129">Özellikli istemci donanım ve yazılım (örneğin, WebAssembly desteği) gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0966b-129">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="0966b-130">İndirme boyutu daha büyük ve uzun uygulama yükleme süresi vardır.</span><span class="sxs-lookup"><span data-stu-id="0966b-130">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="0966b-131">.NET çalışma zamanı ve araç desteği (örneğin, .NET Standard desteği ve hata ayıklama sınırlamaları) yetişkin daha az sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0966b-131">Has less mature .NET runtime and tooling support (for example, limitations in .NET Standard support and debugging).</span></span>

<span data-ttu-id="0966b-132">Visual Studio içerir **Blazor (barındırılan ASP.NET Core)** WebAssembly üzerinde çalışır ve bir ASP.NET Core sunucusunda barındırılan bir Blazor uygulaması oluşturmaya yönelik proje şablonu.</span><span class="sxs-lookup"><span data-stu-id="0966b-132">Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template for creating a Blazor app that runs on WebAssembly and is hosted on an ASP.NET Core server.</span></span> <span data-ttu-id="0966b-133">ASP.NET Core uygulaması Blazor uygulama istemcilere hizmet ancak Aksi durumda, ayrı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="0966b-133">The ASP.NET Core app serves the Blazor app to clients but is otherwise a separate process.</span></span> <span data-ttu-id="0966b-134">İstemci tarafı Blazor uygulama sunucusu ile Web API çağrıları veya SignalR bağlantıları kullanarak ağ üzerinden etkileşim kurabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0966b-134">The client-side Blazor app can interact with the server over the network using Web API calls or SignalR connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0966b-135">Bir istemci-tarafı Blazor uygulaması bir IIS alt uygulama barındırılan bir ASP.NET Core uygulaması tarafından sunulan devralınan ASP.NET Core modülü işleyici devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="0966b-135">If a client-side Blazor app is served by an ASP.NET Core app hosted as an IIS sub-app, disable the inherited ASP.NET Core Module handler.</span></span> <span data-ttu-id="0966b-136">Blazor uygulaması'nın uygulama temel yolu ayarla *index.html* IIS diğer IIS alt uygulama yapılandırma sırasında kullanılan dosya.</span><span class="sxs-lookup"><span data-stu-id="0966b-136">Set the app base path in the Blazor app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>
>
> <span data-ttu-id="0966b-137">Daha fazla bilgi için [uygulama temel yolu](xref:host-and-deploy/razor-components/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="0966b-137">For more information, see [App base path](xref:host-and-deploy/razor-components/index#app-base-path).</span></span>

## <a name="server-side-hosting-model"></a><span data-ttu-id="0966b-138">Sunucu tarafı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="0966b-138">Server-side hosting model</span></span>

<span data-ttu-id="0966b-139">ASP.NET Core Razor bileşenleri sunucu tarafı barındırma modeli içinde uygulama sunucusunda bir ASP.NET Core uygulaması içinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="0966b-139">In the ASP.NET Core Razor Components server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="0966b-140">Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrılarını bir SignalR bağlantısı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="0966b-140">UI updates, event handling, and JavaScript calls are handled over a SignalR connection.</span></span>

![ASP.NET Core Razor bileşenleri sunucu-tarafı: Tarayıcı uygulaması (bir ASP.NET Core uygulaması içinde barındırılan) ile sunucu üzerinde bir SignalR bağlantısı üzerinden etkileşim kurar.](hosting-models/_static/server-side.png)

<span data-ttu-id="0966b-142">Sunucu tarafı barındırma modeli kullanarak bir Razor bileşenleri uygulaması oluşturmak için kullanın **Blazor (sunucu tarafı ASP.NET core'da)** şablonu (`blazorserver` kullanırken [yeni dotnet](/dotnet/core/tools/dotnet-new) bir komut isteminde).</span><span class="sxs-lookup"><span data-stu-id="0966b-142">To create a Razor Components app using the server-side hosting model, use the **Blazor (Server-side in ASP.NET Core)** template (`blazorserver` when using [dotnet new](/dotnet/core/tools/dotnet-new) at a command prompt).</span></span> <span data-ttu-id="0966b-143">ASP.NET Core uygulaması Razor bileşenleri sunucu-tarafı uygulamasını barındıran ve istemcilerin eriştikleri SignalR uç noktasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0966b-143">An ASP.NET Core app hosts the Razor Components server-side app and sets up the SignalR endpoint where clients connect.</span></span> <span data-ttu-id="0966b-144">ASP.NET Core uygulaması uygulamanın başvuran `Startup` sınıfı eklemek için:</span><span class="sxs-lookup"><span data-stu-id="0966b-144">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="0966b-145">Sunucu tarafı Razor bileşenleri Hizmetleri.</span><span class="sxs-lookup"><span data-stu-id="0966b-145">Server-side Razor Components services.</span></span>
* <span data-ttu-id="0966b-146">Ardışık Düzen işleme isteği için uygulama.</span><span class="sxs-lookup"><span data-stu-id="0966b-146">The app to the request handling pipeline.</span></span>

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

<span data-ttu-id="0966b-147">*Blazor.server.js* betik&dagger; istemci bağlantı kurar.</span><span class="sxs-lookup"><span data-stu-id="0966b-147">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="0966b-148">Bunu kalıcı hale getirmek ve gerektiğinde (örneğin, durumunda kayıp ağ bağlantısı) uygulama durumunu geri yüklemek için uygulamanın sorumluluğudur.</span><span class="sxs-lookup"><span data-stu-id="0966b-148">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="0966b-149">Sunucu tarafı barındırma modeli, çeşitli avantajlar sunar:</span><span class="sxs-lookup"><span data-stu-id="0966b-149">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="0966b-150">Tüm .NET uygulamanızla yazmanıza olanak sağlar ve C# bileşen modeli kullanarak.</span><span class="sxs-lookup"><span data-stu-id="0966b-150">Allows you to write your entire app with .NET and C# using the component model.</span></span>
* <span data-ttu-id="0966b-151">Zengin, etkileşimli bir görünümünü sağlar ve gereksiz sayfa yenilenir önler.</span><span class="sxs-lookup"><span data-stu-id="0966b-151">Provides a rich interactive feel and avoids unnecessary page refreshes.</span></span>
* <span data-ttu-id="0966b-152">Bir istemci-tarafı Blazor uygulaması daha önemli ölçüde daha küçük bir uygulama boyutu vardır ve çok daha hızlı yükler.</span><span class="sxs-lookup"><span data-stu-id="0966b-152">Has a significantly smaller app size than a client-side Blazor app and loads much faster.</span></span>
* <span data-ttu-id="0966b-153">Bileşen mantığı server özellikleri, herhangi bir .NET Core uyumlu API'sini kullanma dahil olmak üzere tüm avantajlarından yararlanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0966b-153">Component logic can take full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="0966b-154">Araç, hata ayıklama gibi mevcut .NET beklendiği gibi çalışır. Bu nedenle .NET Core üzerinde sunucu üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="0966b-154">Runs on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="0966b-155">Çalışan ince istemciler (örneğin, WebAssembly ve kaynak desteklemeyen tarayıcılar cihazları kısıtlı).</span><span class="sxs-lookup"><span data-stu-id="0966b-155">Works with thin clients (for example, browsers that don't support WebAssembly and resource constrained devices).</span></span>

<span data-ttu-id="0966b-156">Sunucu tarafı barındırma downsides vardır:</span><span class="sxs-lookup"><span data-stu-id="0966b-156">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="0966b-157">Daha yüksek gecikme süresi vardır: Her bir kullanıcı etkileşimi bir ağ atlama içerir.</span><span class="sxs-lookup"><span data-stu-id="0966b-157">Has higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="0966b-158">Çevrimdışı desteği sunar: İstemci bağlantı başarısız olursa, Uygulama çalışmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="0966b-158">Offers no offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="0966b-159">Ölçeklenebilirlik azalttı: Sunucu, birden çok istemci bağlantılarını yönetme ve istemci durumu işleme.</span><span class="sxs-lookup"><span data-stu-id="0966b-159">Has reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="0966b-160">Uygulama hizmet vermek için bir ASP.NET Core sunucusu gerekir.</span><span class="sxs-lookup"><span data-stu-id="0966b-160">Requires an ASP.NET Core server to serve the app.</span></span> <span data-ttu-id="0966b-161">Dağıtım bir sunucudan (örneğin, bir CDN) olmadan mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="0966b-161">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="0966b-162">&dagger;*Blazor.server.js* betik aşağıdaki yola yayımlanan: *bin / {hata ayıklama | Yayın} / {hedef çerçeve} /publish/ {uygulama-adı}. Uygulama/dağıtım/_framework*.</span><span class="sxs-lookup"><span data-stu-id="0966b-162">&dagger;The *blazor.server.js* script is published to the following path: *bin/{Debug|Release}/{TARGET FRAMEWORK}/publish/{APPLICATION NAME}.App/dist/_framework*.</span></span>
