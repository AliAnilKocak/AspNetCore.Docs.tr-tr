---
title: ASP.NET Core temelleri
author: rick-anderson
description: ASP.NET Core uygulamaları oluşturmak için temel kavramları öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: da2b42a7cf5d116a36d1dd9fa586d40ab31fc52d
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417649"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="51d1c-103">ASP.NET Core temelleri</span><span class="sxs-lookup"><span data-stu-id="51d1c-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51d1c-104">Bu makalede, ASP.NET Core uygulamalarının nasıl geliştirileni anlamak için önemli konulara genel bir bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="51d1c-105">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="51d1c-105">The Startup class</span></span>

<span data-ttu-id="51d1c-106">Sınıf `Startup` nerede:</span><span class="sxs-lookup"><span data-stu-id="51d1c-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="51d1c-107">Uygulamanın gerektirdiği hizmetler yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="51d1c-108">Uygulamanın istek işleme ardışık hattı, bir dizi ara yazılım bileşeni olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="51d1c-109">İşte örnek bir `Startup` sınıf:</span><span class="sxs-lookup"><span data-stu-id="51d1c-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="51d1c-110">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="51d1c-111">Bağımlılık ekleme (hizmetler)</span><span class="sxs-lookup"><span data-stu-id="51d1c-111">Dependency injection (services)</span></span>

<span data-ttu-id="51d1c-112">ASP.NET Core, yapılandırılmış hizmetleri bir uygulama boyunca kullanılabilir kılan yerleşik bir bağımlılık enjeksiyonu (DI) çerçevesi içerir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="51d1c-113">Örneğin, günlük bileşeni bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="51d1c-114">Yapılandırma (veya *kayıt)* hizmetlerini yapılandırma kodu `Startup.ConfigureServices` yönteme eklenir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="51d1c-115">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="51d1c-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="51d1c-116">Hizmetler genellikle constructor enjeksiyonu kullanılarak DI'den çözülür.</span><span class="sxs-lookup"><span data-stu-id="51d1c-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="51d1c-117">Oluşturucu enjeksiyonile, bir sınıf gerekli tür veya arabirim bir yapıcı parametre bildirir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="51d1c-118">DI çerçevesi çalışma zamanında bu hizmetin bir örneğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="51d1c-119">Aşağıdaki örnek, DI'den bir `RazorPagesMovieContext` çözüm için yapıcı enjeksiyon kullanır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="51d1c-120">Yerleşik Denetim Ters Çevirme (IoC) kapsayıcısı bir uygulamanın tüm gereksinimlerini karşılamazsa, bunun yerine bir üçüncü taraf IoC kapsayıcısı kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="51d1c-121">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="51d1c-122">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="51d1c-122">Middleware</span></span>

<span data-ttu-id="51d1c-123">İstek işleme ardışık bir dizi ara yazılım bileşeni olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="51d1c-124">Her bileşen bir `HttpContext` işlem gerçekleştirir ve ya ardışık bir sonraki ara çağırır veya isteği sona erdirir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="51d1c-125">Kural kuralıyla, `Use...` `Startup.Configure` yöntemde bir uzantı yöntemi çağırılarak ardışık bir bileşen ardışık bileşen eklenir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="51d1c-126">Örneğin, statik dosyaların işlenmesini etkinleştirmek `UseStaticFiles`için .</span><span class="sxs-lookup"><span data-stu-id="51d1c-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="51d1c-127">Aşağıdaki örnek, bir istek işleme ardışık yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="51d1c-128">ASP.NET Core dahili ara yazılım zengin bir dizi içerir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="51d1c-129">Özel ara yazılım bileşenleri de yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="51d1c-130">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="51d1c-131">Host</span><span class="sxs-lookup"><span data-stu-id="51d1c-131">Host</span></span>

<span data-ttu-id="51d1c-132">Başlangıç tarihinde, ASP.NET Core uygulaması bir *ana bilgisayar*oluşturur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="51d1c-133">Ev sahibi, uygulamanın tüm kaynaklarını kapsüller:</span><span class="sxs-lookup"><span data-stu-id="51d1c-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="51d1c-134">Bir HTTP sunucu uygulaması</span><span class="sxs-lookup"><span data-stu-id="51d1c-134">An HTTP server implementation</span></span>
* <span data-ttu-id="51d1c-135">Ara yazılım bileşenleri</span><span class="sxs-lookup"><span data-stu-id="51d1c-135">Middleware components</span></span>
* <span data-ttu-id="51d1c-136">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="51d1c-136">Logging</span></span>
* <span data-ttu-id="51d1c-137">Bağımlılık enjeksiyonu (DI) hizmetleri</span><span class="sxs-lookup"><span data-stu-id="51d1c-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="51d1c-138">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="51d1c-138">Configuration</span></span>

<span data-ttu-id="51d1c-139">İki farklı ana bilgisayar vardır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-139">There are two different hosts:</span></span> 

* <span data-ttu-id="51d1c-140">.NET Genel Ana Bilgisayar</span><span class="sxs-lookup"><span data-stu-id="51d1c-140">.NET Generic Host</span></span>
* <span data-ttu-id="51d1c-141">ASP.NET Çekirdek Web Host</span><span class="sxs-lookup"><span data-stu-id="51d1c-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="51d1c-142">.NET Genel Ana Bilgisayar önerilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="51d1c-143">ASP.NET Core Web Host yalnızca geriye dönük uyumluluk için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="51d1c-144">Aşağıdaki örnekte bir .NET Genel Ana Bilgisayar oluşturur:</span><span class="sxs-lookup"><span data-stu-id="51d1c-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="51d1c-145">Ve `CreateDefaultBuilder` `ConfigureWebHostDefaults` yöntemler gibi varsayılan seçenekler kümesi ile bir ana bilgisayar yapılandırmak:</span><span class="sxs-lookup"><span data-stu-id="51d1c-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="51d1c-146">[Kerkenez'i](#servers) web sunucusu olarak kullanın ve IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="51d1c-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="51d1c-147">*appsettings.json*, appsettings.{ adresinden yapılandırmayı *yükleyin. Çevre Adı}.json*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.</span><span class="sxs-lookup"><span data-stu-id="51d1c-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="51d1c-148">Günlüğe kaydetme çıktısını konsola gönderin ve hata ayıklama sağlayıcılarına gönderin.</span><span class="sxs-lookup"><span data-stu-id="51d1c-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="51d1c-149">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="51d1c-150">Web dışı senaryolar</span><span class="sxs-lookup"><span data-stu-id="51d1c-150">Non-web scenarios</span></span>

<span data-ttu-id="51d1c-151">Genel Ana Bilgisayar, günlük, bağımlılık enjeksiyonu (DI), yapılandırma ve uygulama ömrü yönetimi gibi çapraz kesme çerçeve uzantılarını diğer uygulama türlerinin kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="51d1c-152">Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="51d1c-153">Sunucular</span><span class="sxs-lookup"><span data-stu-id="51d1c-153">Servers</span></span>

<span data-ttu-id="51d1c-154">Bir ASP.NET Core uygulaması, HTTP isteklerini dinlemek için bir HTTP sunucusu uygulaması kullanır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="51d1c-155">Sunucu, uygulamadaki [istekleri](xref:fundamentals/request-features) `HttpContext`bir .</span><span class="sxs-lookup"><span data-stu-id="51d1c-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="51d1c-156">Windows</span><span class="sxs-lookup"><span data-stu-id="51d1c-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="51d1c-157">ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="51d1c-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="51d1c-158">*Kerkenez* bir çapraz platform web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="51d1c-159">Kerkenez genellikle [IIS](https://www.iis.net/)kullanılarak ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="51d1c-160">Core 2.0 veya daha sonra ASP.NET,Kestrel doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="51d1c-161">*IIS HTTP Server,* Windows için IIS kullanan bir sunucudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="51d1c-162">Bu sunucu ile ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="51d1c-163">*HTTP.sys,* Windows için IIS ile kullanılmayan bir sunucudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="51d1c-164">macOS</span><span class="sxs-lookup"><span data-stu-id="51d1c-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="51d1c-165">ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="51d1c-166">Core 2.0 veya daha sonra ASP.NET, Kestrel doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="51d1c-167">Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="51d1c-168">Linux</span><span class="sxs-lookup"><span data-stu-id="51d1c-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="51d1c-169">ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="51d1c-170">Core 2.0 veya daha sonra ASP.NET, Kestrel doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="51d1c-171">Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="51d1c-172">Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="51d1c-173">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="51d1c-173">Configuration</span></span>

<span data-ttu-id="51d1c-174">ASP.NET Core, sıralı bir yapılandırma sağlayıcısı kümesinden adi değer çiftleri olarak ayarları alan bir yapılandırma çerçevesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="51d1c-175">Yerleşik yapılandırma sağlayıcıları *.json* dosyaları, *.xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="51d1c-176">Diğer kaynakları desteklemek için özel yapılandırma sağlayıcıları yazın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="51d1c-177">Varsayılan [olarak,](xref:fundamentals/configuration/index#default)ASP.NET Core uygulamaları *appsettings.json,* ortam değişkenleri, komut satırı ve daha fazla sını okuyacak şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="51d1c-178">Uygulamanın yapılandırması yüklendiğinde, ortam değişkenlerinden gelen değerler *appsettings.json'daki*değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="51d1c-179">İlgili yapılandırma değerlerini okumanın tercih edilen yolu [seçenekler deseni](xref:fundamentals/configuration/options)kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="51d1c-180">Daha fazla bilgi için seçenekler [deseni kullanarak Bind hiyerarşik yapılandırma verilerine](xref:fundamentals/configuration/index#optpat)bakın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="51d1c-181">Parolalar gibi gizli yapılandırma verilerini yönetmek için ASP.NET [Core, Gizli Yönetici'yi](xref:security/app-secrets#secret-manager)sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="51d1c-182">Üretim sırları için [Azure Key Vault'u](xref:security/key-vault-configuration)öneriyoruz.</span><span class="sxs-lookup"><span data-stu-id="51d1c-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="51d1c-183">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="51d1c-184">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="51d1c-184">Environments</span></span>

<span data-ttu-id="51d1c-185">Yürütme ortamları, `Development` `Staging` `Production`ASP.NET Core'da birinci sınıf bir kavramdır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="51d1c-186">Ortam değişkenini ayarlayarak uygulamanın `ASPNETCORE_ENVIRONMENT` içinde koştuğu ortamı belirtin.</span><span class="sxs-lookup"><span data-stu-id="51d1c-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="51d1c-187">ASP.NET Core, uygulama başlatmada ortam değişkenini `IWebHostEnvironment` okur ve değeri bir uygulamada depolar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="51d1c-188">Bu uygulama bağımlılık enjeksiyonu (DI) yoluyla bir uygulamanın her yerinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="51d1c-189">Aşağıdaki örnek, `Development` uygulamada çalışırken ayrıntılı hata bilgileri sağlayacak şekilde uygulamayı yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="51d1c-190">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="51d1c-191">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="51d1c-191">Logging</span></span>

<span data-ttu-id="51d1c-192">ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="51d1c-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="51d1c-193">Kullanılabilir sağlayıcılar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-193">Available providers include:</span></span>

* <span data-ttu-id="51d1c-194">Konsol</span><span class="sxs-lookup"><span data-stu-id="51d1c-194">Console</span></span>
* <span data-ttu-id="51d1c-195">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="51d1c-195">Debug</span></span>
* <span data-ttu-id="51d1c-196">Windows'da Olay İzleme</span><span class="sxs-lookup"><span data-stu-id="51d1c-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="51d1c-197">Windows Etkinlik Günlüğü</span><span class="sxs-lookup"><span data-stu-id="51d1c-197">Windows Event Log</span></span>
* <span data-ttu-id="51d1c-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="51d1c-198">TraceSource</span></span>
* <span data-ttu-id="51d1c-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="51d1c-199">Azure App Service</span></span>
* <span data-ttu-id="51d1c-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="51d1c-200">Azure Application Insights</span></span>

<span data-ttu-id="51d1c-201">Günlükleri oluşturmak için, <xref:Microsoft.Extensions.Logging.ILogger%601> bağımlılık enjeksiyon (DI) bir hizmet çözmek <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>ve gibi günlük yöntemleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="51d1c-202">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="51d1c-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="51d1c-203">Herhangi bir `LogInformation` sayıda alanı destekleme gibi günlüğe kaydetme yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="51d1c-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="51d1c-204">Bu alanlar genellikle bir ileti `string`oluşturmak için kullanılır, ancak bazı günlük sağlayıcıları bunları ayrı alanlar olarak veri deposuna gönderir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="51d1c-205">Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlamsal günlük özelliğini](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamalarını mümkün kılar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="51d1c-206">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="51d1c-207">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="51d1c-207">Routing</span></span>

<span data-ttu-id="51d1c-208">*Rota,* işleyiciye eşlenen bir URL desenidir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="51d1c-209">Işleyici genellikle bir Razor sayfası, Bir MVC denetleyicisinde bir eylem yöntemi veya bir ara yazılımdır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="51d1c-210">ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL'ler üzerinde kontrol sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="51d1c-211">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="51d1c-212">Hata işleme</span><span class="sxs-lookup"><span data-stu-id="51d1c-212">Error handling</span></span>

<span data-ttu-id="51d1c-213">ASP.NET Core, işleme hataları için yerleşik özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="51d1c-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="51d1c-214">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="51d1c-214">A developer exception page</span></span>
* <span data-ttu-id="51d1c-215">Özel hata sayfaları</span><span class="sxs-lookup"><span data-stu-id="51d1c-215">Custom error pages</span></span>
* <span data-ttu-id="51d1c-216">Statik durum kodu sayfaları</span><span class="sxs-lookup"><span data-stu-id="51d1c-216">Static status code pages</span></span>
* <span data-ttu-id="51d1c-217">Başlangıç özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="51d1c-217">Startup exception handling</span></span>

<span data-ttu-id="51d1c-218">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="51d1c-219">HTTP isteğinde bulunma</span><span class="sxs-lookup"><span data-stu-id="51d1c-219">Make HTTP requests</span></span>

<span data-ttu-id="51d1c-220">Örnekler oluşturmak `IHttpClientFactory` `HttpClient` için bir uygulama kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="51d1c-221">Fabrika:</span><span class="sxs-lookup"><span data-stu-id="51d1c-221">The factory:</span></span>

* <span data-ttu-id="51d1c-222">Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="51d1c-223">Örneğin, *GitHub'a* erişmek için bir github istemcisi kaydedin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="51d1c-224">Varsayılan istemciyi başka amaçlariçin kaydedin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="51d1c-225">Giden bir istek ara yazılım boru hattı oluşturmak için birden çok devretme işleyicilerinin kaydedilmesi ve zincirlemesi destekler.</span><span class="sxs-lookup"><span data-stu-id="51d1c-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="51d1c-226">Bu desen, ASP.NET Core'un gelen ara yazılım boru hattına benzer.</span><span class="sxs-lookup"><span data-stu-id="51d1c-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="51d1c-227">Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri için çapraz kesme endişelerini yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="51d1c-228">Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly*ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="51d1c-229">Yaşam ömürlerini el ile `HttpClientHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="51d1c-230">Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler <xref:Microsoft.Extensions.Logging.ILogger> için yapılandırılabilir bir günlük deneyimi ekler.</span><span class="sxs-lookup"><span data-stu-id="51d1c-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="51d1c-231">Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="51d1c-232">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="51d1c-232">Content root</span></span>

<span data-ttu-id="51d1c-233">İçerik kökü aşağıdakiler için temel yoldur:</span><span class="sxs-lookup"><span data-stu-id="51d1c-233">The content root is the base path for:</span></span>

* <span data-ttu-id="51d1c-234">Uygulamanın yürütülebilir barındırma (*.exe*).</span><span class="sxs-lookup"><span data-stu-id="51d1c-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="51d1c-235">Uygulamayı oluşturan derleme derlemeler (*.dll*).</span><span class="sxs-lookup"><span data-stu-id="51d1c-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="51d1c-236">Uygulama tarafından kullanılan içerik dosyaları, örneğin:</span><span class="sxs-lookup"><span data-stu-id="51d1c-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="51d1c-237">Jilet dosyaları (*.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="51d1c-238">Yapılandırma dosyaları (*.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="51d1c-239">Veri dosyaları (*.db*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-239">Data files (*.db*)</span></span>
* <span data-ttu-id="51d1c-240">[Web kökü](#web-root), genellikle *wwwroot* klasörü.</span><span class="sxs-lookup"><span data-stu-id="51d1c-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="51d1c-241">Geliştirme sırasında, içerik kökü varsayılan olarak projenin kök dizinine iner.</span><span class="sxs-lookup"><span data-stu-id="51d1c-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="51d1c-242">Bu dizin aynı zamanda hem uygulamanın içerik dosyaları hem de [Web kökü](#web-root)için temel yoldur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="51d1c-243">Ana bilgisayarı kurarken yolunu ayarlayarak farklı bir içerik kökü [belirtin.](#host)</span><span class="sxs-lookup"><span data-stu-id="51d1c-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="51d1c-244">Daha fazla bilgi için [İçerik köküne](xref:fundamentals/host/generic-host#contentrootpath-1)bakın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentrootpath-1).</span></span>

## <a name="web-root"></a><span data-ttu-id="51d1c-245">Web kökü</span><span class="sxs-lookup"><span data-stu-id="51d1c-245">Web root</span></span>

<span data-ttu-id="51d1c-246">Web kökü, genel, statik kaynak dosyalarının temel yoludur:</span><span class="sxs-lookup"><span data-stu-id="51d1c-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="51d1c-247">Stylesheets (*.css*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="51d1c-248">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="51d1c-249">Görüntüler (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="51d1c-250">Varsayılan olarak, statik dosyalar yalnızca web kök dizini ve alt dizinlerinden sunulur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="51d1c-251">Web kök yolu *varsayılan olarak {içerik kökü}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="51d1c-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="51d1c-252">Ana bilgisayarı kurarken yolunu ayarlayarak farklı bir web kökü [belirtin.](#host)</span><span class="sxs-lookup"><span data-stu-id="51d1c-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="51d1c-253">Daha fazla bilgi için [Web köküne](xref:fundamentals/host/generic-host#webroot-1)bakın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot-1).</span></span>

<span data-ttu-id="51d1c-254">Proje dosyasındaki [ \<İçerik> proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *wwwroot'da* dosya yayımlamayı engelleyin.</span><span class="sxs-lookup"><span data-stu-id="51d1c-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="51d1c-255">Aşağıdaki örnek, *wwwroot/local* ve alt dizinlerinde içerik yayımlamayı engeller:</span><span class="sxs-lookup"><span data-stu-id="51d1c-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="51d1c-256">Razor *.cshtml* dosyalarında, tilde-slash (`~/`) web kökünü işaret eder.</span><span class="sxs-lookup"><span data-stu-id="51d1c-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="51d1c-257">Ile `~/` başlayan bir *yol sanal yol*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="51d1c-258">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51d1c-259">Bu makale, ASP.NET Core uygulamalarının nasıl geliştirilebildiğini anlamak için önemli konuların genel bir özetidir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="51d1c-260">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="51d1c-260">The Startup class</span></span>

<span data-ttu-id="51d1c-261">Sınıf `Startup` nerede:</span><span class="sxs-lookup"><span data-stu-id="51d1c-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="51d1c-262">Uygulamanın gerektirdiği hizmetler yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="51d1c-263">İstek işleme ardışık alanı tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="51d1c-264">*Hizmetler,* uygulama tarafından kullanılan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="51d1c-265">Örneğin, günlük bileşeni bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-265">For example, a logging component is a service.</span></span> <span data-ttu-id="51d1c-266">Yapılandırma (veya *kayıt)* hizmetlerini yapılandırma kodu `Startup.ConfigureServices` yönteme eklenir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="51d1c-267">İstek işleme ardışık bir dizi *ara yazılım* bileşeni olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="51d1c-268">Örneğin, bir ara yazılım statik dosyalar için isteklerini işleyebilir veya HTTP isteklerini HTTPS'ye yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="51d1c-269">Her ara yazılım bir `HttpContext` üzerinde eşzamanlı işlemler gerçekleştirir ve sonra bir sonraki ara yazılımı çağırır veya isteği sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="51d1c-270">İstek işleme ardışık hattını yapılandırmak `Startup.Configure` için kod yönteme eklenir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="51d1c-271">İşte örnek bir `Startup` sınıf:</span><span class="sxs-lookup"><span data-stu-id="51d1c-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="51d1c-272">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="51d1c-273">Bağımlılık ekleme (hizmetler)</span><span class="sxs-lookup"><span data-stu-id="51d1c-273">Dependency injection (services)</span></span>

<span data-ttu-id="51d1c-274">ASP.NET Core, yapılandırılmış hizmetleri uygulamasınıfları için kullanılabilir kılan yerleşik bir bağımlılık enjeksiyonu (DI) çerçevesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="51d1c-275">Bir sınıfta bir hizmet örneği almak için bir yolu gerekli türde bir parametre ile bir oluşturucu oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="51d1c-276">Parametre hizmet türü veya arabirim olabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="51d1c-277">DI sistemi hizmeti çalışma zamanında sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="51d1c-278">Burada, Varlık Çerçevesi Temel bağlam nesnesi almak için DI kullanan bir sınıf ver.</span><span class="sxs-lookup"><span data-stu-id="51d1c-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="51d1c-279">Vurgulanan satır yapıcı enjeksiyon bir örnektir:</span><span class="sxs-lookup"><span data-stu-id="51d1c-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="51d1c-280">DI yerleşik olsa da, isterseniz bir üçüncü taraf Inversion of Control (IoC) kapsayıcısı takmanıza izin vermek üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="51d1c-281">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="51d1c-282">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="51d1c-282">Middleware</span></span>

<span data-ttu-id="51d1c-283">İstek işleme ardışık bir dizi ara yazılım bileşeni olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="51d1c-284">Her bileşen bir `HttpContext` üzerinde eşzamanlı işlemler gerçekleştirir ve ardışık ardışık ardışık bir sonraki ara yazılımı çağırır veya isteği sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="51d1c-285">Kural kuralıyla, `Use...` `Startup.Configure` yöntemdeki uzantı yöntemini çağırarak ardışık bir bileşen ardışık bileşen eklenir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="51d1c-286">Örneğin, statik dosyaların işlenmesini etkinleştirmek `UseStaticFiles`için .</span><span class="sxs-lookup"><span data-stu-id="51d1c-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="51d1c-287">Aşağıdaki örnekte vurgulanan kod, istek işleme ardışık hattını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="51d1c-288">ASP.NET Core dahili ara yazılım zengin bir dizi içerir ve özel ara yazılım yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51d1c-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="51d1c-289">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="51d1c-290">Host</span><span class="sxs-lookup"><span data-stu-id="51d1c-290">Host</span></span>

<span data-ttu-id="51d1c-291">Bir ASP.NET Core uygulaması başlangıç üzerine bir *ana bilgisayar* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="51d1c-292">Ana bilgisayar, uygulamanın tüm kaynaklarını kapsayan bir nesnedir( örneğin:</span><span class="sxs-lookup"><span data-stu-id="51d1c-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="51d1c-293">Bir HTTP sunucu uygulaması</span><span class="sxs-lookup"><span data-stu-id="51d1c-293">An HTTP server implementation</span></span>
* <span data-ttu-id="51d1c-294">Ara yazılım bileşenleri</span><span class="sxs-lookup"><span data-stu-id="51d1c-294">Middleware components</span></span>
* <span data-ttu-id="51d1c-295">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="51d1c-295">Logging</span></span>
* <span data-ttu-id="51d1c-296">Di</span><span class="sxs-lookup"><span data-stu-id="51d1c-296">DI</span></span>
* <span data-ttu-id="51d1c-297">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="51d1c-297">Configuration</span></span>

<span data-ttu-id="51d1c-298">Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.</span><span class="sxs-lookup"><span data-stu-id="51d1c-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="51d1c-299">İki ana bilgisayar kullanılabilir: Web Ana Bilgisayar ve Genel Ana Bilgisayar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="51d1c-300">Core 2.xASP.NET, Genel Ana Bilgisayar yalnızca web olmayan senaryolar içindir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="51d1c-301">Ana bilgisayar oluşturmak için `Program.Main`kod:</span><span class="sxs-lookup"><span data-stu-id="51d1c-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="51d1c-302">Yöntem, `CreateDefaultBuilder` aşağıdakigibi sık kullanılan seçenekleri olan bir ana bilgisayarı yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="51d1c-303">[Kerkenez'i](#servers) web sunucusu olarak kullanın ve IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="51d1c-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="51d1c-304">*appsettings.json*, appsettings.{ adresinden yapılandırmayı *yükleyin. Çevre Adı}.json*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.</span><span class="sxs-lookup"><span data-stu-id="51d1c-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="51d1c-305">Günlüğe kaydetme çıktısını konsola gönderin ve hata ayıklama sağlayıcılarına gönderin.</span><span class="sxs-lookup"><span data-stu-id="51d1c-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="51d1c-306">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="51d1c-307">Web dışı senaryolar</span><span class="sxs-lookup"><span data-stu-id="51d1c-307">Non-web scenarios</span></span>

<span data-ttu-id="51d1c-308">Genel Ana Bilgisayar, günlük, bağımlılık enjeksiyonu (DI), yapılandırma ve uygulama ömrü yönetimi gibi çapraz kesme çerçeve uzantılarını diğer uygulama türlerinin kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="51d1c-309">Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="51d1c-310">Sunucular</span><span class="sxs-lookup"><span data-stu-id="51d1c-310">Servers</span></span>

<span data-ttu-id="51d1c-311">Bir ASP.NET Core uygulaması, HTTP isteklerini dinlemek için bir HTTP sunucusu uygulaması kullanır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="51d1c-312">Sunucu, uygulamadaki [istekleri](xref:fundamentals/request-features) `HttpContext`bir .</span><span class="sxs-lookup"><span data-stu-id="51d1c-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="51d1c-313">Windows</span><span class="sxs-lookup"><span data-stu-id="51d1c-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="51d1c-314">ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="51d1c-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="51d1c-315">*Kerkenez* bir çapraz platform web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="51d1c-316">Kerkenez genellikle [IIS](https://www.iis.net/)kullanılarak ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="51d1c-317">Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="51d1c-318">*IIS HTTP Server, IIS* kullanan bir windows sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="51d1c-319">Bu sunucu ile ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="51d1c-320">*HTTP.sys,* Windows için IIS ile kullanılmayan bir sunucudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="51d1c-321">macOS</span><span class="sxs-lookup"><span data-stu-id="51d1c-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="51d1c-322">ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="51d1c-323">Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="51d1c-324">Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="51d1c-325">Linux</span><span class="sxs-lookup"><span data-stu-id="51d1c-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="51d1c-326">ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="51d1c-327">Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="51d1c-328">Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="51d1c-329">Windows</span><span class="sxs-lookup"><span data-stu-id="51d1c-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="51d1c-330">ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="51d1c-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="51d1c-331">*Kerkenez* bir çapraz platform web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="51d1c-332">Kerkenez genellikle [IIS](https://www.iis.net/)kullanılarak ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="51d1c-333">Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="51d1c-334">*HTTP.sys,* Windows için IIS ile kullanılmayan bir sunucudur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="51d1c-335">macOS</span><span class="sxs-lookup"><span data-stu-id="51d1c-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="51d1c-336">ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="51d1c-337">Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="51d1c-338">Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="51d1c-339">Linux</span><span class="sxs-lookup"><span data-stu-id="51d1c-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="51d1c-340">ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="51d1c-341">Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="51d1c-342">Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51d1c-343">Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="51d1c-344">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="51d1c-344">Configuration</span></span>

<span data-ttu-id="51d1c-345">ASP.NET Core, sıralı bir yapılandırma sağlayıcısı kümesinden adi değer çiftleri olarak ayarları alan bir yapılandırma çerçevesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="51d1c-346">*.json* dosyaları, *.xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için yerleşik yapılandırma sağlayıcıları vardır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="51d1c-347">Özel yapılandırma sağlayıcıları da yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51d1c-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="51d1c-348">Örneğin, yapılandırmanın *appsettings.json* ve ortam değişkenlerinden geldiğini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51d1c-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="51d1c-349">Daha sonra *ConnectionString* değeri istendiğinde, çerçeve *appsettings.json* dosyasında ilk olarak görünür.</span><span class="sxs-lookup"><span data-stu-id="51d1c-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="51d1c-350">Değer orada değil, aynı zamanda bir ortam değişkeninde bulunursa, ortam değişkeninden gelen değer önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="51d1c-351">Parolalar gibi gizli yapılandırma verilerini yönetmek için ASP.NET Core bir [Gizli Yönetici aracı](xref:security/app-secrets)sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="51d1c-352">Üretim sırları için [Azure Key Vault'u](xref:security/key-vault-configuration)öneriyoruz.</span><span class="sxs-lookup"><span data-stu-id="51d1c-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="51d1c-353">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="51d1c-354">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="51d1c-354">Options</span></span>

<span data-ttu-id="51d1c-355">Mümkün olduğunda, ASP.NET Core yapılandırma değerlerini depolamak ve almak için *seçenekler modelini* izler.</span><span class="sxs-lookup"><span data-stu-id="51d1c-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="51d1c-356">Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="51d1c-357">Örneğin, aşağıdaki kod WebSockets seçeneklerini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="51d1c-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="51d1c-358">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="51d1c-359">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="51d1c-359">Environments</span></span>

<span data-ttu-id="51d1c-360">*Geliştirme,* *Evreleme*ve *Üretim*gibi yürütme ortamları, ASP.NET Core'da birinci sınıf bir kavramdır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="51d1c-361">`ASPNETCORE_ENVIRONMENT` Ortam değişkenini ayarlayarak bir uygulamanın içinde koştuğu ortamı belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51d1c-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="51d1c-362">ASP.NET Core, uygulama başlatmada ortam değişkenini `IHostingEnvironment` okur ve değeri bir uygulamada depolar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="51d1c-363">Ortam nesnesi DI üzerinden uygulamanın her yerinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="51d1c-364">`Startup` Sınıftan gelen aşağıdaki örnek kod, uygulamayı yalnızca geliştirme aşamasında çalıştığında ayrıntılı hata bilgileri sağlayacak şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="51d1c-365">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="51d1c-366">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="51d1c-366">Logging</span></span>

<span data-ttu-id="51d1c-367">ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="51d1c-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="51d1c-368">Kullanılabilir sağlayıcılar aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="51d1c-368">Available providers include the following:</span></span>

* <span data-ttu-id="51d1c-369">Konsol</span><span class="sxs-lookup"><span data-stu-id="51d1c-369">Console</span></span>
* <span data-ttu-id="51d1c-370">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="51d1c-370">Debug</span></span>
* <span data-ttu-id="51d1c-371">Windows'da Olay İzleme</span><span class="sxs-lookup"><span data-stu-id="51d1c-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="51d1c-372">Windows Etkinlik Günlüğü</span><span class="sxs-lookup"><span data-stu-id="51d1c-372">Windows Event Log</span></span>
* <span data-ttu-id="51d1c-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="51d1c-373">TraceSource</span></span>
* <span data-ttu-id="51d1c-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="51d1c-374">Azure App Service</span></span>
* <span data-ttu-id="51d1c-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="51d1c-375">Azure Application Insights</span></span>

<span data-ttu-id="51d1c-376">DI'den bir `ILogger` nesne alarak ve günlük yöntemlerini çağırarak bir uygulamanın kodundaki herhangi bir yerden günlükler yazın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="51d1c-377">Burada, oluşturucu enjeksiyonve `ILogger` günlüğe kaydetme yöntemi çağrıları vurgulanan bir nesne kullanan örnek kod.</span><span class="sxs-lookup"><span data-stu-id="51d1c-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="51d1c-378">Arabirim, `ILogger` istediğiniz sayıda alanı günlük sağlayıcısına geçirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="51d1c-379">Alanlar genellikle bir ileti dizesi oluşturmak için kullanılır, ancak sağlayıcı da bir veri deposuna ayrı alanlar olarak gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51d1c-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="51d1c-380">Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlamsal günlük özelliğini](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamalarını mümkün kılar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="51d1c-381">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="51d1c-382">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="51d1c-382">Routing</span></span>

<span data-ttu-id="51d1c-383">*Rota,* işleyiciye eşlenen bir URL desenidir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="51d1c-384">Işleyici genellikle bir Razor sayfası, Bir MVC denetleyicisinde bir eylem yöntemi veya bir ara yazılımdır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="51d1c-385">ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL'ler üzerinde kontrol sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="51d1c-386">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="51d1c-387">Hata işleme</span><span class="sxs-lookup"><span data-stu-id="51d1c-387">Error handling</span></span>

<span data-ttu-id="51d1c-388">ASP.NET Core, işleme hataları için yerleşik özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="51d1c-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="51d1c-389">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="51d1c-389">A developer exception page</span></span>
* <span data-ttu-id="51d1c-390">Özel hata sayfaları</span><span class="sxs-lookup"><span data-stu-id="51d1c-390">Custom error pages</span></span>
* <span data-ttu-id="51d1c-391">Statik durum kodu sayfaları</span><span class="sxs-lookup"><span data-stu-id="51d1c-391">Static status code pages</span></span>
* <span data-ttu-id="51d1c-392">Başlangıç özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="51d1c-392">Startup exception handling</span></span>

<span data-ttu-id="51d1c-393">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="51d1c-394">HTTP isteğinde bulunma</span><span class="sxs-lookup"><span data-stu-id="51d1c-394">Make HTTP requests</span></span>

<span data-ttu-id="51d1c-395">Örnekler oluşturmak `IHttpClientFactory` `HttpClient` için bir uygulama kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="51d1c-396">Fabrika:</span><span class="sxs-lookup"><span data-stu-id="51d1c-396">The factory:</span></span>

* <span data-ttu-id="51d1c-397">Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="51d1c-398">Örneğin, *github* istemcisi kaydedilebilir ve GitHub'a erişmek için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="51d1c-399">Varsayılan istemci başka amaçlarla kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="51d1c-400">Giden bir istek ara yazılım boru hattı oluşturmak için birden çok devretme işleyicilerinin kaydedilmesi ve zincirlemesi destekler.</span><span class="sxs-lookup"><span data-stu-id="51d1c-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="51d1c-401">Bu desen, ASP.NET Core'daki gelen ara yazılım düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="51d1c-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="51d1c-402">Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri etrafında çapraz kesme yle ilgili endişeleri yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="51d1c-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="51d1c-403">Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly*ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="51d1c-404">Yaşam ömürlerini el ile `HttpClientHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="51d1c-405">Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.</span><span class="sxs-lookup"><span data-stu-id="51d1c-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="51d1c-406">Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="51d1c-407">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="51d1c-407">Content root</span></span>

<span data-ttu-id="51d1c-408">İçerik kökü, aşağıdakilerin temel yoludur:</span><span class="sxs-lookup"><span data-stu-id="51d1c-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="51d1c-409">Uygulama (*.exe*) barındırma yürütülebilir .</span><span class="sxs-lookup"><span data-stu-id="51d1c-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="51d1c-410">Uygulamayı oluşturan derleme derlemeler (*.dll*).</span><span class="sxs-lookup"><span data-stu-id="51d1c-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="51d1c-411">Uygulama tarafından kullanılan kod dışı içerik dosyaları, örneğin:</span><span class="sxs-lookup"><span data-stu-id="51d1c-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="51d1c-412">Jilet dosyaları (*.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="51d1c-413">Yapılandırma dosyaları (*.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="51d1c-414">Veri dosyaları (*.db*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-414">Data files (*.db*)</span></span>
* <span data-ttu-id="51d1c-415">[Web kökü](#web-root), genellikle yayınlanan *wwwroot* klasörü.</span><span class="sxs-lookup"><span data-stu-id="51d1c-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="51d1c-416">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="51d1c-416">During development:</span></span>

* <span data-ttu-id="51d1c-417">İçerik kökü varsayılan olarak projenin kök dizinine iner.</span><span class="sxs-lookup"><span data-stu-id="51d1c-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="51d1c-418">Projenin kök dizini oluşturmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="51d1c-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="51d1c-419">Projenin kök dizininde uygulamanın kod olmayan içerik dosyalarına giden yol.</span><span class="sxs-lookup"><span data-stu-id="51d1c-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="51d1c-420">[Web kökü](#web-root), genellikle projenin kök dizinindeki *wwwroot* klasörü.</span><span class="sxs-lookup"><span data-stu-id="51d1c-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="51d1c-421">[Ana bilgisayar inşa](#host)ederken alternatif bir içerik kök yolu belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="51d1c-422">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="51d1c-423">Web kökü</span><span class="sxs-lookup"><span data-stu-id="51d1c-423">Web root</span></span>

<span data-ttu-id="51d1c-424">Web kökü, genel, kod suz, statik kaynak dosyalarına temel yoldur:</span><span class="sxs-lookup"><span data-stu-id="51d1c-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="51d1c-425">Stylesheets (*.css*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="51d1c-426">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="51d1c-427">Görüntüler (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="51d1c-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="51d1c-428">Statik dosyalar yalnızca varsayılan olarak web kök dizini (ve alt dizinler) tarafından sunulur.</span><span class="sxs-lookup"><span data-stu-id="51d1c-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="51d1c-429">Web kök yolu *varsayılan olarak {içerik kökü}/wwwroot*olarak belirlenir, ancak [ana bilgisayar ını kurarken](#host)farklı bir web kökü belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="51d1c-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="51d1c-430">Daha fazla bilgi için [Web köküne](xref:fundamentals/host/web-host#web-root)bakın.</span><span class="sxs-lookup"><span data-stu-id="51d1c-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="51d1c-431">Proje dosyasındaki [ \<İçerik> proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *wwwroot'da* dosya yayımlamayı engelleyin.</span><span class="sxs-lookup"><span data-stu-id="51d1c-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="51d1c-432">Aşağıdaki örnek, *wwwroot/yerel* dizinde ve alt dizinlerde içerik yayımlanmasını engeller:</span><span class="sxs-lookup"><span data-stu-id="51d1c-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="51d1c-433">Razor (*.cshtml*) dosyalarında, tilde-slash (`~/`) web kökünü işaret eder.</span><span class="sxs-lookup"><span data-stu-id="51d1c-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="51d1c-434">Ile `~/` başlayan bir *yol sanal yol*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="51d1c-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="51d1c-435">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="51d1c-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
