---
title: Blazor barındırma modelleri
author: guardrex
description: İstemci tarafı ve sunucu tarafı Blazor modelleri barındırma anlayın.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: blazor/hosting-models
ms.openlocfilehash: 23821757bc541c8d17a2c1d6d75c5443b57a9135
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874856"
---
# <a name="blazor-hosting-models"></a><span data-ttu-id="82fe4-103">Blazor barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="82fe4-103">Blazor hosting models</span></span>

<span data-ttu-id="82fe4-104">Tarafından [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="82fe4-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="82fe4-105">Blazor istemci-tarafı çalışmak üzere tasarlanmış bir web çerçevesi olan tarayıcıda bir [WebAssembly](http://webassembly.org/)-.NET çalışma zamanı tabanlı (*Blazor istemci-tarafı*) veya sunucu tarafı ASP.NET Core (*Blazor sunucu tarafı* ).</span><span class="sxs-lookup"><span data-stu-id="82fe4-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](http://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="82fe4-106">Barındırma modeli, uygulama ve bileşen modelleri bakılmaksızın *aynı*.</span><span class="sxs-lookup"><span data-stu-id="82fe4-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="82fe4-107">Bu makalede açıklanan barındırma modellerine yönelik bir proje oluşturmak için bkz: <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="82fe4-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="82fe4-108">İstemci tarafı</span><span class="sxs-lookup"><span data-stu-id="82fe4-108">Client-side</span></span>

<span data-ttu-id="82fe4-109">Asıl barındırma için Blazor WebAssembly tarayıcıda çalışan istemci-tarafı modelidir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="82fe4-110">Tarayıcıya .NET çalışma zamanı Blazor uygulamayı ve bağımlılıkları indirilir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="82fe4-111">Uygulamayı doğrudan tarayıcıda kullanıcı Arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="82fe4-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="82fe4-112">Kullanıcı Arabirimi güncelleştirmeleri ve olay işleme, aynı işlem içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="82fe4-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="82fe4-113">Statik dosya olarak bir web sunucusu veya hizmeti statik içeriği istemcilere hizmet uygulamasının varlıklarını dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor istemci-tarafı: Tarayıcı içinde bir kullanıcı Arabirimi iş parçacığında Blazor uygulama çalışır.](hosting-models/_static/client-side.png)

<span data-ttu-id="82fe4-115">İstemci tarafı barındırma modeli kullanarak bir Blazor uygulaması oluşturmak için aşağıdaki şablonlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="82fe4-115">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="82fe4-116">**(İstemci-tarafı) Blazor** ([dotnet yeni blazor](/dotnet/core/tools/dotnet-new)) &ndash; statik dosyalar bir dizi dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-116">**Blazor (client-side)** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="82fe4-117">**Blazor (ASP.NET Core barındırılan)** ([dotnet yeni blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; bir ASP.NET Core sunucusu tarafından barındırılan.</span><span class="sxs-lookup"><span data-stu-id="82fe4-117">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="82fe4-118">ASP.NET Core uygulaması Blazor uygulama istemcilere hizmet.</span><span class="sxs-lookup"><span data-stu-id="82fe4-118">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="82fe4-119">Web API çağrıları kullanarak ağ üzerinden istemci-tarafı Blazor uygulama sunucusu ile etkileşim kurabilir veya [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="82fe4-119">The client-side Blazor app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="82fe4-120">Şablonları içerir *blazor.webassembly.js* işleyen betik:</span><span class="sxs-lookup"><span data-stu-id="82fe4-120">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="82fe4-121">.NET çalışma zamanı, uygulama ve uygulamanın bağımlılıklarını karşıdan yükleniyor.</span><span class="sxs-lookup"><span data-stu-id="82fe4-121">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="82fe4-122">Uygulamayı çalıştırmak için çalışma zamanı başlatma.</span><span class="sxs-lookup"><span data-stu-id="82fe4-122">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="82fe4-123">İstemci tarafı barındırma modeli, çeşitli avantajlar sunar.</span><span class="sxs-lookup"><span data-stu-id="82fe4-123">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="82fe4-124">İstemci tarafı Blazor:</span><span class="sxs-lookup"><span data-stu-id="82fe4-124">Client-side Blazor:</span></span>

* <span data-ttu-id="82fe4-125">.NET sunucu tarafı bağımlılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="82fe4-125">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="82fe4-126">İstemci kaynakları ve özellikleri tam olarak yararlanır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-126">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="82fe4-127">Yük boşaltma, sunucudan istemciye çalışır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-127">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="82fe4-128">Çevrimdışı senaryolarını destekler.</span><span class="sxs-lookup"><span data-stu-id="82fe4-128">Supports offline scenarios.</span></span>

<span data-ttu-id="82fe4-129">İstemci tarafı barındırma downsides vardır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-129">There are downsides to client-side hosting.</span></span> <span data-ttu-id="82fe4-130">İstemci tarafı Blazor:</span><span class="sxs-lookup"><span data-stu-id="82fe4-130">Client-side Blazor:</span></span>

* <span data-ttu-id="82fe4-131">Uygulama, tarayıcının yeteneklerini kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="82fe4-131">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="82fe4-132">Özellikli istemci donanım ve yazılım (örneğin, WebAssembly desteği) gerektirir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-132">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="82fe4-133">İndirme boyutu daha büyük ve uzun uygulama yükleme süresi vardır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-133">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="82fe4-134">.NET çalışma zamanı ve araç desteği yetişkin daha az olan (örneğin, kısıtlamaları [.NET Standard](/dotnet/standard/net-standard) desteği ve hata ayıklama).</span><span class="sxs-lookup"><span data-stu-id="82fe4-134">Has less mature .NET runtime and tooling support (for example, limitations in [.NET Standard](/dotnet/standard/net-standard) support and debugging).</span></span>

## <a name="server-side"></a><span data-ttu-id="82fe4-135">Sunucu tarafı</span><span class="sxs-lookup"><span data-stu-id="82fe4-135">Server-side</span></span>

<span data-ttu-id="82fe4-136">Sunucu tarafı barındırma modeli ile uygulama sunucusunda bir ASP.NET Core uygulaması içinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="82fe4-136">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="82fe4-137">Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrılarını üzerinden işlenir bir [SignalR](xref:signalr/introduction) bağlantı.</span><span class="sxs-lookup"><span data-stu-id="82fe4-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Tarayıcı uygulaması (bir ASP.NET Core uygulaması içinde barındırılan) ile sunucu üzerinde bir SignalR bağlantısı üzerinden etkileşim kurar.](hosting-models/_static/server-side.png)

<span data-ttu-id="82fe4-139">Sunucu tarafı barındırma modeli kullanarak bir Blazor uygulaması oluşturmak için ASP.NET Core kullanan **Blazor (sunucu tarafı)** şablonu ([dotnet yeni blazorserverside](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="82fe4-139">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor (server-side)** template ([dotnet new blazorserverside](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="82fe4-140">ASP.NET Core uygulaması, sunucu tarafı uygulamayı barındıran ve istemcilerin eriştikleri SignalR uç noktasını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="82fe4-140">The ASP.NET Core app hosts the server-side app and sets up the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="82fe4-141">ASP.NET Core uygulaması uygulamanın başvuran `Startup` sınıfı eklemek için:</span><span class="sxs-lookup"><span data-stu-id="82fe4-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="82fe4-142">Sunucu tarafı hizmetler.</span><span class="sxs-lookup"><span data-stu-id="82fe4-142">Server-side services.</span></span>
* <span data-ttu-id="82fe4-143">Ardışık Düzen işleme isteği için uygulama.</span><span class="sxs-lookup"><span data-stu-id="82fe4-143">The app to the request handling pipeline.</span></span>

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

<span data-ttu-id="82fe4-144">*Blazor.server.js* betik&dagger; istemci bağlantı kurar.</span><span class="sxs-lookup"><span data-stu-id="82fe4-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="82fe4-145">Bunu kalıcı hale getirmek ve gerektiğinde (örneğin, durumunda kayıp ağ bağlantısı) uygulama durumunu geri yüklemek için uygulamanın sorumluluğudur.</span><span class="sxs-lookup"><span data-stu-id="82fe4-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="82fe4-146">Sunucu tarafı barındırma modeli, çeşitli avantajlar sunar:</span><span class="sxs-lookup"><span data-stu-id="82fe4-146">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="82fe4-147">Bir istemci-tarafı uygulaması daha önemli ölçüde daha küçük bir uygulama boyutuna sahiptir ve çok daha hızlı yükler.</span><span class="sxs-lookup"><span data-stu-id="82fe4-147">Has a significantly smaller app size than a client-side app and loads much faster.</span></span>
* <span data-ttu-id="82fe4-148">Sunucu özellikleri, herhangi bir .NET Core uyumlu API'sini kullanma dahil olmak üzere tüm avantajlarından alır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-148">Takes full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="82fe4-149">Araç, hata ayıklama gibi mevcut .NET beklendiği gibi çalışır. Bu nedenle .NET Core üzerinde sunucu üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-149">Runs on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="82fe4-150">İnce istemciler ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-150">Works with thin clients.</span></span> <span data-ttu-id="82fe4-151">Örneğin, WebAssembly ve kaynak desteklemeyen tarayıcılar ile çalışır, cihazları sınırlı.</span><span class="sxs-lookup"><span data-stu-id="82fe4-151">For example, works with browsers that don't support WebAssembly and resource constrained devices.</span></span>
* <span data-ttu-id="82fe4-152">.NET /C# kod tabanına uygulama bileşeni kod dahil olmak üzere, istemcilere hizmet değil.</span><span class="sxs-lookup"><span data-stu-id="82fe4-152">The .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="82fe4-153">Sunucu tarafı barındırma downsides vardır:</span><span class="sxs-lookup"><span data-stu-id="82fe4-153">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="82fe4-154">Daha yüksek gecikme süresi: Her bir kullanıcı etkileşimi bir ağ atlama içerir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-154">Higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="82fe4-155">Çevrimdışı desteği: İstemci bağlantı başarısız olursa, Uygulama çalışmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="82fe4-155">No offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="82fe4-156">Sınırlı ölçeklenebilirlik: Sunucu, birden çok istemci bağlantılarını yönetme ve istemci durumu işleme.</span><span class="sxs-lookup"><span data-stu-id="82fe4-156">Reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="82fe4-157">Bir ASP.NET Core sunucusu uygulama hizmet vermek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-157">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="82fe4-158">Dağıtım bir sunucudan (örneğin, bir CDN) olmadan mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-158">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="82fe4-159">&dagger;*Blazor.server.js* betik sunulan ASP.NET Core paylaşılan Framework katıştırılmış bir kaynaktan.</span><span class="sxs-lookup"><span data-stu-id="82fe4-159">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="82fe4-160">Aynı sunucuya yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="82fe4-160">Reconnection to the same server</span></span>

<span data-ttu-id="82fe4-161">Sunucu tarafı uygulamalar Blazor sunucunun etkin bir SignalR bağlantısı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-161">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="82fe4-162">Bir bağlantı kaybedilirse uygulamanın sunucuya yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="82fe4-162">If a connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="82fe4-163">Bellekte hala istemcinin durum olduğu sürece, istemci oturum durumunu kaybetmeden sürdürür.</span><span class="sxs-lookup"><span data-stu-id="82fe4-163">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="82fe4-164">İstemci bağlantısı kesildi algıladığında, istemci yeniden bağlanmayı dener ancak bir varsayılan kullanıcı arabirimini kullanıcıya görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-164">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="82fe4-165">Yeniden bağlanma başarısız olursa, kullanıcı yeniden denemek için seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-165">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="82fe4-166">Kullanıcı arabirimini özelleştirmek için bir öğe ile tanımlama `components-reconnect-modal` olarak kendi `id`.</span><span class="sxs-lookup"><span data-stu-id="82fe4-166">To customize the UI, define an element with `components-reconnect-modal` as its `id`.</span></span> <span data-ttu-id="82fe4-167">İstemci bu öğe, bağlantı durumuna bağlı aşağıdaki CSS sınıflarının biri ile güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="82fe4-167">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="82fe4-168">`components-reconnect-show` &ndash; Bağlantı kesildi ve istemci yeniden bağlanmayı deniyor göstermek için uygulamanın UI göstermesi.</span><span class="sxs-lookup"><span data-stu-id="82fe4-168">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="82fe4-169">`components-reconnect-hide` &ndash; İstemci UI Gizle etkin bir bağlantı vardır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-169">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="82fe4-170">`components-reconnect-failed` &ndash; Yeniden bağlanma başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="82fe4-170">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="82fe4-171">Yeniden bağlanmayı yeniden denemek için çağrı `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="82fe4-171">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="82fe4-172">Prerendering sonra durum bilgisi olan yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="82fe4-172">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="82fe4-173">Blazor sunucu tarafı uygulamalar varsayılan olarak sunucuya istemci bağlantı kurulmadan önce sunucuda UI prerender şekilde ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-173">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="82fe4-174">Bu, ayarlanır *_Host.cshtml* Razor sayfası:</span><span class="sxs-lookup"><span data-stu-id="82fe4-174">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
<span data-ttu-id="82fe4-175">İstemci uygulama prerender için kullanılan aynı duruma sunucusuna bağlanır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-175">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="82fe4-176">Uygulamanın durumu bellekte hala varsa, SignalR bağlantı kurulduktan sonra bileşen durumu rerendered değil.</span><span class="sxs-lookup"><span data-stu-id="82fe4-176">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="82fe4-177">Durum bilgisi olan etkileşimli bileşenleri Razor sayfaları ve görünümler oluşturma</span><span class="sxs-lookup"><span data-stu-id="82fe4-177">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="82fe4-178">Durum bilgisi olan etkileşimli bileşenleri bir Razor sayfası ya da Görünüm eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-178">Stateful interactive components can be added to a Razor page or view.</span></span> <span data-ttu-id="82fe4-179">Sayfa veya Görünüm oluşturulduğunda, bileşeni ile prerendered.</span><span class="sxs-lookup"><span data-stu-id="82fe4-179">When the page or view renders, the component is prerendered with it.</span></span> <span data-ttu-id="82fe4-180">Durumu bellekte hala olduğu sürece, istemci bağlantısı kurulduktan sonra uygulama için bileşen durumu daha sonra yeniden bağlanır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-180">The app then reconnects to the component state once the client connection is established as long as the state is still in memory.</span></span>
 
<span data-ttu-id="82fe4-181">Örneğin, bir sayaç bileşeni ile form kullanarak belirtilen bir başlangıç sayısı aşağıdaki Razor sayfası oluşturur:</span><span class="sxs-lookup"><span data-stu-id="82fe4-181">For example, the following Razor page renders a Counter component with an initial count that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="82fe4-182">Uygulama prerendering Algıla</span><span class="sxs-lookup"><span data-stu-id="82fe4-182">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="82fe4-183">SignalR istemcisi Blazor sunucu tarafı uygulamalar için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="82fe4-183">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="82fe4-184">Bazen, Blazor sunucu tarafı uygulamalar tarafından kullanılan SignalR istemci yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="82fe4-184">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="82fe4-185">Örneğin, bir bağlantı sorunu tanılamak için SignalR istemci günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="82fe4-185">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="82fe4-186">SignalR istemcisinde yapılandırmak zorunda *sayfaları /\_Host.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="82fe4-186">To configure the SignalR client in the *Pages/\_Host.cshtml* file:</span></span>

* <span data-ttu-id="82fe4-187">Ekleme bir `autostart="false"` özniteliğini `<script>` etiketinde *blazor.server.js* betiği.</span><span class="sxs-lookup"><span data-stu-id="82fe4-187">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="82fe4-188">Çağrı `Blazor.start` ve SignalR Oluşturucu belirten bir yapılandırma nesnesi içinde geçirin.</span><span class="sxs-lookup"><span data-stu-id="82fe4-188">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

### <a name="improved-signalr-connection-lifetime-handling"></a><span data-ttu-id="82fe4-189">Geliştirilmiş SignalR bağlantı ömrü işleme</span><span class="sxs-lookup"><span data-stu-id="82fe4-189">Improved SignalR connection lifetime handling</span></span>

<span data-ttu-id="82fe4-190">Otomatik yeniden bağlantılar çağrılarak etkinleştirilebilir `withAutomaticReconnect` metodunda `HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="82fe4-190">Automatic reconnects can be enabled by calling the `withAutomaticReconnect` method on `HubConnectionBuilder`:</span></span>

```csharp
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="82fe4-191">Parametreleri belirtmeden `withAutomaticReconnect` her girişimden 0, 2, 10 ve 30 saniye bekledikten sonra yeniden bağlanmayı deneyin üzere istemciyi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="82fe4-191">Without specifying parameters, `withAutomaticReconnect` configures the client to try to reconnect, waiting 0, 2, 10, and 30 seconds between each attempt.</span></span>

<span data-ttu-id="82fe4-192">Hatasından önce sitede yeniden bağlanma denemesi sayısı varsayılan olmayan yapılandırma veya yeniden zamanlamasını değiştirmek için `withAutomaticReconnect` yeniden bağlanma girişimleri başlatmadan önce beklenecek milisaniye cinsinden gecikme değeri temsil eden sayı dizisi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="82fe4-192">To configure a non-default number of reconnect attempts before failure or to change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt:</span></span>

```csharp
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 2000, 5000]) // defaults to [0, 2000, 10000, 30000]
    .build();
```

### <a name="improved-disconnect-and-reconnect-handling"></a><span data-ttu-id="82fe4-193">Bağlantı kesme geliştirdik ve yeniden işleme</span><span class="sxs-lookup"><span data-stu-id="82fe4-193">Improved disconnect and reconnect handling</span></span>

<span data-ttu-id="82fe4-194">Yeniden bağlanma girişimleri başlatmadan önce `HubConnection` geçer `Reconnecting` durumu ve ateşlenir kendi `onreconnecting` geri çağırma.</span><span class="sxs-lookup"><span data-stu-id="82fe4-194">Before starting any reconnect attempts, the `HubConnection` transitions to the `Reconnecting` state and fires its `onreconnecting` callback.</span></span> <span data-ttu-id="82fe4-195">Bu, bağlantı kesildi kullanıcıları uyarmak, devre dışı kullanıcı Arabirimi öğeleri ve bağlantısı kesilmiş durumu nedeniyle ortaya çıkabilecek karmaşık kullanıcı senaryolarını kolaylaştırmak için bir fırsat sağlar:</span><span class="sxs-lookup"><span data-stu-id="82fe4-195">This provides an opportunity to warn users that the connection was lost, disable UI elements, and mitigate confusing user scenarios that might occur due to the disconnected state:</span></span>

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="82fe4-196">İstemci ilk dört girişimlerinin içinde başarıyla bağlanırsa `HubConnection` geçişleri başa `Connected` durumu ve ateşlenir `onreconnected` geri çağırma.</span><span class="sxs-lookup"><span data-stu-id="82fe4-196">If the client successfully reconnects within its first four attempts, the `HubConnection` transitions back to the `Connected` state and fires `onreconnected` callback.</span></span> <span data-ttu-id="82fe4-197">Bu bağlantı yeniden kurulur kullanıcılara bildirmek için bir fırsat sağlar:</span><span class="sxs-lookup"><span data-stu-id="82fe4-197">This provides an opportunity to inform users that the connection is re-established:</span></span>

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="82fe4-198">İstemci ilk dört girişimlerinin içinde başarıyla yeniden değil `HubConnection` geçer `Disconnected` durumu ve ateşlenir kendi `onclosed` geri çağırma.</span><span class="sxs-lookup"><span data-stu-id="82fe4-198">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` transitions to the `Disconnected` state and fires its `onclosed` callback.</span></span> <span data-ttu-id="82fe4-199">Kullanıcılar bağlantıyı kalıcı olarak kaybolur ve sayfayı yenilemeyi önermek için bir fırsat budur.</span><span class="sxs-lookup"><span data-stu-id="82fe4-199">This is an opportunity to inform users that the connection is permanently lost and to recommend refreshing the page.</span></span>

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

## <a name="additional-resources"></a><span data-ttu-id="82fe4-200">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="82fe4-200">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
