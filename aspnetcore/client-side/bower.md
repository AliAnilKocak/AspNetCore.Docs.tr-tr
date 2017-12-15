---
title: ASP.NET Core Bower kullanarak
author: rick-anderson
description: "İstemci tarafı paketleri Bower ile yönetme."
keywords: ASP.NET Core, bower
ms.author: riande
manager: wpickett
ms.date: 02/14/2017
ms.topic: article
ms.assetid: df7c43da-280e-4df6-86cb-eecec8f12bfc
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/bower
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0258315e0e24d662086a3171b58112e08b9a40ab
ms.sourcegitcommit: 198fb0488e961048bfa376cf58cb853ef1d1cb91
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/14/2017
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a><span data-ttu-id="8bc32-104">İstemci tarafı paketleri ASP.NET Core Bower ile yönetme</span><span class="sxs-lookup"><span data-stu-id="8bc32-104">Manage client-side packages with Bower in ASP.NET Core</span></span>

<span data-ttu-id="8bc32-105">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel pirinç](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/), ve [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="8bc32-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel Rice](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/), and [Scott Addie](https://scottaddie.com)</span></span> 

> [!IMPORTANT]
> <span data-ttu-id="8bc32-106">Bower korunur, ancak bunlar farklı bir çözüme kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="8bc32-106">While Bower is maintained, they recommend using a different solution.</span></span> <span data-ttu-id="8bc32-107">Yarn Webpack ile kendisi için popüler bir alternatif olan [geçiş yönergeleri](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-107">Yarn with Webpack is one popular alternative for which [migration instructions](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) are available.</span></span>

<span data-ttu-id="8bc32-108">[Bower](https://bower.io/) kendisini "İçin bir paket yöneticisi web." çağırır</span><span class="sxs-lookup"><span data-stu-id="8bc32-108">[Bower](https://bower.io/) calls itself "A package manager for the web."</span></span> <span data-ttu-id="8bc32-109">.NET ekosistemi içinde statik içerik dosyaları NuGet verilmemesine göre sol void doldurur.</span><span class="sxs-lookup"><span data-stu-id="8bc32-109">Within the .NET ecosystem, it fills the void left by NuGet’s inability to deliver static content files.</span></span> <span data-ttu-id="8bc32-110">ASP.NET Core projeleri için bu statik dosyalar gibi istemci tarafı kitaplıklara devralınan [jQuery](http://jquery.com/) ve [önyükleme](http://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="8bc32-110">For ASP.NET Core projects, these static files are inherent to client-side libraries like [jQuery](http://jquery.com/) and [Bootstrap](http://getbootstrap.com/).</span></span> <span data-ttu-id="8bc32-111">.NET kitaplıkları için kullanmaya devam [NuGet](https://www.nuget.org/) Paket Yöneticisi.</span><span class="sxs-lookup"><span data-stu-id="8bc32-111">For .NET libraries, you still use [NuGet](https://www.nuget.org/) package manager.</span></span>

<span data-ttu-id="8bc32-112">İstemci-tarafı ayarlamak ASP.NET Core proje şablonları ile oluşturulan yeni projeler derleme işlemi.</span><span class="sxs-lookup"><span data-stu-id="8bc32-112">New projects created with the ASP.NET Core project templates set up the client-side build process.</span></span> <span data-ttu-id="8bc32-113">[jQuery](http://jquery.com/) ve [önyükleme](http://getbootstrap.com/) yüklü olan ve Bower desteklenir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-113">[jQuery](http://jquery.com/) and [Bootstrap](http://getbootstrap.com/) are installed, and Bower is supported.</span></span>

<span data-ttu-id="8bc32-114">İstemci tarafı paketleri listelenir *bower.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="8bc32-114">Client-side packages are listed in the *bower.json* file.</span></span> <span data-ttu-id="8bc32-115">ASP.NET Core proje şablonları yapılandırır *bower.json* jQuery, jQuery doğrulama ve önyükleme.</span><span class="sxs-lookup"><span data-stu-id="8bc32-115">The ASP.NET Core project templates configures *bower.json* with jQuery, jQuery validation, and Bootstrap.</span></span>

<span data-ttu-id="8bc32-116">Bu öğreticide desteği ekleyeceğiz [yazı tipi harika](http://fontawesome.io).</span><span class="sxs-lookup"><span data-stu-id="8bc32-116">In this tutorial, we'll add support for [Font Awesome](http://fontawesome.io).</span></span> <span data-ttu-id="8bc32-117">Bower paketleri ile yüklenebilir **Bower paketlerini Yönet** UI veya el ile *bower.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="8bc32-117">Bower packages can be installed with the **Manage Bower Packages** UI or manually in the *bower.json* file.</span></span>

### <a name="installation-via-manage-bower-packages-ui"></a><span data-ttu-id="8bc32-118">Yönet Bower paketleri kullanıcı Arabirimi aracılığıyla yükleme</span><span class="sxs-lookup"><span data-stu-id="8bc32-118">Installation via Manage Bower Packages UI</span></span>

* <span data-ttu-id="8bc32-119">İle yeni bir ASP.NET çekirdek Web uygulaması oluşturma **ASP.NET çekirdek Web uygulaması (.NET Core)** şablonu.</span><span class="sxs-lookup"><span data-stu-id="8bc32-119">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="8bc32-120">Seçin **Web uygulaması** ve **kimlik doğrulaması yok**.</span><span class="sxs-lookup"><span data-stu-id="8bc32-120">Select **Web Application** and **No Authentication**.</span></span>

* <span data-ttu-id="8bc32-121">Çözüm Gezgini'nde projeye sağ tıklayıp **Bower paketlerini Yönet** (Alternatif olarak ana menüden **proje** > **Bower paketlerini Yönet**).</span><span class="sxs-lookup"><span data-stu-id="8bc32-121">Right-click the project in Solution Explorer and select **Manage Bower Packages** (alternatively from the main menu, **Project** > **Manage Bower Packages**).</span></span>

* <span data-ttu-id="8bc32-122">İçinde **Bower: \<proje adı\>**  penceresinde "Gözat" sekmesini tıklatın ve ardından girerek paketlerin listesini filtrelemek `font-awesome` arama kutusuna:</span><span class="sxs-lookup"><span data-stu-id="8bc32-122">In the **Bower: \<project name\>** window, click the "Browse" tab, and then filter the packages list by entering `font-awesome` in the search box:</span></span>

 ![bower paketlerini yönetme](bower/_static/manage-bower-packages.png)

* <span data-ttu-id="8bc32-124">Onaylayın "değişiklikleri kaydedilsin *bower.json*" onay kutusu işaretli.</span><span class="sxs-lookup"><span data-stu-id="8bc32-124">Confirm that the "Save changes to *bower.json*" checkbox is checked.</span></span> <span data-ttu-id="8bc32-125">Aşağı açılan listeden bir sürüm seçin ve'ı tıklatın **yükleme** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="8bc32-125">Select a version from the drop-down list and click the **Install** button.</span></span> <span data-ttu-id="8bc32-126">**Çıkış** penceresi ilgili yükleme ayrıntılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-126">The **Output** window shows the installation details.</span></span>

### <a name="manual-installation-in-bowerjson"></a><span data-ttu-id="8bc32-127">Bower.json içinde el ile yükleme</span><span class="sxs-lookup"><span data-stu-id="8bc32-127">Manual installation in bower.json</span></span>

<span data-ttu-id="8bc32-128">Açık *bower.json* dosya ve "yazı tipi harika" bağımlılıklarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8bc32-128">Open the *bower.json* file and add "font-awesome" to the dependencies.</span></span> <span data-ttu-id="8bc32-129">IntelliSense kullanılabilir paketler gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-129">IntelliSense shows the available packages.</span></span> <span data-ttu-id="8bc32-130">Bir paket seçildiğinde kullanılabilir sürümlerin görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-130">When a package is selected, the available versions are displayed.</span></span> <span data-ttu-id="8bc32-131">Aşağıdaki görüntüleri eski ve gördüğünüz eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="8bc32-131">The images below are older and will not match what you see.</span></span>

![Bower paket Explorer IntelliSense](bower/_static/add-package.png)

![bower sürüm IntelliSense](bower/_static/version-intelliSense.png)

<span data-ttu-id="8bc32-134">Bower kullandığı [anlamsal sürüm oluşturma](http://semver.org/) bağımlılıkları düzenlemek için.</span><span class="sxs-lookup"><span data-stu-id="8bc32-134">Bower uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="8bc32-135">Anlamsal sürüm oluşturma olarak da bilinen SemVer tanımlayan paketleri numaralandırma düzeniyle \<ana >.\< İkincil >. \<düzeltme eki >.</span><span class="sxs-lookup"><span data-stu-id="8bc32-135">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="8bc32-136">IntelliSense, yalnızca birkaç genel seçenek göstererek anlamsal sürüm oluşturma basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-136">IntelliSense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="8bc32-137">Üst öğeyi IntelliSense listesinde (yukarıdaki örnekte 4.6.3), paketin en son kararlı sürümü olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-137">The top item in the IntelliSense list (4.6.3 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="8bc32-138">Şapka (^) karakteri son ana sürümle eşleşen ve en son ikincil sürümle tilde (~) eşleşir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-138">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span>

<span data-ttu-id="8bc32-139">Kaydet *bower.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="8bc32-139">Save the *bower.json* file.</span></span> <span data-ttu-id="8bc32-140">Visual Studio izleyen *bower.json* dosyasındaki değişiklikleri.</span><span class="sxs-lookup"><span data-stu-id="8bc32-140">Visual Studio watches the *bower.json* file for changes.</span></span> <span data-ttu-id="8bc32-141">Kaydedilmesi, *bower yükleme* komutu yürütülene.</span><span class="sxs-lookup"><span data-stu-id="8bc32-141">Upon saving, the *bower install* command is executed.</span></span> <span data-ttu-id="8bc32-142">Çıktı pencerenin bkz **npm/Bower** yürütülen tam komut için görünümü.</span><span class="sxs-lookup"><span data-stu-id="8bc32-142">See the Output window's **Bower/npm** view for the exact command executed.</span></span>

<span data-ttu-id="8bc32-143">Açık *.bowerrc* altında dosya *bower.json*.</span><span class="sxs-lookup"><span data-stu-id="8bc32-143">Open the *.bowerrc* file under *bower.json*.</span></span> <span data-ttu-id="8bc32-144">`directory` Özelliği ayarlanmış *wwwroot/lib* Bower konumu belirten paket varlıklar yükler.</span><span class="sxs-lookup"><span data-stu-id="8bc32-144">The `directory` property is set to *wwwroot/lib* which indicates the location Bower will install the package assets.</span></span>

```json
{
 "directory": "wwwroot/lib"
}
```

<span data-ttu-id="8bc32-145">Çözüm Gezgini arama kutusuna, bulmak ve yazı tipi harika paket görüntülemek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bc32-145">You can use the search box in Solution Explorer to find and display the font-awesome package.</span></span>

<span data-ttu-id="8bc32-146">Açık *görünümler/paylaşılan\_Layout.cshtml* dosya ve yazı tipi harika CSS dosyası ortama eklemek [etiket Yardımcısı](xref:mvc/views/tag-helpers/intro) için `Development`.</span><span class="sxs-lookup"><span data-stu-id="8bc32-146">Open the *Views\Shared\_Layout.cshtml* file and add the font-awesome CSS file to the environment [Tag Helper](xref:mvc/views/tag-helpers/intro) for `Development`.</span></span> <span data-ttu-id="8bc32-147">Çözüm Gezgini'nde, sürükleme ve bırakma *yazı tipi awesome.css* içinde `<environment names="Development">` öğesi.</span><span class="sxs-lookup"><span data-stu-id="8bc32-147">From Solution Explorer, drag and drop *font-awesome.css* inside the `<environment names="Development">` element.</span></span>

[!code-html[Main](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

<span data-ttu-id="8bc32-148">Bir üretim uygulamasında, eklediğiniz *yazı tipi awesome.min.css* için ortam etiketi Yardımcısı için `Staging,Production`.</span><span class="sxs-lookup"><span data-stu-id="8bc32-148">In a production app you would add *font-awesome.min.css* to the environment tag helper for `Staging,Production`.</span></span>

<span data-ttu-id="8bc32-149">Değiştir *Views\Home\About.cshtml* aşağıdaki biçimlendirme Razor dosyasıyla:</span><span class="sxs-lookup"><span data-stu-id="8bc32-149">Replace the contents of the *Views\Home\About.cshtml* Razor file with the following markup:</span></span>

[!code-html[Main](bower/sample/About.cshtml)]

<span data-ttu-id="8bc32-150">Uygulamayı çalıştırın ve yazı tipi harika paket çalıştığını doğrulamak için hakkında görünümüne gidin.</span><span class="sxs-lookup"><span data-stu-id="8bc32-150">Run the app and navigate to the About view to verify the font-awesome package works.</span></span>

## <a name="exploring-the-client-side-build-process"></a><span data-ttu-id="8bc32-151">İstemci tarafı yapılandırma işlemi keşfetme</span><span class="sxs-lookup"><span data-stu-id="8bc32-151">Exploring the client-side build process</span></span>

<span data-ttu-id="8bc32-152">Çoğu ASP.NET Core proje şablonları Bower kullanmak üzere önceden yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8bc32-152">Most ASP.NET Core project templates are already configured to use Bower.</span></span> <span data-ttu-id="8bc32-153">Sonraki Bu izlenecek yol boş ASP.NET Core proje ile başlar ve bir projede Bower nasıl kullanıldığı için sınıflandırıp her parça el ile ekler.</span><span class="sxs-lookup"><span data-stu-id="8bc32-153">This next walkthrough starts with an empty ASP.NET Core project and adds each piece manually, so you can get a feel for how Bower is used in a project.</span></span> <span data-ttu-id="8bc32-154">Proje yapısı ve her bir yapılandırma değişikliği yaptığınız gibi çıktı çalışma zamanı için olanlar görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bc32-154">You can see what happens to the project structure and the runtime output as each configuration change is made.</span></span>

<span data-ttu-id="8bc32-155">İstemci tarafı yapılandırma işlemi Bower ile kullanmak için gereken genel adımlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8bc32-155">The general steps to use the client-side build process with Bower are:</span></span>

* <span data-ttu-id="8bc32-156">Projenizde kullanılan paketleri tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="8bc32-156">Define packages used in your project.</span></span> <!-- once defined, you don't need to download them, VS does -->
* <span data-ttu-id="8bc32-157">Web sayfalarınıza paketlerinden başvuru.</span><span class="sxs-lookup"><span data-stu-id="8bc32-157">Reference packages from your web pages.</span></span>

### <a name="define-packages"></a><span data-ttu-id="8bc32-158">Paket tanımlama</span><span class="sxs-lookup"><span data-stu-id="8bc32-158">Define packages</span></span>

<span data-ttu-id="8bc32-159">Paketler listesi sonra *bower.json* dosyası, Visual Studio bunları indirir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-159">Once you list packages in the *bower.json* file, Visual Studio will download them.</span></span> <span data-ttu-id="8bc32-160">Aşağıdaki örnek, jQuery ve önyükleme için yüklemek için Bower kullanır. *wwwroot* klasör.</span><span class="sxs-lookup"><span data-stu-id="8bc32-160">The following example uses Bower to load jQuery and Bootstrap to the *wwwroot* folder.</span></span>

* <span data-ttu-id="8bc32-161">İle yeni bir ASP.NET çekirdek Web uygulaması oluşturma **ASP.NET çekirdek Web uygulaması (.NET Core)** şablonu.</span><span class="sxs-lookup"><span data-stu-id="8bc32-161">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="8bc32-162">Seçin **boş** proje şablonu ve tıklatın **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="8bc32-162">Select the **Empty** project template and click **OK**.</span></span>

* <span data-ttu-id="8bc32-163">Çözüm Gezgini'nde projeye sağ tıklayın > **Yeni Öğe Ekle** seçip **Bower yapılandırma dosyası**.</span><span class="sxs-lookup"><span data-stu-id="8bc32-163">In Solution Explorer, right-click the project > **Add New Item** and select **Bower Configuration File**.</span></span> <span data-ttu-id="8bc32-164">Not: A *.bowerrc* dosyası da eklenir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-164">Note: A *.bowerrc* file is also added.</span></span>

* <span data-ttu-id="8bc32-165">Açık *bower.json*, jquery ekleme ve için bootstrap `dependencies` bölümü.</span><span class="sxs-lookup"><span data-stu-id="8bc32-165">Open *bower.json*, and add jquery and bootstrap to the `dependencies` section.</span></span> <span data-ttu-id="8bc32-166">Elde edilen *bower.json* dosya, aşağıdaki gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="8bc32-166">The resulting *bower.json* file will look like the following example.</span></span> <span data-ttu-id="8bc32-167">Sürümler, zaman içinde değişir ve aşağıdaki görüntü eşleşmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="8bc32-167">The versions will change over time and may not match the image below.</span></span>

[!code-json[Main](bower/sample/bower.json?highlight=5,6)]

* <span data-ttu-id="8bc32-168">Kaydet *bower.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="8bc32-168">Save the *bower.json* file.</span></span>

 <span data-ttu-id="8bc32-169">Proje içeriyor doğrulayın *önyükleme* ve *jQuery* dizinlerde *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="8bc32-169">Verify the project includes the *bootstrap* and *jQuery* directories in *wwwroot/lib*.</span></span> <span data-ttu-id="8bc32-170">Bower kullandığı *.bowerrc* varlıkları yüklemek için dosya *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="8bc32-170">Bower uses the *.bowerrc* file to install the assets in *wwwroot/lib*.</span></span>

 <span data-ttu-id="8bc32-171">Not: El ile Dosya düzenlemeye alternatif "Bower paketlerini Yönet" kullanıcı Arabirimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="8bc32-171">Note: The "Manage Bower Packages" UI provides an alternative to manual file editing.</span></span>

### <a name="enable-static-files"></a><span data-ttu-id="8bc32-172">Statik dosyaları etkinleştir</span><span class="sxs-lookup"><span data-stu-id="8bc32-172">Enable static files</span></span>

* <span data-ttu-id="8bc32-173">Ekleme `Microsoft.AspNetCore.StaticFiles` NuGet paketini projeye.</span><span class="sxs-lookup"><span data-stu-id="8bc32-173">Add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project.</span></span>
* <span data-ttu-id="8bc32-174">İle sunulacak statik dosyaları etkinleştir [statik dosya ara yazılımlarını](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions).</span><span class="sxs-lookup"><span data-stu-id="8bc32-174">Enable static files to be served with the [Static file middleware](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions).</span></span> <span data-ttu-id="8bc32-175">Bir çağrı ekleyin [UseStaticFiles](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions) için `Configure` yöntemi `Startup`.</span><span class="sxs-lookup"><span data-stu-id="8bc32-175">Add a call to [UseStaticFiles](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.staticfileextensions) to the `Configure` method of `Startup`.</span></span>

[!code-csharp[Main](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a><span data-ttu-id="8bc32-176">Referans paketleri</span><span class="sxs-lookup"><span data-stu-id="8bc32-176">Reference packages</span></span>

<span data-ttu-id="8bc32-177">Bu bölümde, dağıtılan paketler erişebildiğinizi doğrulamak için bir HTML sayfası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8bc32-177">In this section, you will create an HTML page to verify it can access the deployed packages.</span></span>

* <span data-ttu-id="8bc32-178">Adlı yeni bir HTML sayfası Ekle *Index.html* için *wwwroot* klasör.</span><span class="sxs-lookup"><span data-stu-id="8bc32-178">Add a new HTML page named *Index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="8bc32-179">Not: HTML dosyasına eklemelisiniz *wwwroot* klasör.</span><span class="sxs-lookup"><span data-stu-id="8bc32-179">Note: You must add the HTML file to the *wwwroot* folder.</span></span> <span data-ttu-id="8bc32-180">Varsayılan olarak, statik içerik dışında sunulamıyor *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8bc32-180">By default, static content cannot be served outside *wwwroot*.</span></span> <span data-ttu-id="8bc32-181">Bkz: [statik dosyaları ile çalışma](xref:fundamentals/static-files) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="8bc32-181">See [Working with static files](xref:fundamentals/static-files) for more information.</span></span>

 <span data-ttu-id="8bc32-182">Değiştir *Index.html* aşağıdaki biçimlendirme ile:</span><span class="sxs-lookup"><span data-stu-id="8bc32-182">Replace the contents of *Index.html* with the following markup:</span></span>

[!code-html[Main](bower/sample/Index.html)]

* <span data-ttu-id="8bc32-183">Uygulamayı çalıştırın ve gidin `http://localhost:<port>/Index.html`.</span><span class="sxs-lookup"><span data-stu-id="8bc32-183">Run the app and navigate to `http://localhost:<port>/Index.html`.</span></span> <span data-ttu-id="8bc32-184">Alternatif olarak, ile *Index.html* açıldı, basın `Ctrl+Shift+W`.</span><span class="sxs-lookup"><span data-stu-id="8bc32-184">Alternatively, with *Index.html* opened, press `Ctrl+Shift+W`.</span></span> <span data-ttu-id="8bc32-185">Jumbotron stil uygulanır, düğme tıklatıldığında jQuery kodu yanıt verir ve önyükleme düğmesi durumu değiştiğinde doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8bc32-185">Verify that the jumbotron styling is applied, the jQuery code responds when the button is clicked, and that the Bootstrap button changes state.</span></span>

 ![jumbotron stili](bower/_static/jumbotron.png)
