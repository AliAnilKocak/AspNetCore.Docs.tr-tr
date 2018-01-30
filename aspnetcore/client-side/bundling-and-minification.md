---
title: "Paketleme ve ASP.NET Core küçültme"
author: scottaddie
description: "Paketleme ve küçültme teknikleri uygulayarak bir ASP.NET Core web uygulamasında statik kaynakları en iyi duruma getirme hakkında bilgi edinin."
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2018
ms.devlang: csharp
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: client-side/bundling-and-minification
ms.openlocfilehash: 6c233d0957ce9974adbc6112e6194c072aab0b41
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/30/2018
---
# <a name="bundling-and-minification"></a><span data-ttu-id="a742d-103">Paketleme ve küçültme</span><span class="sxs-lookup"><span data-stu-id="a742d-103">Bundling and minification</span></span>

<span data-ttu-id="a742d-104">Tarafından [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a742d-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a742d-105">Bu makalede paketleme ve küçültme, bu özellikler ASP.NET Core web apps ile nasıl kullanılabileceğini de dahil olmak üzere uygulama yararlarını açıklar.</span><span class="sxs-lookup"><span data-stu-id="a742d-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="a742d-106">Paketleme ve küçültme nedir?</span><span class="sxs-lookup"><span data-stu-id="a742d-106">What is bundling and minification?</span></span>

<span data-ttu-id="a742d-107">Paketleme ve küçültme bir web uygulamasını uygulayabilirsiniz iki ayrı performans iyileştirmelerini var.</span><span class="sxs-lookup"><span data-stu-id="a742d-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="a742d-108">Birlikte kullanıldığında, paketleme ve küçültme sunucusu isteklerinin sayısını azaltmak ve istenen statik varlıklar boyutunun azaltılması performansı.</span><span class="sxs-lookup"><span data-stu-id="a742d-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="a742d-109">Paketleme ve küçültme öncelikle ilk sayfa isteği yükleme süresini artırır.</span><span class="sxs-lookup"><span data-stu-id="a742d-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="a742d-110">Bir web sayfası istenen sonra tarayıcı statik varlıklar (JavaScript, CSS ve görüntüleri) önbelleğe alır.</span><span class="sxs-lookup"><span data-stu-id="a742d-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="a742d-111">Sonuç olarak, paketleme ve küçültme aynı sayfa veya sayfaları, aynı varlıklar isteyen aynı sitedeki isterken performansı yok.</span><span class="sxs-lookup"><span data-stu-id="a742d-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="a742d-112">Varsa süresi üstbilgi değil doğru ayarladığınızdan varlıklar ve paketleme ve küçültme değil kullandıysanız, tarayıcının yenilik buluşsal yöntemler varlıklar eski birkaç gün sonra işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="a742d-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="a742d-113">Ayrıca, tarayıcı her varlık için bir doğrulama isteği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="a742d-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="a742d-114">Bu durumda, paketleme ve küçültme ilk sayfa isteği sonra bile performans geliştirmesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="a742d-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="a742d-115">Paketleme</span><span class="sxs-lookup"><span data-stu-id="a742d-115">Bundling</span></span>

<span data-ttu-id="a742d-116">Paketleme birden çok dosya tek bir dosya halinde birleştirir.</span><span class="sxs-lookup"><span data-stu-id="a742d-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="a742d-117">Paketleme, bir web sayfası gibi bir web varlık işlemek için gerekli olan sunucu isteklerinin sayısını azaltır.</span><span class="sxs-lookup"><span data-stu-id="a742d-117">Bundling reduces the number of server requests which are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="a742d-118">Tek tek Paket herhangi bir sayıda özellikle CSS, JavaScript vb. için oluşturabilirsiniz. Daha az sayıda dosya daha az HTTP isteğini tarayıcı sunucuya veya uygulamanızın sağlayan hizmet anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="a742d-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="a742d-119">Bu sonuçları ilk sayfa yükleme performansı geliştirildi.</span><span class="sxs-lookup"><span data-stu-id="a742d-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="a742d-120">Küçültme</span><span class="sxs-lookup"><span data-stu-id="a742d-120">Minification</span></span>

<span data-ttu-id="a742d-121">Küçültme işlevselliği değiştirmeden koddan gereksiz karakterleri kaldırır.</span><span class="sxs-lookup"><span data-stu-id="a742d-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="a742d-122">Sonuç istenilen varlıkların (CSS, görüntüler ve JavaScript dosyaları gibi) önemli boyutu azalma olur.</span><span class="sxs-lookup"><span data-stu-id="a742d-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="a742d-123">Küçültme ortak yan etkileri değişken adları bir karakter kısaltmak ve açıklamalar ve gereksiz boşluk kaldırma içerir.</span><span class="sxs-lookup"><span data-stu-id="a742d-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="a742d-124">Şu JavaScript işlevi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="a742d-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="a742d-125">Küçültme işlevi aşağıdaki azaltır:</span><span class="sxs-lookup"><span data-stu-id="a742d-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="a742d-126">Gereksiz boşluk ve açıklamaları kaldırma ek olarak, aşağıdaki parametre ve değişken adları şu şekilde adlandırıldı:</span><span class="sxs-lookup"><span data-stu-id="a742d-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="a742d-127">Özgün</span><span class="sxs-lookup"><span data-stu-id="a742d-127">Original</span></span> | <span data-ttu-id="a742d-128">Yeniden adlandırıldı</span><span class="sxs-lookup"><span data-stu-id="a742d-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="a742d-129">Paketleme ve küçültme etkisi</span><span class="sxs-lookup"><span data-stu-id="a742d-129">Impact of bundling and minification</span></span>

<span data-ttu-id="a742d-130">Aşağıdaki tabloda tek tek varlıklar yükleme ve paketleme ve küçültme kullanma arasındaki farklar özetlenmektedir:</span><span class="sxs-lookup"><span data-stu-id="a742d-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="a742d-131">Eylem</span><span class="sxs-lookup"><span data-stu-id="a742d-131">Action</span></span> | <span data-ttu-id="a742d-132">B/M ile</span><span class="sxs-lookup"><span data-stu-id="a742d-132">With B/M</span></span> | <span data-ttu-id="a742d-133">B/M</span><span class="sxs-lookup"><span data-stu-id="a742d-133">Without B/M</span></span> | <span data-ttu-id="a742d-134">Değiştir</span><span class="sxs-lookup"><span data-stu-id="a742d-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="a742d-135">Dosya istekleri</span><span class="sxs-lookup"><span data-stu-id="a742d-135">File Requests</span></span>  | <span data-ttu-id="a742d-136">7</span><span class="sxs-lookup"><span data-stu-id="a742d-136">7</span></span>   | <span data-ttu-id="a742d-137">18</span><span class="sxs-lookup"><span data-stu-id="a742d-137">18</span></span>     | <span data-ttu-id="a742d-138">157%</span><span class="sxs-lookup"><span data-stu-id="a742d-138">157%</span></span>
<span data-ttu-id="a742d-139">KB Transferred</span><span class="sxs-lookup"><span data-stu-id="a742d-139">KB Transferred</span></span> | <span data-ttu-id="a742d-140">156</span><span class="sxs-lookup"><span data-stu-id="a742d-140">156</span></span> | <span data-ttu-id="a742d-141">264.68</span><span class="sxs-lookup"><span data-stu-id="a742d-141">264.68</span></span> | <span data-ttu-id="a742d-142">70%</span><span class="sxs-lookup"><span data-stu-id="a742d-142">70%</span></span>
<span data-ttu-id="a742d-143">Yükleme süresi (ms)</span><span class="sxs-lookup"><span data-stu-id="a742d-143">Load Time (ms)</span></span> | <span data-ttu-id="a742d-144">885</span><span class="sxs-lookup"><span data-stu-id="a742d-144">885</span></span> | <span data-ttu-id="a742d-145">2360</span><span class="sxs-lookup"><span data-stu-id="a742d-145">2360</span></span>   | <span data-ttu-id="a742d-146">167%</span><span class="sxs-lookup"><span data-stu-id="a742d-146">167%</span></span>

<span data-ttu-id="a742d-147">Tarayıcılar HTTP istek üstbilgilerinin açısından oldukça ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="a742d-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="a742d-148">Toplam bayt sayısı, paketleme, önemli ölçüde azalma ölçüm gördüğünüz gönderdi.</span><span class="sxs-lookup"><span data-stu-id="a742d-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="a742d-149">Bu örnek yerel olarak çalıştı ancak önemli bir iyileştirme yükleme zamanını gösterir.</span><span class="sxs-lookup"><span data-stu-id="a742d-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="a742d-150">Paketleme ve küçültme varlıklarla kullanarak bir ağ üzerinden aktarıldığında büyük performans artışı alırlar.</span><span class="sxs-lookup"><span data-stu-id="a742d-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="a742d-151">Paketleme ve küçültme stratejisi seçme</span><span class="sxs-lookup"><span data-stu-id="a742d-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="a742d-152">MVC ve Razor sayfalarının proje şablonları paketleme ve küçültme oluşan bir JSON yapılandırma dosyası için bir giden kutusu çözümü sağlar.</span><span class="sxs-lookup"><span data-stu-id="a742d-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="a742d-153">Gibi üçüncü taraf araçları [Gulp](xref:client-side/using-gulp) ve [Grunt](xref:client-side/using-grunt) görev koşucular, biraz daha karmaşık aynı görevleri yerine getirin.</span><span class="sxs-lookup"><span data-stu-id="a742d-153">Third-party tools, such as the [Gulp](xref:client-side/using-gulp) and [Grunt](xref:client-side/using-grunt) task runners, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="a742d-154">Bir üçüncü taraf aracı, geliştirme iş akışınızı paketleme ve küçültme ötesinde işleme gerektiren harika uygun olduğunda&mdash;linting ve görüntü iyileştirme gibi.</span><span class="sxs-lookup"><span data-stu-id="a742d-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="a742d-155">Tasarım zamanı paketleme ve küçültme kullanarak küçültülmüş dosyaları uygulamanın dağıtımdan önce oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a742d-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="a742d-156">Paketleme ve dağıtım öncesinde küçültülmesine azaltılmış sunucu iş yükü avantajı sağlar.</span><span class="sxs-lookup"><span data-stu-id="a742d-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="a742d-157">Ancak, bu tasarım zamanı paketleme bilmek önemlidir ve küçültme yapı karmaşıklığını artırır ve yalnızca statik dosyaları ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="a742d-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="a742d-158">Paketleme ve küçültme yapılandırın</span><span class="sxs-lookup"><span data-stu-id="a742d-158">Configure bundling and minification</span></span>

<span data-ttu-id="a742d-159">MVC ve Razor sayfalarının proje şablonları sağlayan bir *bundleconfig.json* her paket için seçenekleri tanımlayan yapılandırma dosyası.</span><span class="sxs-lookup"><span data-stu-id="a742d-159">The MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file which defines the options for each bundle.</span></span> <span data-ttu-id="a742d-160">Varsayılan olarak, özel JavaScript için tanımlanmış bir tek Paket yapılandırması (*wwwroot/js/site.js*) ve stil sayfası (*wwwroot/css/site.css*) dosyaları:</span><span class="sxs-lookup"><span data-stu-id="a742d-160">By default, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="a742d-161">Yapılandırma seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="a742d-161">Configuration options include:</span></span>

* <span data-ttu-id="a742d-162">`outputFileName`: Çıkış için paket dosyasının adı.</span><span class="sxs-lookup"><span data-stu-id="a742d-162">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="a742d-163">Bir göreli yolu içerebilir *bundleconfig.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="a742d-163">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="a742d-164">**Gerekli**</span><span class="sxs-lookup"><span data-stu-id="a742d-164">**required**</span></span>
* <span data-ttu-id="a742d-165">`inputFiles`: Birlikte paketlemektir dosyaları dizisi.</span><span class="sxs-lookup"><span data-stu-id="a742d-165">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="a742d-166">Bu yapılandırma dosyasının göreli yollardır.</span><span class="sxs-lookup"><span data-stu-id="a742d-166">These are relative paths to the configuration file.</span></span> <span data-ttu-id="a742d-167">**İsteğe bağlı**, \* bir boş çıkış dosyası boş bir değer sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="a742d-167">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="a742d-168">[genelleme](http://www.tldp.org/LDP/abs/html/globbingref.html) desenleri desteklenir.</span><span class="sxs-lookup"><span data-stu-id="a742d-168">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="a742d-169">`minify`: Çıkış türü küçültme seçenekleri.</span><span class="sxs-lookup"><span data-stu-id="a742d-169">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="a742d-170">**İsteğe bağlı**, *varsayılan -`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="a742d-170">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="a742d-171">Çıkış dosya türü yapılandırma seçenekleri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a742d-171">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="a742d-172">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="a742d-172">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="a742d-173">JavaScript küçültücü</span><span class="sxs-lookup"><span data-stu-id="a742d-173">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="a742d-174">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="a742d-174">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="a742d-175">`includeInProject`: Proje dosyası için oluşturulan dosyalar eklenip eklenmeyeceğini belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="a742d-175">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="a742d-176">**İsteğe bağlı**, *varsayılan - yanlış*</span><span class="sxs-lookup"><span data-stu-id="a742d-176">**optional**, *default - false*</span></span>
* <span data-ttu-id="a742d-177">`sourceMap`: İle birlikte gelen dosyası için kaynak eşlemesi oluşturulup oluşturulmayacağını belirten bayrak.</span><span class="sxs-lookup"><span data-stu-id="a742d-177">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="a742d-178">**İsteğe bağlı**, *varsayılan - yanlış*</span><span class="sxs-lookup"><span data-stu-id="a742d-178">**optional**, *default - false*</span></span>
* <span data-ttu-id="a742d-179">`sourceMapRootPath`: Oluşturulan kaynak eşleme dosyasını depolayan kök yolu.</span><span class="sxs-lookup"><span data-stu-id="a742d-179">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="a742d-180">Derleme zamanı yürütülmesi paketleme ve küçültme</span><span class="sxs-lookup"><span data-stu-id="a742d-180">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="a742d-181">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet paketi, paketleme, yürütme ve küçültme derleme zamanında sağlar.</span><span class="sxs-lookup"><span data-stu-id="a742d-181">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="a742d-182">Paket yerleştirir [MSBuild hedefleri](/visualstudio/msbuild/msbuild-targets) yapı ve temiz saatte çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a742d-182">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="a742d-183">*Bundleconfig.json* tanımlanan yapılandırmasını temel alarak çıktı dosyaları üretmek için derleme süreci tarafından dosya analiz.</span><span class="sxs-lookup"><span data-stu-id="a742d-183">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="a742d-184">Microsoft destek sağlayan GitHub topluluk odaklı bir projede BuildBundlerMinifier ait.</span><span class="sxs-lookup"><span data-stu-id="a742d-184">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="a742d-185">Sorunları Dosyalanan [burada](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="a742d-185">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a742d-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a742d-186">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a742d-187">Ekleme *BuildBundlerMinifier* projenize paket.</span><span class="sxs-lookup"><span data-stu-id="a742d-187">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="a742d-188">Projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a742d-188">Build the project.</span></span> <span data-ttu-id="a742d-189">Çıktı penceresinde görünür:</span><span class="sxs-lookup"><span data-stu-id="a742d-189">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="a742d-190">Projeyi temizleyin.</span><span class="sxs-lookup"><span data-stu-id="a742d-190">Clean the project.</span></span> <span data-ttu-id="a742d-191">Çıktı penceresinde görünür:</span><span class="sxs-lookup"><span data-stu-id="a742d-191">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a742d-192">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a742d-192">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="a742d-193">Ekleme *BuildBundlerMinifier* projenize paketi:</span><span class="sxs-lookup"><span data-stu-id="a742d-193">Add the *BuildBundlerMinifier* package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="a742d-194">ASP.NET kullanıyorsanız 1.x çekirdek, yeni eklenen paket geri yükleme:</span><span class="sxs-lookup"><span data-stu-id="a742d-194">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```console
dotnet restore
```

<span data-ttu-id="a742d-195">Projeyi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="a742d-195">Build the project:</span></span>

```console
dotnet build
```

<span data-ttu-id="a742d-196">Aşağıda görünür:</span><span class="sxs-lookup"><span data-stu-id="a742d-196">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="a742d-197">Projeyi temizleyin:</span><span class="sxs-lookup"><span data-stu-id="a742d-197">Clean the project:</span></span>

```console
dotnet clean
```

<span data-ttu-id="a742d-198">Şu çıktı görünür:</span><span class="sxs-lookup"><span data-stu-id="a742d-198">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="a742d-199">Paketleme ve küçültme geçici yürütülmesi</span><span class="sxs-lookup"><span data-stu-id="a742d-199">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="a742d-200">Proje derleme olmadan bir geçici temelinde paketleme ve küçültme görevleri çalıştırmak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="a742d-200">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="a742d-201">Ekleme [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet paketini projenize:</span><span class="sxs-lookup"><span data-stu-id="a742d-201">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="a742d-202">Microsoft destek sağlayan GitHub topluluk odaklı bir projede BundlerMinifier.Core ait.</span><span class="sxs-lookup"><span data-stu-id="a742d-202">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="a742d-203">Sorunları Dosyalanan [burada](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="a742d-203">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="a742d-204">Bu paket dahil etmek için .NET Core CLI genişletir *dotnet paket* aracı.</span><span class="sxs-lookup"><span data-stu-id="a742d-204">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="a742d-205">Aşağıdaki komut, Paket Yöneticisi Konsolu (PMC) penceresinde veya bir komut kabuğu çalıştırılabilir:</span><span class="sxs-lookup"><span data-stu-id="a742d-205">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```console
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="a742d-206">NuGet Paket Yöneticisi \*.csproj dosyaya bağımlılıkları ekler `<PackageReference />` düğümleri.</span><span class="sxs-lookup"><span data-stu-id="a742d-206">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="a742d-207">`dotnet bundle` Komutu ile .NET Core CLI kayıtlı yalnızca bir `<DotNetCliToolReference />` düğümü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a742d-207">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="a742d-208">\*.Csproj dosyasını uygun şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="a742d-208">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="a742d-209">İş akışı için dosyaları Ekle</span><span class="sxs-lookup"><span data-stu-id="a742d-209">Add files to workflow</span></span>

<span data-ttu-id="a742d-210">Bir örnekte göz önünde bulundurun ek bir *custom.css* dosya, aşağıdaki benzeyen eklenir:</span><span class="sxs-lookup"><span data-stu-id="a742d-210">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="a742d-211">Küçültülecek *custom.css* ve onunla paketini *site.css* içine bir *site.min.css* dosya, göreli yol *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="a742d-211">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="a742d-212">Alternatif olarak, aşağıdaki genelleme düzeni kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="a742d-212">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css)"]
> ```
>
> <span data-ttu-id="a742d-213">Bu genelleme deseni tüm CSS dosyaları eşler ve küçültülmüş dosya deseni dışlar.</span><span class="sxs-lookup"><span data-stu-id="a742d-213">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="a742d-214">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a742d-214">Build the application.</span></span> <span data-ttu-id="a742d-215">Açık *site.min.css* ve içeriğini fark *custom.css* dosyasının sonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="a742d-215">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="a742d-216">Ortam tabanlı paketleme ve küçültme</span><span class="sxs-lookup"><span data-stu-id="a742d-216">Environment-based bundling and minification</span></span>

<span data-ttu-id="a742d-217">En iyi uygulama, uygulamanızın ile birlikte gelen ve küçültülmüş dosyaları bir üretim ortamında kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="a742d-217">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="a742d-218">Geliştirme sırasında daha kolay uygulama hata ayıklama için özgün dosyaları olun.</span><span class="sxs-lookup"><span data-stu-id="a742d-218">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="a742d-219">Sayfalarınızda kullanarak eklemek için hangi dosyaların belirtin [ortam etiketi yardımcı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) görünümlerinizi de.</span><span class="sxs-lookup"><span data-stu-id="a742d-219">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="a742d-220">Ortam etiketi yardımcı yalnızca özel çalıştırırken içeriğini işleyen [ortamları](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="a742d-220">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a742d-221">Aşağıdaki `environment` etiketi çalıştırırken işlenmemiş CSS dosyaları işler `Development` ortamı:</span><span class="sxs-lookup"><span data-stu-id="a742d-221">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a742d-222">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a742d-222">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a742d-223">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a742d-223">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

---

<span data-ttu-id="a742d-224">Aşağıdaki `environment` etiketi bir ortamda dışında çalıştırırken ile birlikte gelen ve küçültülmüş CSS dosyaları işler `Development`.</span><span class="sxs-lookup"><span data-stu-id="a742d-224">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="a742d-225">Örneğin, çalışan `Production` veya `Staging` tetikler bu stil sayfaları oluşturma:</span><span class="sxs-lookup"><span data-stu-id="a742d-225">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a742d-226">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a742d-226">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a742d-227">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a742d-227">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

---

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="a742d-228">Gulp gelen bundleconfig.JSON kullanma</span><span class="sxs-lookup"><span data-stu-id="a742d-228">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="a742d-229">Bir uygulamanın paketleme ve küçültme iş akışı ek işlem gerektiren durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="a742d-229">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="a742d-230">Görüntüyü iyileştirme, önbellek busting ve CDN varlık işleme örnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="a742d-230">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="a742d-231">Bu gereksinimleri karşılamak için Gulp kullanmak için paketleme ve küçültme iş akışı dönüştürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a742d-231">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="a742d-232">Paketleyici & küçültücü uzantısı kullanma</span><span class="sxs-lookup"><span data-stu-id="a742d-232">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="a742d-233">Visual Studio [Paketleyici & küçültücü](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) uzantısını Gulp dönüştürme işler.</span><span class="sxs-lookup"><span data-stu-id="a742d-233">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="a742d-234">Microsoft destek sağlayan GitHub topluluk odaklı bir projede Paketleyici & küçültücü uzantısı ait.</span><span class="sxs-lookup"><span data-stu-id="a742d-234">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="a742d-235">Sorunları Dosyalanan [burada](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="a742d-235">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="a742d-236">Sağ *bundleconfig.json* dosya Çözüm Gezgini'nde ve seçin **Paketleyici & küçültücü** > **dönüştürmek için Gulp...** :</span><span class="sxs-lookup"><span data-stu-id="a742d-236">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Bağlam menüsü öğesini dönüştürmek için Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="a742d-238">*Gulpfile.js* ve *package.json* dosyaları projeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="a742d-238">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="a742d-239">Destekleyici [npm](https://www.npmjs.com/) içinde listelenen paketler *package.json* dosyanın `devDependencies` bölüm yüklenir.</span><span class="sxs-lookup"><span data-stu-id="a742d-239">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="a742d-240">Genel bir bağımlılık olarak Gulp CLI yükleme PMC penceresinde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a742d-240">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="a742d-241">*Gulpfile.js* dosya okuma *bundleconfig.json* giriş, çıkış ve ayarları dosyası.</span><span class="sxs-lookup"><span data-stu-id="a742d-241">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="a742d-242">El ile Dönüştür</span><span class="sxs-lookup"><span data-stu-id="a742d-242">Convert manually</span></span>

<span data-ttu-id="a742d-243">Visual Studio ve/veya Paketleyici & küçültücü uzantısı yoksa, el ile dönüştürün.</span><span class="sxs-lookup"><span data-stu-id="a742d-243">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="a742d-244">Ekleme bir *package.json* dosyasıyla aşağıdaki `devDependencies`, proje kök:</span><span class="sxs-lookup"><span data-stu-id="a742d-244">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="a742d-245">Aynı düzeyde aşağıdaki komutu çalıştırarak bağımlılıkları yükler *package.json*:</span><span class="sxs-lookup"><span data-stu-id="a742d-245">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="a742d-246">Gulp CLI genel bağımlılık olarak yükleyin:</span><span class="sxs-lookup"><span data-stu-id="a742d-246">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="a742d-247">Kopya *gulpfile.js* proje kök dosya altında:</span><span class="sxs-lookup"><span data-stu-id="a742d-247">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="a742d-248">Gulp görevleri Çalıştır</span><span class="sxs-lookup"><span data-stu-id="a742d-248">Run Gulp tasks</span></span>

<span data-ttu-id="a742d-249">Visual Studio'da projeyi derlemeler önce Gulp küçültme görev tetiklemek için aşağıdaki ekleyin [MSBuild hedef](/visualstudio/msbuild/msbuild-targets) \*.csproj dosyasına:</span><span class="sxs-lookup"><span data-stu-id="a742d-249">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="a742d-250">Bu örnekte, içinde herhangi bir görevi tanımlanan `MyPreCompileTarget` önceden tanımlanmış önce çalıştırmak hedef `Build` hedef.</span><span class="sxs-lookup"><span data-stu-id="a742d-250">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="a742d-251">Visual Studio çıktı penceresinde aşağıdakine benzer bir çıktı görünür:</span><span class="sxs-lookup"><span data-stu-id="a742d-251">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="a742d-252">Alternatif olarak, Visual Studio'nun görev Çalıştırıcı Gezgini belirli Visual Studio olayları Gulp görevleri bağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a742d-252">Alternatively, Visual Studio's Task Runner Explorer may be used to bind Gulp tasks to specific Visual Studio events.</span></span> <span data-ttu-id="a742d-253">Bkz: [varsayılan görevleri çalıştırma](xref:client-side/using-gulp#running-default-tasks) , bunu ilgili yönergeler için.</span><span class="sxs-lookup"><span data-stu-id="a742d-253">See [Running default tasks](xref:client-side/using-gulp#running-default-tasks) for instructions on doing that.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a742d-254">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a742d-254">Additional resources</span></span>

* [<span data-ttu-id="a742d-255">Gulp kullanma</span><span class="sxs-lookup"><span data-stu-id="a742d-255">Using Gulp</span></span>](xref:client-side/using-gulp)
* [<span data-ttu-id="a742d-256">Grunt kullanma</span><span class="sxs-lookup"><span data-stu-id="a742d-256">Using Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="a742d-257">Birden çok ortamı ile çalışma</span><span class="sxs-lookup"><span data-stu-id="a742d-257">Working with Multiple Environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="a742d-258">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="a742d-258">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
