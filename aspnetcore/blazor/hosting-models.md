---
title: Blazor barındırma modellerini ASP.NET Core
author: guardrex
description: Blazor WebAssembly ve Blazor Server barındırma modellerini anlayın.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: blazor/hosting-models
ms.openlocfilehash: 6e225e490e54e44877fa27573ff9b513c8dcd9a3
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964061"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="75300-103">Blazor barındırma modellerini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75300-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="75300-104">[Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="75300-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="75300-105">Blazor, bir [Webassembly](https://webassembly.org/)tabanlı .NET Runtime (*Blazor webassembly*) veya ASP.NET Core (*Blazor Server*) içindeki sunucu tarafında tarayıcıda istemci tarafı çalıştırmak için tasarlanan bir Web çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="75300-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="75300-106">Barındırma modelinden bağımsız olarak, uygulama ve bileşen modelleri *aynıdır*.</span><span class="sxs-lookup"><span data-stu-id="75300-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="75300-107">Bu makalede açıklanan barındırma modelleriyle ilgili bir proje oluşturmak için, bkz <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="75300-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="75300-108">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="75300-108">Blazor WebAssembly</span></span>

<span data-ttu-id="75300-109">Blazor için sorumlu barındırma modeli, WebAssembly üzerinde tarayıcıda istemci tarafında çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="75300-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="75300-110">Blazor uygulaması, bağımlılıkları ve .NET çalışma zamanı tarayıcıya indirilir.</span><span class="sxs-lookup"><span data-stu-id="75300-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="75300-111">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="75300-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="75300-112">UI güncelleştirmeleri ve olay işleme aynı işlem içinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="75300-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="75300-113">Uygulamanın varlıkları, istemcilere statik içerik sunan bir Web sunucusuna veya hizmete statik dosyalar olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="75300-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor WebAssembly: Blazor uygulaması, tarayıcı içindeki bir kullanıcı arabirimi iş parçacığında çalışır.](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="75300-115">İstemci tarafı barındırma modelini kullanarak bir Blazor uygulaması oluşturmak için, **Blazor WebAssembly uygulama** şablonunu ([DotNet New blazorwasm](/dotnet/core/tools/dotnet-new)) kullanın.</span><span class="sxs-lookup"><span data-stu-id="75300-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="75300-116">**Blazor WebAssembly uygulama** şablonunu seçtikten sonra, **ASP.NET Core barındırılan** onay kutusunu ([DotNet New blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) seçerek uygulamayı ASP.NET Core arka ucunu kullanacak şekilde yapılandırma seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="75300-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="75300-117">ASP.NET Core uygulaması, Blazor uygulamasını istemcilere sunar.</span><span class="sxs-lookup"><span data-stu-id="75300-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="75300-118">Blazor WebAssembly uygulaması, Web API çağrılarını veya [SignalR](xref:signalr/introduction)kullanarak ağ üzerinden sunucu ile etkileşime geçebilir.</span><span class="sxs-lookup"><span data-stu-id="75300-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="75300-119">Şablonlar şunları ele alan *blazor. webassembly. js* betiğini içerir:</span><span class="sxs-lookup"><span data-stu-id="75300-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="75300-120">.NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirme.</span><span class="sxs-lookup"><span data-stu-id="75300-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="75300-121">Uygulamayı çalıştırmak için çalışma zamanının başlatılması.</span><span class="sxs-lookup"><span data-stu-id="75300-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="75300-122">Blazor WebAssembly barındırma modeli çeşitli avantajlar sunar:</span><span class="sxs-lookup"><span data-stu-id="75300-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="75300-123">.NET sunucu tarafı bağımlılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="75300-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="75300-124">Uygulama, istemciye indirildikten sonra tamamen çalışır.</span><span class="sxs-lookup"><span data-stu-id="75300-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="75300-125">İstemci kaynakları ve yetenekleri tamamen yararlanılabilir.</span><span class="sxs-lookup"><span data-stu-id="75300-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="75300-126">İş sunucudan istemciye boşaltılır.</span><span class="sxs-lookup"><span data-stu-id="75300-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="75300-127">Uygulamayı barındırmak için bir ASP.NET Core Web sunucusu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="75300-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="75300-128">Sunucusuz dağıtım senaryoları mümkündür (örneğin, bir CDN 'den uygulama sunma).</span><span class="sxs-lookup"><span data-stu-id="75300-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="75300-129">Blazor WebAssembly barındırması için aşağı taraf vardır:</span><span class="sxs-lookup"><span data-stu-id="75300-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="75300-130">Uygulama tarayıcının özelliklerine kısıtlıdır.</span><span class="sxs-lookup"><span data-stu-id="75300-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="75300-131">Uyumlu istemci donanımı ve yazılımı (örneğin, WebAssembly desteği) gereklidir.</span><span class="sxs-lookup"><span data-stu-id="75300-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="75300-132">İndirme boyutu daha büyüktür ve uygulamaların yüklenmesi daha uzun sürer.</span><span class="sxs-lookup"><span data-stu-id="75300-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="75300-133">.NET çalışma zamanı ve araç desteği daha az olgun.</span><span class="sxs-lookup"><span data-stu-id="75300-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="75300-134">Örneğin, [.NET Standard](/dotnet/standard/net-standard) desteğinin ve hata ayıklamada sınırlamalar mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="75300-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="75300-135">Blazor sunucusu</span><span class="sxs-lookup"><span data-stu-id="75300-135">Blazor Server</span></span>

<span data-ttu-id="75300-136">Blazor sunucusu barındırma modeliyle, uygulama sunucuda ASP.NET Core bir uygulama içinden yürütülür.</span><span class="sxs-lookup"><span data-stu-id="75300-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="75300-137">Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrıları bir [SignalR](xref:signalr/introduction) bağlantısı üzerinden işlenir.</span><span class="sxs-lookup"><span data-stu-id="75300-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![Tarayıcı, bir SignalR bağlantısı üzerinden sunucusunda (bir ASP.NET Core uygulamasının içinde barındırılan) uygulamayla etkileşime girer.](hosting-models/_static/blazor-server.png)

<span data-ttu-id="75300-139">Blazor Server barındırma modelini kullanarak bir Blazor uygulaması oluşturmak için, ASP.NET Core **Blazor Server uygulama** şablonunu kullanın ([DotNet yeni blazorserver](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="75300-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="75300-140">ASP.NET Core uygulaması, Blazor sunucu uygulamasını barındırır ve istemcilerin bağlanacağı SignalR uç noktasını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="75300-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="75300-141">ASP.NET Core uygulama, eklenecek uygulamanın `Startup` sınıfına başvurur:</span><span class="sxs-lookup"><span data-stu-id="75300-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="75300-142">Sunucu tarafı hizmetler.</span><span class="sxs-lookup"><span data-stu-id="75300-142">Server-side services.</span></span>
* <span data-ttu-id="75300-143">İstek işleme işlem hattının uygulaması.</span><span class="sxs-lookup"><span data-stu-id="75300-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="75300-144">*Blazor. Server. js* betiği&dagger; , istemci bağlantısını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="75300-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="75300-145">Uygulamanın, uygulama durumunu (örneğin, kayıp ağ bağlantısı durumunda) kalıcı hale getirmek ve geri yüklemek, uygulamanın sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="75300-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="75300-146">Blazor sunucusu barındırma modeli çeşitli avantajlar sunar:</span><span class="sxs-lookup"><span data-stu-id="75300-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="75300-147">İndirme boyutu bir Blazor WebAssembly uygulamasından önemli ölçüde küçüktür ve uygulama çok daha hızlı yüklenir.</span><span class="sxs-lookup"><span data-stu-id="75300-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="75300-148">Uygulama, .NET Core ile uyumlu API 'lerin kullanımı dahil olmak üzere sunucu olanaklarından tam olarak yararlanır.</span><span class="sxs-lookup"><span data-stu-id="75300-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="75300-149">Sunucuda .NET Core, uygulamayı çalıştırmak için kullanılır, bu nedenle hata ayıklama gibi mevcut .NET araçları beklendiği gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="75300-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="75300-150">Ölçülü istemciler desteklenir.</span><span class="sxs-lookup"><span data-stu-id="75300-150">Thin clients are supported.</span></span> <span data-ttu-id="75300-151">Örneğin, Blazor Server Apps, WebAssembly ve kaynak kısıtlı cihazlarda bulunan tarayıcılarla çalışır.</span><span class="sxs-lookup"><span data-stu-id="75300-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="75300-152">Uygulamanın bileşen kodu da dahilC# olmak üzere, uygulamanın .NET/kod tabanı istemcilere sunulmuyor.</span><span class="sxs-lookup"><span data-stu-id="75300-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="75300-153">Blazor sunucusu barındırma için aşağı taraf vardır:</span><span class="sxs-lookup"><span data-stu-id="75300-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="75300-154">Daha yüksek gecikme süresi genellikle vardır.</span><span class="sxs-lookup"><span data-stu-id="75300-154">Higher latency usually exists.</span></span> <span data-ttu-id="75300-155">Her Kullanıcı etkileşimi bir ağ atmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="75300-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="75300-156">Çevrimdışı destek yoktur.</span><span class="sxs-lookup"><span data-stu-id="75300-156">There's no offline support.</span></span> <span data-ttu-id="75300-157">İstemci bağlantısı başarısız olursa, uygulama çalışmayı durduruyor.</span><span class="sxs-lookup"><span data-stu-id="75300-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="75300-158">Ölçeklenebilirlik, çok sayıda kullanıcısı olan uygulamalar için zorlayıcı bir uygulamalardır.</span><span class="sxs-lookup"><span data-stu-id="75300-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="75300-159">Sunucunun birden çok istemci bağlantısını yönetmesi ve istemci durumunu işlemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="75300-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="75300-160">Uygulamayı çalıştırmak için bir ASP.NET Core sunucusu gerekir.</span><span class="sxs-lookup"><span data-stu-id="75300-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="75300-161">Sunucusuz dağıtım senaryoları mümkün değildir (örneğin, bir CDN 'den uygulama sunma).</span><span class="sxs-lookup"><span data-stu-id="75300-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="75300-162">&dagger;*Blazor. Server. js* betiği ASP.NET Core paylaşılan çerçevede eklenmiş bir kaynaktan sunulur.</span><span class="sxs-lookup"><span data-stu-id="75300-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="75300-163">Sunucu tarafından işlenmiş Kullanıcı arabirimine karşılaştırma</span><span class="sxs-lookup"><span data-stu-id="75300-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="75300-164">Blazor Server uygulamalarını anlamanın bir yolu, Razor görünümlerini veya Razor Pages kullanarak ASP.NET Core uygulamalarda Kullanıcı arabirimi oluşturma için geleneksel modellerden nasıl farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="75300-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="75300-165">Her iki model de, HTML içeriğini anlatmak için Razor dilini kullanır, ancak biçimlendirmenin nasıl işlendiği konusunda önemli ölçüde farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="75300-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="75300-166">Bir Razor sayfası veya görünüm işlendiğinde, her Razor kodu satırı metin biçiminde HTML yayar.</span><span class="sxs-lookup"><span data-stu-id="75300-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="75300-167">Oluşturulduktan sonra sunucu, üretilen herhangi bir durum da dahil olmak üzere sayfayı veya görünüm örneğini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="75300-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="75300-168">Sayfa için başka bir istek gerçekleştiğinde, örneğin sunucu doğrulaması başarısız olduğunda ve doğrulama özeti görüntülendiğinde:</span><span class="sxs-lookup"><span data-stu-id="75300-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="75300-169">Sayfanın tamamı HTML metnine yeniden eklenir.</span><span class="sxs-lookup"><span data-stu-id="75300-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="75300-170">Sayfa istemciye gönderilir.</span><span class="sxs-lookup"><span data-stu-id="75300-170">The page is sent to the client.</span></span>

<span data-ttu-id="75300-171">Blazor uygulaması, *bileşen*olarak adlandırılan UI 'ın yeniden kullanılabilir öğelerinden oluşur.</span><span class="sxs-lookup"><span data-stu-id="75300-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="75300-172">Bir bileşen kod C# , biçimlendirme ve diğer bileşenleri içerir.</span><span class="sxs-lookup"><span data-stu-id="75300-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="75300-173">Bir bileşen işlendiğinde, Blazor bir HTML veya XML Belge Nesne Modeli (DOM) gibi dahil edilen bileşenlerin bir grafiğini üretir.</span><span class="sxs-lookup"><span data-stu-id="75300-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="75300-174">Bu grafik, özelliklerde ve alanlarında tutulan bileşen durumunu içerir.</span><span class="sxs-lookup"><span data-stu-id="75300-174">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="75300-175">Blazor, biçimlendirmenin ikili gösterimini üretmek için bileşen grafiğini değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="75300-175">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="75300-176">İkili biçimi şu şekilde olabilir:</span><span class="sxs-lookup"><span data-stu-id="75300-176">The binary format can be:</span></span>

* <span data-ttu-id="75300-177">HTML metnine açıldı (prerendering sırasında).</span><span class="sxs-lookup"><span data-stu-id="75300-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="75300-178">Düzenli işleme sırasında biçimlendirmeyi verimli bir şekilde güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="75300-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="75300-179">Blazor içinde bir kullanıcı arabirimi güncelleştirmesi şu şekilde tetiklenir:</span><span class="sxs-lookup"><span data-stu-id="75300-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="75300-180">Düğme seçme gibi kullanıcı etkileşimi.</span><span class="sxs-lookup"><span data-stu-id="75300-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="75300-181">Zamanlayıcı gibi uygulama Tetikleyicileri.</span><span class="sxs-lookup"><span data-stu-id="75300-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="75300-182">Grafik yeniden tanımlanır ve bir UI *farkı* (fark) hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="75300-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="75300-183">Bu fark, istemcideki Kullanıcı arabirimini güncelleştirmek için gereken en küçük DOM düzenlemelerinin kümesidir.</span><span class="sxs-lookup"><span data-stu-id="75300-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="75300-184">Fark istemciye bir ikili biçimde gönderilir ve tarayıcı tarafından uygulanır.</span><span class="sxs-lookup"><span data-stu-id="75300-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="75300-185">Kullanıcı, istemci üzerinde bundan uzaklaştığında bir bileşen atılmış olur.</span><span class="sxs-lookup"><span data-stu-id="75300-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="75300-186">Bir Kullanıcı bir bileşenle etkileşim kurarken, bileşenin durumu (hizmetler, kaynaklar) sunucunun belleğinde tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="75300-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="75300-187">Birçok bileşenin durumu sunucu tarafından eşzamanlı olarak Korunabileceğinden, bellek tükenmesi sorunu ele alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="75300-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="75300-188">Sunucu belleğinin en iyi şekilde kullanılmasını sağlamak üzere bir Blazor sunucu uygulamasının nasıl yazılacağı hakkında yönergeler için bkz <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="75300-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="75300-189">Uygulanıp</span><span class="sxs-lookup"><span data-stu-id="75300-189">Circuits</span></span>

<span data-ttu-id="75300-190">Bir Blazor sunucu uygulaması [ASP.NET Core SignalR](xref:signalr/introduction)'nin üzerine kurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="75300-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="75300-191">Her istemci, *devre*adlı bir veya daha fazla SignalR bağlantısı üzerinden sunucusuyla iletişim kurar.</span><span class="sxs-lookup"><span data-stu-id="75300-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="75300-192">Bir devre, geçici ağ kesintilerine tolerans sağlayan SignalR bağlantıları üzerinden Blazor.</span><span class="sxs-lookup"><span data-stu-id="75300-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="75300-193">Bir Blazor istemcisi, SignalR bağlantısının kesileceğini gördüğünde, yeni bir SignalR bağlantısı kullanarak sunucuya yeniden bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="75300-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="75300-194">Bir Blazor sunucu uygulamasına bağlı her tarayıcı ekranı (tarayıcı sekmesi veya iframe) bir SignalR bağlantısı kullanır.</span><span class="sxs-lookup"><span data-stu-id="75300-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="75300-195">Bu, tipik sunucu tarafından işlenmiş uygulamalarla karşılaştırıldığında daha önemli bir ayırım ifade etmiştir.</span><span class="sxs-lookup"><span data-stu-id="75300-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="75300-196">Sunucu tarafından işlenen bir uygulamada, aynı uygulamayı birden çok tarayıcı ekranında açmak genellikle sunucuda ek kaynak taleplerine çevirilmez.</span><span class="sxs-lookup"><span data-stu-id="75300-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="75300-197">Bir Blazor sunucu uygulamasında, her tarayıcı ekranı, sunucu tarafından yönetilecek ayrı bir devre ve bileşen durumunun ayrı örneklerini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="75300-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="75300-198">Blazor bir tarayıcı sekmesini kapatmayı veya bir dış URL 'ye gidilerek *düzgün* bir şekilde sonlandırılmasını kabul eder.</span><span class="sxs-lookup"><span data-stu-id="75300-198">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="75300-199">Düzgün sonlandırma durumunda, devre ve ilişkili kaynaklar hemen serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="75300-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="75300-200">Bir istemci, örneğin bir ağ kesintisi nedeniyle düzgün şekilde kesilmeyen bir şekilde kesilebilir.</span><span class="sxs-lookup"><span data-stu-id="75300-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="75300-201">Blazor sunucusu, istemcinin yeniden bağlanmasına izin vermek üzere yapılandırılabilir bir Aralık için bağlantısı kesilen devreleri depolar.</span><span class="sxs-lookup"><span data-stu-id="75300-201">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="75300-202">Daha fazla bilgi için [aynı sunucuya yeniden bağlanma](#reconnection-to-the-same-server) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="75300-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="75300-203">UI gecikmesi</span><span class="sxs-lookup"><span data-stu-id="75300-203">UI Latency</span></span>

<span data-ttu-id="75300-204">UI gecikme süresi, başlatılan bir eylemden Kullanıcı arabiriminin güncelleştirildiği zamana kadar geçen süredir.</span><span class="sxs-lookup"><span data-stu-id="75300-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="75300-205">Bir uygulamanın kullanıcıya yanıt vermesi için kullanıcı ARABIRIMI gecikmesi için daha küçük değerler zorunludur.</span><span class="sxs-lookup"><span data-stu-id="75300-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="75300-206">Bir Blazor sunucu uygulamasında her bir eylem sunucuya gönderilir, işlenir ve bir UI farkı geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="75300-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="75300-207">Sonuç olarak, UI gecikmesi ağ gecikme süresinin toplamı ve eylemi işlerken sunucu gecikmesi sayısıdır.</span><span class="sxs-lookup"><span data-stu-id="75300-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="75300-208">Özel bir kurumsal ağla sınırlı bir iş kolu uygulaması için, ağ gecikmesi nedeniyle kullanıcı gecikmesi algılarını üzerindeki etki, genellikle çok sayıda CEPSİZ olur.</span><span class="sxs-lookup"><span data-stu-id="75300-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="75300-209">Internet üzerinden dağıtılan bir uygulama için, özellikle de kullanıcılar coğrafi olarak coğrafi olarak dağıtılmışsa gecikme süresi kullanıcılara karşı farklılık gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="75300-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="75300-210">Bellek kullanımı ayrıca uygulama gecikme süresine de katkıda bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="75300-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="75300-211">Daha fazla bellek kullanımı, her ikisi de uygulama performansının düşmesine neden olan ve bu nedenle kullanıcı arabirimi gecikmesini arttığı diskte sık görülen çöp toplama veya disk belleği belleği</span><span class="sxs-lookup"><span data-stu-id="75300-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="75300-212">Daha fazla bilgi için bkz. <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="75300-212">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="75300-213">Blazor sunucu uygulamaları, ağ gecikmesini ve bellek kullanımını azaltarak UI gecikmesini en aza indirmek için iyileştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="75300-213">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="75300-214">Ağ gecikmesini ölçmeye yönelik bir yaklaşım için bkz <xref:host-and-deploy/blazor/server#measure-network-latency>.</span><span class="sxs-lookup"><span data-stu-id="75300-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="75300-215">SignalR ve Blazor hakkında daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="75300-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="75300-216">Aynı sunucuya yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="75300-216">Reconnection to the same server</span></span>

<span data-ttu-id="75300-217">Blazor Server uygulamaları, sunucusuna etkin bir SignalR bağlantısı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="75300-217">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="75300-218">Bağlantı kaybolursa, uygulama sunucuya yeniden bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="75300-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="75300-219">İstemcinin durumu hala bellekte olduğu sürece, istemci oturumu durum kaybı olmadan devam eder.</span><span class="sxs-lookup"><span data-stu-id="75300-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

<span data-ttu-id="75300-220">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="75300-220">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="75300-221">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="75300-221">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="75300-222">Kullanıcı arabirimini özelleştirmek için, `components-reconnect-modal` *_host. cshtml* Razor sayfasında `id` olarak bir öğesi tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="75300-222">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="75300-223">İstemci bu öğeyi bağlantı durumuna göre aşağıdaki CSS sınıflarından biriyle güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="75300-223">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>

* <span data-ttu-id="75300-224">`components-reconnect-show`&ndash; Bağlantının kaybolduğunu ve istemcinin yeniden bağlanmaya çalışıyor olduğunu göstermek için Kullanıcı arabirimini görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="75300-224">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="75300-225">`components-reconnect-hide`&ndash; İstemcinin etkin bir bağlantısı vardır ve Kullanıcı arabirimini gizleyin.</span><span class="sxs-lookup"><span data-stu-id="75300-225">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="75300-226">`components-reconnect-failed`&ndash; Yeniden bağlantı kurulamadı.</span><span class="sxs-lookup"><span data-stu-id="75300-226">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="75300-227">Yeniden bağlanmayı yeniden denemek için çağrısı `window.Blazor.reconnect()`yapın.</span><span class="sxs-lookup"><span data-stu-id="75300-227">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="75300-228">Prerendering sonrasında durum bilgisi olan yeniden bağlanma</span><span class="sxs-lookup"><span data-stu-id="75300-228">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="75300-229">Blazor sunucu uygulamaları, sunucu bağlantısı oluşturulmadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="75300-229">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="75300-230">Bu, *_Host. cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="75300-230">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="75300-231">`RenderMode`bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="75300-231">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="75300-232">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="75300-232">Is prerendered into the page.</span></span>
* <span data-ttu-id="75300-233">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.</span><span class="sxs-lookup"><span data-stu-id="75300-233">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="75300-234">Açıklama</span><span class="sxs-lookup"><span data-stu-id="75300-234">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="75300-235">Bileşeni statik HTML olarak işler ve bir Blazor Server uygulaması için işaret içerir.</span><span class="sxs-lookup"><span data-stu-id="75300-235">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="75300-236">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasını önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="75300-236">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="75300-237">Parametreler desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="75300-237">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="75300-238">Bir Blazor sunucu uygulaması için işaretleyici işler.</span><span class="sxs-lookup"><span data-stu-id="75300-238">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="75300-239">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="75300-239">Output from the component isn't included.</span></span> <span data-ttu-id="75300-240">Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasını önyüklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="75300-240">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="75300-241">Parametreler desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="75300-241">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="75300-242">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="75300-242">Renders the component into static HTML.</span></span> <span data-ttu-id="75300-243">Parametreler destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="75300-243">Parameters are supported.</span></span> |

<span data-ttu-id="75300-244">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="75300-244">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="75300-245">İstemci, uygulamayı PreRender 'da kullanılan aynı durum ile sunucuya yeniden bağlanır.</span><span class="sxs-lookup"><span data-stu-id="75300-245">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="75300-246">Uygulamanın durumu hala bellekte ise, SignalR bağlantısı kurulduktan sonra bileşen durumu tekrar verilmez.</span><span class="sxs-lookup"><span data-stu-id="75300-246">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="75300-247">Razor sayfaları ve görünümlerinden durum bilgisi olan etkileşimli bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="75300-247">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="75300-248">Durum bilgisi olan etkileşimli bileşenler Razor sayfasına veya görünümüne eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="75300-248">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="75300-249">Sayfa veya görünüm şunları işler:</span><span class="sxs-lookup"><span data-stu-id="75300-249">When the page or view renders:</span></span>

* <span data-ttu-id="75300-250">Bileşen sayfa veya görünümle birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="75300-250">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="75300-251">Prerendering için kullanılan ilk bileşen durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="75300-251">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="75300-252">SignalR bağlantısı oluşturulduğunda yeni bileşen durumu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="75300-252">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="75300-253">Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:</span><span class="sxs-lookup"><span data-stu-id="75300-253">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="75300-254">Razor sayfaları ve görünümlerinden etkileşimsiz bileşenleri işleme</span><span class="sxs-lookup"><span data-stu-id="75300-254">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="75300-255">Aşağıdaki Razor sayfasında, `MyComponent` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir:</span><span class="sxs-lookup"><span data-stu-id="75300-255">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="75300-256">Statik `MyComponent` olarak işlendiğinden bileşen etkileşimli olamaz.</span><span class="sxs-lookup"><span data-stu-id="75300-256">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="75300-257">Uygulamanın ne zaman prerendering olduğunu Algıla</span><span class="sxs-lookup"><span data-stu-id="75300-257">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="75300-258">Blazor Server uygulamaları için SignalR istemcisini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="75300-258">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="75300-259">Bazen, Blazor Server uygulamaları tarafından kullanılan SignalR istemcisini yapılandırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="75300-259">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="75300-260">Örneğin, bir bağlantı sorununu tanılamak için SignalR istemcisinde günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="75300-260">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="75300-261">*/_Host. cshtml* dosyasında SignalR istemcisini yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="75300-261">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="75300-262">`autostart="false"` *Blazor. Server. js* betiği için `<script>` etikete bir öznitelik ekleyin.</span><span class="sxs-lookup"><span data-stu-id="75300-262">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="75300-263">SignalR oluşturucuyu belirten bir yapılandırma nesnesi çağırın `Blazor.start` ve geçirin.</span><span class="sxs-lookup"><span data-stu-id="75300-263">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="75300-264">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="75300-264">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
