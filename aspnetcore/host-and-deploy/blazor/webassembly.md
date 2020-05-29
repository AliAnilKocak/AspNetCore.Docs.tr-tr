---
<span data-ttu-id="ba5b5-101">Başlık: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-103">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-104">'Identity'</span></span>
- <span data-ttu-id="ba5b5-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-106">'Razor'</span></span>
- <span data-ttu-id="ba5b5-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="ba5b5-108">ASP.NET Core webassembly 'ı barındırma ve dağıtma Blazor</span><span class="sxs-lookup"><span data-stu-id="ba5b5-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="ba5b5-109">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından</span><span class="sxs-lookup"><span data-stu-id="ba5b5-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="ba5b5-110">[ Blazor Webassembly barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="ba5b5-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="ba5b5-111">BlazorUygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="ba5b5-112">Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="ba5b5-113">Aşağıdaki dağıtım stratejileri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="ba5b5-114">BlazorUygulama, bir ASP.NET Core uygulaması tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="ba5b5-115">Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="ba5b5-116">BlazorUygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .NET uygulamayı sunmak için kullanılmaz Blazor .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="ba5b5-117">Bu strateji [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır. Bu, bir Blazor WEBASSEMBLY uygulamasını bir IIS alt uygulaması olarak barındırma hakkında bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="ba5b5-118">Brotli ön sıkıştırma</span><span class="sxs-lookup"><span data-stu-id="ba5b5-118">Brotli precompression</span></span>

<span data-ttu-id="ba5b5-119">BlazorWebassembly uygulaması yayımlandığında, çıkış en yüksek düzeydeki [Brotli sıkıştırma algoritması](https://tools.ietf.org/html/rfc7932) kullanılarak, uygulama boyutunu azaltmak ve çalışma zamanı sıkıştırması gereksinimini kaldırmak için önceden sıkıştırılır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="ba5b5-120">IIS *Web. config* sıkıştırma yapılandırması için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="ba5b5-121">Doğru yönlendirme için URL 'Leri yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="ba5b5-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="ba5b5-122">Webassembly uygulamasındaki sayfa bileşenlerine yönelik yönlendirme istekleri Blazor Blazor , sunucuda, barındırılan bir uygulamada yönlendirme istekleri kadar basittir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="ba5b5-123">Blazorİki bileşeni olan bir webassembly uygulaması düşünün:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="ba5b5-124">*Main. Razor*: uygulamanın köküne yükler ve `About` bileşene () bir bağlantı içerir `href="About"` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-124">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="ba5b5-125">*. Razor*: `About` bileşeni hakkında.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-125">*About.razor*: `About` component.</span></span>

<span data-ttu-id="ba5b5-126">Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="ba5b5-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="ba5b5-127">Tarayıcı bir istek yapar.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-127">The browser makes a request.</span></span>
1. <span data-ttu-id="ba5b5-128">Varsayılan sayfa döndürülür, bu genellikle *index. html*'dir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="ba5b5-129">uygulamanın *index. html* önyükleme.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-129">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="ba5b5-130">uygulamasının yönlendirici yüklenir ve Razor `Main` bileşen işlenir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-130">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="ba5b5-131">Ana sayfada, `About` Blazor yönlendirici tarayıcıyı Internet 'te için bir istek yapmayı durdurduğundan `www.contoso.com` `About` ve Işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="ba5b5-132">\* Blazor Webassembly uygulamasındaki\* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="ba5b5-133">Yönlendirici istekleri dahili olarak işler.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-133">The router handles the requests internally.</span></span>

<span data-ttu-id="ba5b5-134">Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="ba5b5-135">Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="ba5b5-136">Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri, fiziksel olarak sunucu üzerinde olmayan kaynakların tüm isteklerini *Dizin. html* sayfasına yeniden yazmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="ba5b5-137">*İndex. html* döndürüldüğünde, uygulamanın Blazor yönlendiricisi doğru kaynakla sürer ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="ba5b5-138">Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayınlanan *Web. config* dosyasıyla birlikte kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="ba5b5-139">Daha fazla bilgi için [IIS](#iis) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="ba5b5-140">ASP.NET Core ile barındırılan dağıtım</span><span class="sxs-lookup"><span data-stu-id="ba5b5-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="ba5b5-141">*Barındırılan dağıtım* , Blazor webassembly uygulamasını Web sunucusunda çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılara sunar.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="ba5b5-142">İstemci Blazor weelsembly uygulaması, sunucu uygulamasının */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe, sunucu uygulamasının diğer statik Web varlıklarıyla birlikte yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="ba5b5-143">İki uygulama birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-143">The two apps are deployed together.</span></span> <span data-ttu-id="ba5b5-144">ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="ba5b5-145">Barındırılan bir dağıtım için Visual Studio, \*\* Blazor \*\* `blazorwasm` **barındırılan** seçenek seçili olarak ( [DotNet yeni](/dotnet/core/tools/dotnet-new) komut kullanılırken şablon), webassembly uygulama projesi şablonunu (komut kullanılırken) içerir `-ho|--hosted` `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="ba5b5-146">Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="ba5b5-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="ba5b5-147">Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..</span><span class="sxs-lookup"><span data-stu-id="ba5b5-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="ba5b5-148">Tek başına dağıtım</span><span class="sxs-lookup"><span data-stu-id="ba5b5-148">Standalone deployment</span></span>

<span data-ttu-id="ba5b5-149">*Tek başına dağıtım* , Blazor webassembly uygulamasına doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="ba5b5-150">Herhangi bir statik dosya sunucusu, Blazor uygulamayı sunabilir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="ba5b5-151">Tek başına dağıtım varlıkları */BIN/Release/{Target Framework}/Publish/Wwwroot* klasöründe yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="ba5b5-152">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ba5b5-152">Azure App Service</span></span>

Blazor<span data-ttu-id="ba5b5-153">WebAssembly uygulamaları, uygulamayı [IIS](#iis)üzerinde barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-153"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="ba5b5-154">BlazorLinux için Azure App Service tek başına webassembly uygulaması dağıtmak Şu anda desteklenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="ba5b5-155">Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="ba5b5-156">Bu senaryoyu etkinleştirmek için iş devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="ba5b5-157">IIS</span><span class="sxs-lookup"><span data-stu-id="ba5b5-157">IIS</span></span>

<span data-ttu-id="ba5b5-158">IIS, uygulamalar için özellikli bir statik dosya sunucusudur Blazor .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="ba5b5-159">IIS 'yi barındıracak şekilde yapılandırmak için Blazor bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="ba5b5-160">Yayımlanan varlıklar */BIN/Release/{Target Framework}/Publish* klasöründe oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="ba5b5-161">Web sunucusunda veya barındırma hizmetinde *Yayımlama* klasörünün içeriğini barındırın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="ba5b5-162">Web. config</span><span class="sxs-lookup"><span data-stu-id="ba5b5-162">web.config</span></span>

<span data-ttu-id="ba5b5-163">Bir Blazor Proje yayımlandığında, AŞAĞıDAKI IIS yapılandırmasıyla bir *Web. config* dosyası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="ba5b5-164">MIME türleri aşağıdaki dosya uzantıları için ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="ba5b5-165">*. dll*:`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="ba5b5-165">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="ba5b5-166">*. JSON*:`application/json`</span><span class="sxs-lookup"><span data-stu-id="ba5b5-166">*.json*: `application/json`</span></span>
  * <span data-ttu-id="ba5b5-167">*...*:`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="ba5b5-167">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="ba5b5-168">*. WOFF*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ba5b5-168">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="ba5b5-169">*. woff2*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="ba5b5-169">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="ba5b5-170">Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="ba5b5-171">URL yeniden yazma modülü kuralları oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="ba5b5-172">Uygulamanın statik varlıklarının bulunduğu alt dizini (*Wwwroot/{Path istenen}*) sunar.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="ba5b5-173">Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe (*Wwwroot/index.html*) uygulamanın varsayılan belgesine yeniden YÖNLENDIRILMESI için Spa geri dönüş yönlendirmesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="ba5b5-174">Özel Web. config kullanma</span><span class="sxs-lookup"><span data-stu-id="ba5b5-174">Use a custom web.config</span></span>

<span data-ttu-id="ba5b5-175">Özel *Web. config* dosyasını kullanmak için, özel *Web. config* dosyasını proje klasörünün köküne yerleştirin ve projeyi yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="ba5b5-176">URL yeniden yazma modülünü yükler</span><span class="sxs-lookup"><span data-stu-id="ba5b5-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="ba5b5-177">[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="ba5b5-178">Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="ba5b5-179">Modül IIS Web sitesinden indirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="ba5b5-180">Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="ba5b5-181">Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="ba5b5-182">Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="ba5b5-183">Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="ba5b5-184">Yükleyiciyi sunucuya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-184">Copy the installer to the server.</span></span> <span data-ttu-id="ba5b5-185">Yükleyiciyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-185">Run the installer.</span></span> <span data-ttu-id="ba5b5-186">, **Install** düğmesini seçin ve lisans koşullarını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="ba5b5-187">Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="ba5b5-188">Web sitesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ba5b5-188">Configure the website</span></span>

<span data-ttu-id="ba5b5-189">Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="ba5b5-190">Klasör şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-190">The folder contains:</span></span>

* <span data-ttu-id="ba5b5-191">Gerekli yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS 'nin Web sitesini yapılandırmak için kullandığı *Web. config* dosyası.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="ba5b5-192">Uygulamanın statik varlık klasörü.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="ba5b5-193">IIS alt uygulaması olarak barındırma</span><span class="sxs-lookup"><span data-stu-id="ba5b5-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="ba5b5-194">Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="ba5b5-195">Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="ba5b5-196">BlazorDosyaya bir bölüm ekleyerek uygulamanın yayınlanan *Web. config* dosyasındaki işleyiciyi kaldırın `<handlers>` :</span><span class="sxs-lookup"><span data-stu-id="ba5b5-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="ba5b5-197">Şu `<system.webServer>` şekilde ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın `<location>` `inheritInChildApplications` `false` :</span><span class="sxs-lookup"><span data-stu-id="ba5b5-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="ba5b5-198">İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:host-and-deploy/blazor/index#app-base-path)ek olarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="ba5b5-199">Uygulamanın *index. html* dosyasındaki uygulama temel yolunu, IIS 'de alt uygulamayı YAPıLANDıRıRKEN kullanılan IIS diğer adına ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="ba5b5-200">Brotli ve gzip sıkıştırması</span><span class="sxs-lookup"><span data-stu-id="ba5b5-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="ba5b5-201">IIS, *Web. config* aracılığıyla Brotli veya gzip sıkıştırılan varlıkları sunacak şekilde yapılandırılabilir Blazor .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="ba5b5-202">Örnek bir yapılandırma için bkz. [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="ba5b5-203">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="ba5b5-203">Troubleshooting</span></span>

<span data-ttu-id="ba5b5-204">*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="ba5b5-205">Modül yüklü olmadığında, *Web. config* dosyası IIS tarafından ayrıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="ba5b5-206">Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet olarak yüklemesini engeller Blazor .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="ba5b5-207">IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için bkz <xref:test/troubleshoot-azure-iis> ..</span><span class="sxs-lookup"><span data-stu-id="ba5b5-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="ba5b5-208">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="ba5b5-208">Azure Storage</span></span>

<span data-ttu-id="ba5b5-209">[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulama barındırmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="ba5b5-210">Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="ba5b5-211">Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="ba5b5-212">**Dizin belgesi adını** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="ba5b5-213">**Hata belge yolunu** olarak ayarlayın `index.html` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-213">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="ba5b5-214">bileşenler ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-214"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="ba5b5-215">Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında Blazor , blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna**yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="ba5b5-216">*İndex. html* blobu döndürülür ve Blazor yönlendirici yolu yükler ve işler.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="ba5b5-217">Dosyalar ' üst bilgilerinde uygunsuz MIME türleri nedeniyle çalışma zamanında dosya yüklenmemişse `Content-Type` , aşağıdaki eylemlerden birini gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-217">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="ba5b5-218">Araçlar, dosyalar dağıtıldığında doğru MIME türlerini (üstbilgiler) ayarlamak üzere yapılandırın `Content-Type` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-218">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="ba5b5-219">`Content-Type`Uygulama dağıtıldıktan sonra dosyalar IÇIN MIME türlerini (üstbilgiler) değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-219">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="ba5b5-220">Her dosya için Depolama Gezgini (Azure portal):</span><span class="sxs-lookup"><span data-stu-id="ba5b5-220">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="ba5b5-221">Dosyaya sağ tıklayın ve **Özellikler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-221">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="ba5b5-222">**ContentType** ' ı ayarlayın ve **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-222">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="ba5b5-223">Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-223">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="ba5b5-224">Nginx</span><span class="sxs-lookup"><span data-stu-id="ba5b5-224">Nginx</span></span>

<span data-ttu-id="ba5b5-225">Aşağıdaki *NGINX. conf* dosyası, NGINX 'in, disk üzerinde karşılık gelen bir dosyayı bulamadığı her seferinde *index. html* dosyasını göndermek üzere nasıl yapılandırılacağını gösterecek şekilde basitleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-225">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="ba5b5-226">Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-226">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="ba5b5-227">Docker 'da NGINX</span><span class="sxs-lookup"><span data-stu-id="ba5b5-227">Nginx in Docker</span></span>

<span data-ttu-id="ba5b5-228">BlazorNGINX kullanarak Docker 'da barındırmak Için Dockerfile 'ı alp tabanlı NGINX görüntüsünü kullanacak şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-228">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="ba5b5-229">Dockerfile dosyasını, *NGINX. config* dosyasını kapsayıcıya kopyalamak için güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-229">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="ba5b5-230">Aşağıdaki örnekte gösterildiği gibi Dockerfile dosyasına bir satır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-230">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="ba5b5-231">Apache</span><span class="sxs-lookup"><span data-stu-id="ba5b5-231">Apache</span></span>

<span data-ttu-id="ba5b5-232">BlazorCenelsembly uygulamasını CentOS 7 veya sonraki bir sürüme dağıtmak için:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-232">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="ba5b5-233">Apache yapılandırma dosyasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-233">Create the Apache configuration file.</span></span> <span data-ttu-id="ba5b5-234">Aşağıdaki örnek basitleştirilmiş bir yapılandırma dosyasıdır (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="ba5b5-234">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="ba5b5-235">Apache yapılandırma dosyasını `/etc/httpd/conf.d/` , CentOS 7 ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-235">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="ba5b5-236">Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin ( `DocumentRoot` yapılandırma dosyasında belirtilen konum).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-236">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="ba5b5-237">Apache hizmetini yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-237">Restart the Apache service.</span></span>

<span data-ttu-id="ba5b5-238">Daha fazla bilgi için bkz. [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) ve [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-238">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="ba5b5-239">GitHub sayfaları</span><span class="sxs-lookup"><span data-stu-id="ba5b5-239">GitHub Pages</span></span>

<span data-ttu-id="ba5b5-240">URL yeniden işlemesini işlemek için, isteği *index. html* sayfasına yönlendirmeyi işleyen bir betiği olan bir *404. html* dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-240">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="ba5b5-241">Topluluk tarafından sunulan örnek bir uygulama için bkz. [GitHub sayfaları Için tek sayfalı uygulamalar](https://spa-github-pages.rafrex.com/) ([GitHub üzerinde rafrex/Spa-GitHub-Pages](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-241">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="ba5b5-242">Topluluk yaklaşımını kullanan bir örnek, GitHub ([canlı site](https://blazor-demo.github.io/)) [üzerinde blazor-demo/blazor-demo. GitHub. IO](https://github.com/blazor-demo/blazor-demo.github.io) adresinde görülebilir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-242">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="ba5b5-243">Bir kuruluş sitesi yerine bir proje sitesi kullanırken `<base>` etiketi *index. html*dosyasına ekleyin veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-243">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="ba5b5-244">`href`Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin,) ayarlayın `my-repository/` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-244">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ba5b5-245">Ana bilgisayar yapılandırma değerleri</span><span class="sxs-lookup"><span data-stu-id="ba5b5-245">Host configuration values</span></span>

<span data-ttu-id="ba5b5-246">[ Blazor Webassembly uygulamaları](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında aşağıdaki ana bilgisayar yapılandırma değerlerini komut satırı bağımsız değişkenleri olarak kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-246">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="ba5b5-247">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="ba5b5-247">Content root</span></span>

<span data-ttu-id="ba5b5-248">`--contentroot`Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-248">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="ba5b5-249">Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-249">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="ba5b5-250">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-250">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ba5b5-251">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-251">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="ba5b5-252">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-252">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ba5b5-253">Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile bir komut isteminden çalıştırıldığında kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-253">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="ba5b5-254">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-254">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ba5b5-255">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-255">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="ba5b5-256">Yol tabanı</span><span class="sxs-lookup"><span data-stu-id="ba5b5-256">Path base</span></span>

<span data-ttu-id="ba5b5-257">`--pathbase`Bağımsız değişkeni, kök olmayan göreli BIR URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket, `href` `/` hazırlama ve üretim için dışında bir yola ayarlanır).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-257">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="ba5b5-258">Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-258">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="ba5b5-259">Daha fazla bilgi için bkz. [uygulama temel yolu](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-259">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ba5b5-260">Etiketinde belirtilen yolun aksine `href` `<base>` , `/` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin `--pathbase` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-260">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="ba5b5-261">Etikette uygulama temel yolu `<base>` `<base href="/CoolApp/">` (sondaki eğik çizgi içeriyorsa) olarak sağlanmışsa, komut satırı bağımsız değişken değerini `--pathbase=/CoolApp` (sondaki eğik çizgi yok) olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-261">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="ba5b5-262">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-262">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ba5b5-263">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-263">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="ba5b5-264">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-264">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ba5b5-265">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-265">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="ba5b5-266">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-266">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ba5b5-267">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-267">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="ba5b5-268">URL’ler</span><span class="sxs-lookup"><span data-stu-id="ba5b5-268">URLs</span></span>

<span data-ttu-id="ba5b5-269">`--urls`Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve protokollerle IP adreslerini veya konak adreslerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-269">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="ba5b5-270">Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-270">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="ba5b5-271">Uygulamanın dizininden şunu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-271">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="ba5b5-272">**IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-272">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="ba5b5-273">Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-273">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="ba5b5-274">Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-274">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="ba5b5-275">Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-275">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="ba5b5-276">Bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ba5b5-276">Configure the Linker</span></span>

Blazor<span data-ttu-id="ba5b5-277">çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-277"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="ba5b5-278">Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-278">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="ba5b5-279">Özel önyükleme kaynağı yükleme</span><span class="sxs-lookup"><span data-stu-id="ba5b5-279">Custom boot resource loading</span></span>

<span data-ttu-id="ba5b5-280">Blazor `loadBootResource` Yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için bir webassembly uygulaması işleviyle başlatılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-280">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="ba5b5-281">`loadBootResource`Aşağıdaki senaryolar için kullanın:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-281">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="ba5b5-282">Kullanıcıların bir CDN 'den saat dilimi verileri veya *DotNet. IStream* gibi statik kaynakları yüklemesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-282">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="ba5b5-283">Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-283">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="ba5b5-284">Her isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları `fetch` .</span><span class="sxs-lookup"><span data-stu-id="ba5b5-284">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="ba5b5-285">`loadBootResource`Parametreler aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-285">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="ba5b5-286">Parametre</span><span class="sxs-lookup"><span data-stu-id="ba5b5-286">Parameter</span></span>    | <span data-ttu-id="ba5b5-287">Açıklama</span><span class="sxs-lookup"><span data-stu-id="ba5b5-287">Description</span></span> |
| ---
<span data-ttu-id="ba5b5-288">Başlık: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-288">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-290">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-291">'Identity'</span></span>
- <span data-ttu-id="ba5b5-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-293">'Razor'</span></span>
- <span data-ttu-id="ba5b5-294">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba5b5-295">Başlık: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-295">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-297">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-298">'Identity'</span></span>
- <span data-ttu-id="ba5b5-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-300">'Razor'</span></span>
- <span data-ttu-id="ba5b5-301">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba5b5-302">Başlık: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-302">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-304">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-305">'Identity'</span></span>
- <span data-ttu-id="ba5b5-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-307">'Razor'</span></span>
- <span data-ttu-id="ba5b5-308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba5b5-309">Başlık: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-309">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-310">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-311">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-312">'Identity'</span></span>
- <span data-ttu-id="ba5b5-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-314">'Razor'</span></span>
- <span data-ttu-id="ba5b5-315">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-315">'SignalR' uid:</span></span> 

<span data-ttu-id="ba5b5-316">------ | ---title: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-316">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-317">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-318">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-319">'Identity'</span></span>
- <span data-ttu-id="ba5b5-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-321">'Razor'</span></span>
- <span data-ttu-id="ba5b5-322">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba5b5-323">Başlık: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-323">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-324">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-325">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-326">'Identity'</span></span>
- <span data-ttu-id="ba5b5-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-328">'Razor'</span></span>
- <span data-ttu-id="ba5b5-329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ba5b5-330">Başlık: ' ana bilgisayar ve dağıtım ASP.NET Core Blazor webassembly ' Yazar: Açıklama: ' Blazor ASP.NET Core, Içerik teslim AĞLARı (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="ba5b5-330">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="ba5b5-331">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-331">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ba5b5-332">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-332">'Blazor'</span></span>
- <span data-ttu-id="ba5b5-333">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-333">'Identity'</span></span>
- <span data-ttu-id="ba5b5-334">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-334">'Let's Encrypt'</span></span>
- <span data-ttu-id="ba5b5-335">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ba5b5-335">'Razor'</span></span>
- <span data-ttu-id="ba5b5-336">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-336">'SignalR' uid:</span></span> 

<span data-ttu-id="ba5b5-337">------ | | `type`       | Kaynağın türü.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-337">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="ba5b5-338">İzinleri olan türler: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata` | | `name`       | Kaynağın adı.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-338">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="ba5b5-339">| | `defaultUri` | Kaynağın göreli veya mutlak URI 'SI.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-339">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="ba5b5-340">| | `integrity`  | Yanıtta beklenen içeriği temsil eden bütünlük dizesi.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-340">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="ba5b5-341">`loadBootResource`yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-341">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="ba5b5-342">URI dizesi.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-342">URI string.</span></span> <span data-ttu-id="ba5b5-343">Aşağıdaki örnekte (*Wwwroot/index.html*), aşağıdaki dosyalar ŞURADA bir CDN 'den sunulur `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="ba5b5-343">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="ba5b5-344">*DotNet. \* . JS*</span><span class="sxs-lookup"><span data-stu-id="ba5b5-344">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="ba5b5-345">*DotNet.*</span><span class="sxs-lookup"><span data-stu-id="ba5b5-345">*dotnet.wasm*</span></span>
  * <span data-ttu-id="ba5b5-346">Saat dilimi verileri</span><span class="sxs-lookup"><span data-stu-id="ba5b5-346">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="ba5b5-347">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-347">`Promise<Response>`.</span></span> <span data-ttu-id="ba5b5-348">`integrity`Varsayılan bütünlük denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-348">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="ba5b5-349">Aşağıdaki örnek (*Wwwroot/index.html*) giden isteklere özel bir http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-349">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="ba5b5-350">`null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-350">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="ba5b5-351">Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-351">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="ba5b5-352">CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-352">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="ba5b5-353">Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-353">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="ba5b5-354">İçin belirtilmemiş türler `loadBootResource` , varsayılan yükleme davranışları başına Framework tarafından yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-354">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="ba5b5-355">DLL dosyalarının dosya adı uzantısını değiştirme</span><span class="sxs-lookup"><span data-stu-id="ba5b5-355">Change the filename extension of DLL files</span></span>

<span data-ttu-id="ba5b5-356">Uygulamanın yayımlanmış *. dll* dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa, bu bölümdeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-356">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="ba5b5-357">Uygulamayı yayımladıktan sonra, *. dll* dosyalarını farklı bir dosya uzantısı kullanacak şekilde yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-357">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="ba5b5-358">Uygulamanın yayımlanmış çıktısının *Wwwroot* dizinindeki *. dll* dosyalarını hedefleyin (ÖRNEĞIN, *{Content root}/bin/Release/netstandard2.1/Publish/Wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="ba5b5-358">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="ba5b5-359">Aşağıdaki örneklerde,. *DLL* dosyaları *. bin* dosya uzantısını kullanacak şekilde yeniden adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-359">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="ba5b5-360">Windows'da:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-360">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="ba5b5-361">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-361">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="ba5b5-362">Linux veya macOS 'ta:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-362">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="ba5b5-363">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-363">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="ba5b5-364">*. Bin*' den farklı bir dosya uzantısı kullanmak için, önceki komutlarda *. bin yerine.*</span><span class="sxs-lookup"><span data-stu-id="ba5b5-364">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="ba5b5-365">Sıkıştırılmış *blazor. Boot. JSON. gz* ve *blazor.Boot.JSON.br* dosyalarını ele almak için aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-365">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="ba5b5-366">Sıkıştırılmış *blazor. Boot. JSON. gz* ve *blazor.Boot.JSON.br* dosyalarını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-366">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="ba5b5-367">Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-367">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="ba5b5-368">Güncelleştirilmiş *blazor. Boot. JSON* dosyasını yeniden sıkıştırın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-368">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="ba5b5-369">Yukarıdaki kılavuz, hizmet çalışanı varlıkları kullanımda olduğunda da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-369">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="ba5b5-370">*Wwwroot/Service-Worker-varlıklar. js. br* ve *Wwwroot/Service-Worker-assets. js. gz*'yi kaldırın veya yeniden sıkıştırın.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-370">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="ba5b5-371">Aksi halde, tarayıcıda dosya bütünlüğü denetimleri başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-371">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="ba5b5-372">Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-372">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="ba5b5-373">*ChangeDLLExtensions. ps1:*:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-373">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="ba5b5-374">Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-374">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="ba5b5-375">Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:</span><span class="sxs-lookup"><span data-stu-id="ba5b5-375">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="ba5b5-376">Geri bildirim sağlamak için, [#5477 aspnetcore/sorunlar](https://github.com/dotnet/aspnetcore/issues/5477)' ı ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="ba5b5-376">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
