---
title: ASP.NET çekirdek yapılandırması
author: rick-anderson
description: Yapılandırma API'si tarafından birden çok yöntem ASP.NET Core uygulamayı yapılandırmak için kullanın.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/configuration/index
ms.openlocfilehash: 1048554c78e3810206b1261371ae7c41485c436a
ms.sourcegitcommit: 63fb07fb3f71b32daf2c9466e132f2e7cc617163
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2018
ms.locfileid: "35252132"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="f7c19-103">ASP.NET çekirdek yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f7c19-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="f7c19-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [işareti Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), [Daniel Roth](https://github.com/danroth27), ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f7c19-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f7c19-105">Yapılandırma API'si ad-değer çiftlerinin listesini temel web uygulamasını ASP.NET Core yapılandırmak için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-105">The Configuration API provides a way to configure an ASP.NET Core web app based on a list of name-value pairs.</span></span> <span data-ttu-id="f7c19-106">Yapılandırma, birden fazla kaynaktan çalışma zamanında okuyun.</span><span class="sxs-lookup"><span data-stu-id="f7c19-106">Configuration is read at runtime from multiple sources.</span></span> <span data-ttu-id="f7c19-107">Ad-değer çiftleri çok düzeyli bir hiyerarşiye gruplandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-107">Name-value pairs can be grouped into a multi-level hierarchy.</span></span>

<span data-ttu-id="f7c19-108">İçin yapılandırma sağlayıcısı vardır:</span><span class="sxs-lookup"><span data-stu-id="f7c19-108">There are configuration providers for:</span></span>

* <span data-ttu-id="f7c19-109">Dosya biçimleri (INI, JSON ve XML).</span><span class="sxs-lookup"><span data-stu-id="f7c19-109">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="f7c19-110">Komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f7c19-110">Command-line arguments.</span></span>
* <span data-ttu-id="f7c19-111">Ortam değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f7c19-111">Environment variables.</span></span>
* <span data-ttu-id="f7c19-112">Bellek içi .NET nesneleri.</span><span class="sxs-lookup"><span data-stu-id="f7c19-112">In-memory .NET objects.</span></span>
* <span data-ttu-id="f7c19-113">Şifrelenmemiş [gizli Yöneticisi](xref:security/app-secrets) depolama.</span><span class="sxs-lookup"><span data-stu-id="f7c19-113">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="f7c19-114">Şifrelenmiş bir kullanıcı depolamak, gibi [Azure anahtar kasası](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f7c19-114">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="f7c19-115">(Yüklü veya oluşturulan) özel sağlayıcıları.</span><span class="sxs-lookup"><span data-stu-id="f7c19-115">Custom providers (installed or created).</span></span>

<span data-ttu-id="f7c19-116">Her yapılandırma değeri bir dize anahtarı eşler.</span><span class="sxs-lookup"><span data-stu-id="f7c19-116">Each configuration value maps to a string key.</span></span> <span data-ttu-id="f7c19-117">Özel bir ayarları seri durumdan çıkarılacak yerleşik bağlama Destek [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) nesne (basit bir .NET sınıf özelliklerine sahip).</span><span class="sxs-lookup"><span data-stu-id="f7c19-117">There's built-in binding support to deserialize settings into a custom [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) object (a simple .NET class with properties).</span></span>

<span data-ttu-id="f7c19-118">Seçenekler düzeni seçenekleri sınıfları ilgili ayar gruplarını göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-118">The options pattern uses options classes to represent groups of related settings.</span></span> <span data-ttu-id="f7c19-119">Seçenekler kullanılması daha fazla bilgi için bkz: [seçenekleri](xref:fundamentals/configuration/options) konu.</span><span class="sxs-lookup"><span data-stu-id="f7c19-119">For more information on using the options pattern, see the [Options](xref:fundamentals/configuration/options) topic.</span></span>

<span data-ttu-id="f7c19-120">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7c19-120">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="json-configuration"></a><span data-ttu-id="f7c19-121">JSON yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f7c19-121">JSON configuration</span></span>

<span data-ttu-id="f7c19-122">Aşağıdaki konsol uygulaması JSON yapılandırma sağlayıcısı kullanır:</span><span class="sxs-lookup"><span data-stu-id="f7c19-122">The following console app uses the JSON configuration provider:</span></span>

[!code-csharp[](index/sample/ConfigJson/Program.cs)]

<span data-ttu-id="f7c19-123">Uygulama okur ve aşağıdaki yapılandırma ayarları görüntüler:</span><span class="sxs-lookup"><span data-stu-id="f7c19-123">The app reads and displays the following configuration settings:</span></span>

[!code-json[](index/sample/ConfigJson/appsettings.json)]

<span data-ttu-id="f7c19-124">Hiyerarşik düğümleri virgülle ayrılır ad-değer çiftlerinin listesini yapılandırma oluşur (`:`).</span><span class="sxs-lookup"><span data-stu-id="f7c19-124">Configuration consists of a hierarchical list of name-value pairs in which the nodes are separated by a colon (`:`).</span></span> <span data-ttu-id="f7c19-125">Bir değer almak için erişim `Configuration` karşılık gelen öğenin anahtarı ile dizinleyici:</span><span class="sxs-lookup"><span data-stu-id="f7c19-125">To retrieve a value, access the `Configuration` indexer with the corresponding item's key:</span></span>

[!code-csharp[](index/sample/ConfigJson/Program.cs?range=21-22)]

<span data-ttu-id="f7c19-126">JSON biçimli yapılandırma kaynaklarını dizilerde çalışmak için iki nokta üst üste ayrılmış dize bir parçası olarak bir dizi dizini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f7c19-126">To work with arrays in JSON-formatted configuration sources, use an array index as part of the colon-separated string.</span></span> <span data-ttu-id="f7c19-127">Aşağıdaki örnek önceki ilk öğe adını alır `wizards` dizi:</span><span class="sxs-lookup"><span data-stu-id="f7c19-127">The following example gets the name of the first item in the preceding `wizards` array:</span></span>

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}");
// Output: Gandalf
```

<span data-ttu-id="f7c19-128">Ad-değer çiftleri için yerleşik yazılmış [yapılandırma](/dotnet/api/microsoft.extensions.configuration) sağlayıcılarıdır **değil** kalıcı.</span><span class="sxs-lookup"><span data-stu-id="f7c19-128">Name-value pairs written to the built-in [Configuration](/dotnet/api/microsoft.extensions.configuration) providers are **not** persisted.</span></span> <span data-ttu-id="f7c19-129">Ancak, değerleri kaydeder özel bir sağlayıcı oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-129">However, a custom provider that saves values can be created.</span></span> <span data-ttu-id="f7c19-130">Bkz: [özel yapılandırma sağlayıcısının](xref:fundamentals/configuration/index#custom-config-providers).</span><span class="sxs-lookup"><span data-stu-id="f7c19-130">See [custom configuration provider](xref:fundamentals/configuration/index#custom-config-providers).</span></span>

<span data-ttu-id="f7c19-131">Yukarıdaki örnek yapılandırma dizin oluşturucu değerleri okumak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-131">The preceding sample uses the configuration indexer to read values.</span></span> <span data-ttu-id="f7c19-132">Erişim yapılandırması dışında `Startup`, kullanın *seçenekleri düzeni*.</span><span class="sxs-lookup"><span data-stu-id="f7c19-132">To access configuration outside of `Startup`, use the *options pattern*.</span></span> <span data-ttu-id="f7c19-133">Daha fazla bilgi için bkz: [seçenekleri](xref:fundamentals/configuration/options) konu.</span><span class="sxs-lookup"><span data-stu-id="f7c19-133">For more information, see the [Options](xref:fundamentals/configuration/options) topic.</span></span>

## <a name="xml-configuration"></a><span data-ttu-id="f7c19-134">XML yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f7c19-134">XML configuration</span></span>

<span data-ttu-id="f7c19-135">XML biçimli yapılandırma kaynaklarını dizilerde çalışmak için sağlayan bir `name` her öğenin dizini.</span><span class="sxs-lookup"><span data-stu-id="f7c19-135">To work with arrays in XML-formatted configuration sources, provide a `name` index to each element.</span></span> <span data-ttu-id="f7c19-136">Dizin değerlerine erişmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="f7c19-136">Use the index to access the values:</span></span>

```xml
<wizards>
  <wizard name="Gandalf">
    <age>1000</age>
  </wizard>
  <wizard name="Harry">
    <age>17</age>
  </wizard>
</wizards>
```

```csharp
Console.Write($"{Configuration["wizard:Harry:age"]}");
// Output: 17
```

## <a name="configuration-by-environment"></a><span data-ttu-id="f7c19-137">Ortam yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f7c19-137">Configuration by environment</span></span>

<span data-ttu-id="f7c19-138">Örneğin, geliştirme, test ve üretim farklı ortamlar için farklı yapılandırma ayarlarını sağlamak için genel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="f7c19-138">It's typical to have different configuration settings for different environments, for example, development, testing, and production.</span></span> <span data-ttu-id="f7c19-139">`CreateDefaultBuilder` Bir ASP.NET Core 2.x uygulamasında genişletme yöntemi (veya kullanarak `AddJsonFile` ve `AddEnvironmentVariables` bir ASP.NET Core 1.x uygulamasındaki doğrudan) JSON dosyaları ve sistem yapılandırma kaynaklarını okumak için bir yapılandırma sağlayıcısı ekler:</span><span class="sxs-lookup"><span data-stu-id="f7c19-139">The `CreateDefaultBuilder` extension method in an ASP.NET Core 2.x app (or using `AddJsonFile` and `AddEnvironmentVariables` directly in an ASP.NET Core 1.x app) adds configuration providers for reading JSON files and system configuration sources:</span></span>

* <span data-ttu-id="f7c19-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="f7c19-140">*appsettings.json*</span></span>
* <span data-ttu-id="f7c19-141">*appSettings. \<EnvironmentName > .json*</span><span class="sxs-lookup"><span data-stu-id="f7c19-141">*appsettings.\<EnvironmentName>.json*</span></span>
* <span data-ttu-id="f7c19-142">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="f7c19-142">Environment variables</span></span>

<span data-ttu-id="f7c19-143">ASP.NET Core 1.x uygulamalarına ihtiyacı çağırmak `AddJsonFile` ve [AddEnvironmentVariables](/dotnet/api/microsoft.extensions.configuration.environmentvariablesextensions.addenvironmentvariables#Microsoft_Extensions_Configuration_EnvironmentVariablesExtensions_AddEnvironmentVariables_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_).</span><span class="sxs-lookup"><span data-stu-id="f7c19-143">ASP.NET Core 1.x apps need to call `AddJsonFile` and [AddEnvironmentVariables](/dotnet/api/microsoft.extensions.configuration.environmentvariablesextensions.addenvironmentvariables#Microsoft_Extensions_Configuration_EnvironmentVariablesExtensions_AddEnvironmentVariables_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_).</span></span>

<span data-ttu-id="f7c19-144">Bkz: [AddJsonFile](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions) için bir açıklama parametreleri.</span><span class="sxs-lookup"><span data-stu-id="f7c19-144">See [AddJsonFile](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions) for an explanation of the parameters.</span></span> <span data-ttu-id="f7c19-145">`reloadOnChange` yalnızca ASP.NET Core 1.1 ve sonraki sürümlerinde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-145">`reloadOnChange` is only supported in ASP.NET Core 1.1 and later.</span></span>

<span data-ttu-id="f7c19-146">Yapılandırma kaynaklarını belirtilen sırada salt okunurdur.</span><span class="sxs-lookup"><span data-stu-id="f7c19-146">Configuration sources are read in the order that they're specified.</span></span> <span data-ttu-id="f7c19-147">Önceki kod, ortam değişkenleri son salt okunurdur.</span><span class="sxs-lookup"><span data-stu-id="f7c19-147">In the preceding code, the environment variables are read last.</span></span> <span data-ttu-id="f7c19-148">Herhangi bir yapılandırma değeri ortamı Değiştir iki önceki sağlayıcıları belirlenen ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f7c19-148">Any configuration values set through the environment replace those set in the two previous providers.</span></span>

<span data-ttu-id="f7c19-149">Aşağıdakileri göz önünde bulundurun *appsettings. Staging.JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f7c19-149">Consider the following *appsettings.Staging.json* file:</span></span>

[!code-json[](index/sample/appsettings.Staging.json)]

<span data-ttu-id="f7c19-150">Ortam ayarlandığında `Staging`, aşağıdaki `Configure` yöntemi okur değerini `MyConfig`:</span><span class="sxs-lookup"><span data-stu-id="f7c19-150">When the environment is set to `Staging`, the following `Configure` method reads the value of `MyConfig`:</span></span>

[!code-csharp[](index/sample/StartupConfig.cs?name=snippet&highlight=3,4)]

<span data-ttu-id="f7c19-151">Ortam genellikle ayarlamak `Development`, `Staging`, veya `Production`.</span><span class="sxs-lookup"><span data-stu-id="f7c19-151">The environment is typically set to `Development`, `Staging`, or `Production`.</span></span> <span data-ttu-id="f7c19-152">Daha fazla bilgi için bkz: [kullanan birden çok ortamlar](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f7c19-152">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f7c19-153">Yapılandırma dikkate alınacak noktalar:</span><span class="sxs-lookup"><span data-stu-id="f7c19-153">Configuration considerations:</span></span>

* <span data-ttu-id="f7c19-154">[IOptionsSnapshot](xref:fundamentals/configuration/options#reload-configuration-data-with-ioptionssnapshot) değişiklik yapıldığında yapılandırma verileri yeniden yükleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f7c19-154">[IOptionsSnapshot](xref:fundamentals/configuration/options#reload-configuration-data-with-ioptionssnapshot) can reload configuration data when it changes.</span></span>
* <span data-ttu-id="f7c19-155">Yapılandırma anahtarları **değil** büyük küçük harfe duyarlı.</span><span class="sxs-lookup"><span data-stu-id="f7c19-155">Configuration keys are **not** case-sensitive.</span></span>
* <span data-ttu-id="f7c19-156">**Hiçbir zaman** parolalar ve diğer hassas verileri yapılandırma sağlayıcısı kodu veya düz metin yapılandırma dosyalarını depolar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-156">**Never** store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="f7c19-157">Verme geliştirme üretim gizlilikleri kullanın veya sınama ortamlarında.</span><span class="sxs-lookup"><span data-stu-id="f7c19-157">Don't use production secrets in development or test environments.</span></span> <span data-ttu-id="f7c19-158">Böylece, bir kaynak kod deposuna yanlışlıkla uygulanamıyor gizli proje dışında belirtin.</span><span class="sxs-lookup"><span data-stu-id="f7c19-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span> <span data-ttu-id="f7c19-159">Daha fazla bilgi edinmek [birden çok ortamı kullanmayı](xref:fundamentals/environments) ve yönetme [uygulama sırrı geliştirme güvenli depolama](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f7c19-159">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing [safe storage of app secrets in development](xref:security/app-secrets).</span></span>
* <span data-ttu-id="f7c19-160">Ortam değişkenleri, iki nokta belirtilen hiyerarşik yapılandırma değerleri için (`:`) tüm platformlarda çalışmayabilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-160">For hierarchical config values specified in environment variables, a colon (`:`) may not work on all platforms.</span></span> <span data-ttu-id="f7c19-161">Çift alt çizgi (`__`) tüm platformlar tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-161">Double underscore (`__`) is supported by all platforms.</span></span>
* <span data-ttu-id="f7c19-162">Yapılandırma API'si, iki nokta kullanılırken (`:`) tüm platformlarda çalışır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-162">When interacting with the configuration API, a colon (`:`) works on all platforms.</span></span>

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a><span data-ttu-id="f7c19-163">Bellek içi sağlayıcısı ve bağlama için bir POCO sınıfı</span><span class="sxs-lookup"><span data-stu-id="f7c19-163">In-memory provider and binding to a POCO class</span></span>

<span data-ttu-id="f7c19-164">Aşağıdaki örnek, bellek içi Sağlayıcısı'nı kullanın ve bir sınıfa bağlamak gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f7c19-164">The following sample shows how to use the in-memory provider and bind to a class:</span></span>

[!code-csharp[](index/sample/InMemory/Program.cs)]

<span data-ttu-id="f7c19-165">Yapılandırma değerleri dize olarak döndürülür, ancak bağlama nesnelerin yapımı sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-165">Configuration values are returned as strings, but binding enables the construction of objects.</span></span> <span data-ttu-id="f7c19-166">Bağlama POCO nesneleri veya hatta tüm nesne grafiklerinin alınmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-166">Binding allows the retrieval of POCO objects or even entire object graphs.</span></span>

### <a name="getvalue"></a><span data-ttu-id="f7c19-167">GetValue</span><span class="sxs-lookup"><span data-stu-id="f7c19-167">GetValue</span></span>

<span data-ttu-id="f7c19-168">Aşağıdaki örnek gösterilmektedir [GetValue&lt;T&gt; ](/dotnet/api/microsoft.extensions.configuration.configurationbinder.get?view=aspnetcore-2.0#Microsoft_Extensions_Configuration_ConfigurationBinder_Get__1_Microsoft_Extensions_Configuration_IConfiguration_) genişletme yöntemi:</span><span class="sxs-lookup"><span data-stu-id="f7c19-168">The following sample demonstrates the [GetValue&lt;T&gt;](/dotnet/api/microsoft.extensions.configuration.configurationbinder.get?view=aspnetcore-2.0#Microsoft_Extensions_Configuration_ConfigurationBinder_Get__1_Microsoft_Extensions_Configuration_IConfiguration_) extension method:</span></span>

[!code-csharp[](index/sample/InMemoryGetValue/Program.cs?highlight=31)]

<span data-ttu-id="f7c19-169">ConfigurationBinder's `GetValue<T>` yöntemi, varsayılan değer (80 örnekteki) belirtimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-169">The ConfigurationBinder's `GetValue<T>` method allows the specification of a default value (80 in the sample).</span></span> <span data-ttu-id="f7c19-170">`GetValue<T>` Basit senaryolar için ve tüm bölümleri bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="f7c19-170">`GetValue<T>` is for simple scenarios and doesn't bind to entire sections.</span></span> <span data-ttu-id="f7c19-171">`GetValue<T>` skaler değerleri alır `GetSection(key).Value` belirli bir türüne dönüştürülemiyor.</span><span class="sxs-lookup"><span data-stu-id="f7c19-171">`GetValue<T>` obtains scalar values from `GetSection(key).Value` converted to a specific type.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="f7c19-172">Bir nesne grafiğinin bağlama</span><span class="sxs-lookup"><span data-stu-id="f7c19-172">Bind to an object graph</span></span>

<span data-ttu-id="f7c19-173">Bir sınıftaki her nesneyi yinelemeli olarak bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-173">Each object in a class can be recursively bound.</span></span> <span data-ttu-id="f7c19-174">Aşağıdakileri göz önünde bulundurun `AppSettings` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="f7c19-174">Consider the following `AppSettings` class:</span></span>

[!code-csharp[](index/sample/ObjectGraph/AppSettings.cs)]

<span data-ttu-id="f7c19-175">Aşağıdaki örnek bağlar `AppSettings` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="f7c19-175">The following sample binds to the `AppSettings` class:</span></span>

[!code-csharp[](index/sample/ObjectGraph/Program.cs?highlight=15-16)]

<span data-ttu-id="f7c19-176">**ASP.NET Core 1.1** ve daha yüksek kullanabilirsiniz `Get<T>`, tüm bölümleri ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-176">**ASP.NET Core 1.1** and higher can use `Get<T>`, which works with entire sections.</span></span> <span data-ttu-id="f7c19-177">`Get<T>` kullanmaktan daha kullanışlı olabilir `Bind`.</span><span class="sxs-lookup"><span data-stu-id="f7c19-177">`Get<T>` can be more convenient than using `Bind`.</span></span> <span data-ttu-id="f7c19-178">Aşağıdaki kodu nasıl kullanılacağını gösterir `Get<T>` önceki örnekle:</span><span class="sxs-lookup"><span data-stu-id="f7c19-178">The following code shows how to use `Get<T>` with the preceding sample:</span></span>

```csharp
var appConfig = config.GetSection("App").Get<AppSettings>();
```

<span data-ttu-id="f7c19-179">Aşağıdakileri kullanarak *appsettings.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f7c19-179">Using the following *appsettings.json* file:</span></span>

[!code-json[](index/sample/ObjectGraph/appsettings.json)]

<span data-ttu-id="f7c19-180">Program görüntüler `Height 11`.</span><span class="sxs-lookup"><span data-stu-id="f7c19-180">The program displays `Height 11`.</span></span>

<span data-ttu-id="f7c19-181">Aşağıdaki kod birimine kullanılabilir test yapılandırması:</span><span class="sxs-lookup"><span data-stu-id="f7c19-181">The following code can be used to unit test the configuration:</span></span>

```csharp
[Fact]
public void CanBindObjectTree()
{
    var dict = new Dictionary<string, string>
        {
            {"App:Profile:Machine", "Rick"},
            {"App:Connection:Value", "connectionstring"},
            {"App:Window:Height", "11"},
            {"App:Window:Width", "11"}
        };
    var builder = new ConfigurationBuilder();
    builder.AddInMemoryCollection(dict);
    var config = builder.Build();

    var settings = new AppSettings();
    config.GetSection("App").Bind(settings);

    Assert.Equal("Rick", settings.Profile.Machine);
    Assert.Equal(11, settings.Window.Height);
    Assert.Equal(11, settings.Window.Width);
    Assert.Equal("connectionstring", settings.Connection.Value);
}
```

<a name="custom-config-providers"></a>

## <a name="create-an-entity-framework-custom-provider"></a><span data-ttu-id="f7c19-182">Entity Framework özel sağlayıcı oluşturma</span><span class="sxs-lookup"><span data-stu-id="f7c19-182">Create an Entity Framework custom provider</span></span>

<span data-ttu-id="f7c19-183">Bu bölümde, ad-değer çiftleri EF kullanan bir veritabanından okur bir temel yapılandırma sağlayıcısı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f7c19-183">In this section, a basic configuration provider that reads name-value pairs from a database using EF is created.</span></span>

<span data-ttu-id="f7c19-184">Tanımlayan bir `ConfigurationValue` yapılandırma değerlerini veritabanında depolamak için varlık:</span><span class="sxs-lookup"><span data-stu-id="f7c19-184">Define a `ConfigurationValue` entity for storing configuration values in the database:</span></span>

[!code-csharp[](index/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

<span data-ttu-id="f7c19-185">Ekleme bir `ConfigurationContext` depolamak ve yapılandırılmış değerlerine erişmek için:</span><span class="sxs-lookup"><span data-stu-id="f7c19-185">Add a `ConfigurationContext` to store and access the configured values:</span></span>

[!code-csharp[](index/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="f7c19-186">Arabirimini uygulayan bir sınıf oluşturun [IConfigurationSource](/dotnet/api/Microsoft.Extensions.Configuration.IConfigurationSource):</span><span class="sxs-lookup"><span data-stu-id="f7c19-186">Create a class that implements [IConfigurationSource](/dotnet/api/Microsoft.Extensions.Configuration.IConfigurationSource):</span></span>

[!code-csharp[](index/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

<span data-ttu-id="f7c19-187">Özel yapılandırma sağlayıcısının devralarak oluşturma [ConfigurationProvider](/dotnet/api/Microsoft.Extensions.Configuration.ConfigurationProvider).</span><span class="sxs-lookup"><span data-stu-id="f7c19-187">Create the custom configuration provider by inheriting from [ConfigurationProvider](/dotnet/api/Microsoft.Extensions.Configuration.ConfigurationProvider).</span></span> <span data-ttu-id="f7c19-188">Boş olduğunda yapılandırma sağlayıcısı veritabanı başlatır:</span><span class="sxs-lookup"><span data-stu-id="f7c19-188">The configuration provider initializes the database when it's empty:</span></span>

[!code-csharp[](index/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

<span data-ttu-id="f7c19-189">Örneği çalıştırdığınızda ("value_from_ef_1" ve "value_from_ef_2") veritabanından vurgulanan değerler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-189">The highlighted values from the database ("value_from_ef_1" and "value_from_ef_2") are displayed when the sample is run.</span></span>

<span data-ttu-id="f7c19-190">Bir `EFConfigSource` genişletme yöntemi yapılandırma kaynağı eklemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f7c19-190">An `EFConfigSource` extension method for adding the configuration source can be used:</span></span>

[!code-csharp[](index/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

<span data-ttu-id="f7c19-191">Aşağıdaki kod özel kullanmayı gösterir `EFConfigProvider`:</span><span class="sxs-lookup"><span data-stu-id="f7c19-191">The following code shows how to use the custom `EFConfigProvider`:</span></span>

[!code-csharp[](index/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

<span data-ttu-id="f7c19-192">Örnek ekler özel Not `EFConfigProvider` JSON sağlayıcısı sonra bu nedenle veritabanından herhangi bir ayarı geçersiz kılar ayarlarından *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="f7c19-192">Note the sample adds the custom `EFConfigProvider` after the JSON provider, so any settings from the database will override settings from the *appsettings.json* file.</span></span>

<span data-ttu-id="f7c19-193">Aşağıdakileri kullanarak *appsettings.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f7c19-193">Using the following *appsettings.json* file:</span></span>

[!code-json[](index/sample/CustomConfigurationProvider/appsettings.json)]

<span data-ttu-id="f7c19-194">Şu çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="f7c19-194">The following output is displayed:</span></span>

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a><span data-ttu-id="f7c19-195">Komut satırı yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f7c19-195">CommandLine configuration provider</span></span>

<span data-ttu-id="f7c19-196">[CommandLine yapılandırma sağlayıcısı](/dotnet/api/microsoft.extensions.configuration.commandline.commandlineconfigurationprovider) çalışma zamanında yapılandırması için komut satırı bağımsız değişkeni anahtar-değer çiftleri alır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-196">The [CommandLine configuration provider](/dotnet/api/microsoft.extensions.configuration.commandline.commandlineconfigurationprovider) receives command-line argument key-value pairs for configuration at runtime.</span></span>

[<span data-ttu-id="f7c19-197">Görüntülemek veya komut satırı yapılandırma örneği indirin</span><span class="sxs-lookup"><span data-stu-id="f7c19-197">View or download the CommandLine configuration sample</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample/CommandLine)

### <a name="setup-and-use-the-commandline-configuration-provider"></a><span data-ttu-id="f7c19-198">Kurulum ve komut satırı yapılandırma sağlayıcısı kullanın</span><span class="sxs-lookup"><span data-stu-id="f7c19-198">Setup and use the CommandLine configuration provider</span></span>

# <a name="basic-configurationtabbasicconfiguration"></a>[<span data-ttu-id="f7c19-199">Temel yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f7c19-199">Basic Configuration</span></span>](#tab/basicconfiguration/)

<span data-ttu-id="f7c19-200">Komut satırı yapılandırmasını etkinleştirmek için arama `AddCommandLine` genişletme yöntemi örneği üzerinde [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder):</span><span class="sxs-lookup"><span data-stu-id="f7c19-200">To activate command-line configuration, call the `AddCommandLine` extension method on an instance of [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder):</span></span>

[!code-csharp[](index/sample_snapshot//CommandLine/Program.cs?highlight=18,21)]

<span data-ttu-id="f7c19-201">Kod çalıştırmadan, aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="f7c19-201">Running the code, the following output is displayed:</span></span>

```console
MachineName: MairaPC
Left: 1980
```

<span data-ttu-id="f7c19-202">Komut satırında anahtar-değer çiftleri bağımsız değişken geçirme değerlerini değiştirir `Profile:MachineName` ve `App:MainWindow:Left`:</span><span class="sxs-lookup"><span data-stu-id="f7c19-202">Passing argument key-value pairs on the command line changes the values of `Profile:MachineName` and `App:MainWindow:Left`:</span></span>

```console
dotnet run Profile:MachineName=BartPC App:MainWindow:Left=1979
```

<span data-ttu-id="f7c19-203">Konsol penceresinde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="f7c19-203">The console window displays:</span></span>

```console
MachineName: BartPC
Left: 1979
```

<span data-ttu-id="f7c19-204">Komut satırı yapılandırması ile diğer yapılandırma sağlayıcıları tarafından sağlanan yapılandırma geçersiz kılmak için arama `AddCommandLine` üzerinde son `ConfigurationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="f7c19-204">To override configuration provided by other configuration providers with command-line configuration, call `AddCommandLine` last on `ConfigurationBuilder`:</span></span>

[!code-csharp[](index/sample_snapshot//CommandLine/Program2.cs?range=11-16&highlight=1,5)]

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f7c19-205">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f7c19-205">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="f7c19-206">Tipik ASP.NET Core 2.x uygulamaları kullanma statik kolaylık metodunun `CreateDefaultBuilder` konak oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="f7c19-206">Typical ASP.NET Core 2.x apps use the static convenience method `CreateDefaultBuilder` to build the host:</span></span>

[!code-csharp[](index/sample_snapshot//Program.cs?highlight=12)]

<span data-ttu-id="f7c19-207">`CreateDefaultBuilder` İsteğe bağlı yapılandırmasından yükler *appsettings.json*, *appsettings. { Ortam} .json*, [kullanıcı parolaları](xref:security/app-secrets) (içinde `Development` ortamı), ortam değişkenleri ve komut satırı bağımsız değişkenleri.</span><span class="sxs-lookup"><span data-stu-id="f7c19-207">`CreateDefaultBuilder` loads optional configuration from *appsettings.json*, *appsettings.{Environment}.json*, [user secrets](xref:security/app-secrets) (in the `Development` environment), environment variables, and command-line arguments.</span></span> <span data-ttu-id="f7c19-208">Komut satırı yapılandırma sağlayıcısı son çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-208">The CommandLine configuration provider is called last.</span></span> <span data-ttu-id="f7c19-209">Sağlayıcı son çağırma yapılandırması bir yapılandırma sağlayıcıları tarafından ayarlanmış geçersiz kılmak için çalışma zamanında geçirilen komut satırı bağımsız değişkenleri önceki adlı sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-209">Calling the provider last allows the command-line arguments passed at runtime to override configuration set by the other configuration providers called earlier.</span></span>

<span data-ttu-id="f7c19-210">İçin *appsettings* where dosyaları:</span><span class="sxs-lookup"><span data-stu-id="f7c19-210">For *appsettings* files where:</span></span>

* <span data-ttu-id="f7c19-211">`reloadOnChange` etkin.</span><span class="sxs-lookup"><span data-stu-id="f7c19-211">`reloadOnChange` is enabled.</span></span>
* <span data-ttu-id="f7c19-212">Komut satırı bağımsız değişkenleri aynı ayar içerir ve bir *appsettings* dosya.</span><span class="sxs-lookup"><span data-stu-id="f7c19-212">Contain the same setting in the command-line arguments and an *appsettings* file.</span></span>
* <span data-ttu-id="f7c19-213">*Appsettings* eşleşen komut satırı bağımsız değişkeni içeren bir dosya, uygulama başladıktan sonra değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-213">The *appsettings* file containing the matching command-line argument is changed after the app starts.</span></span>

<span data-ttu-id="f7c19-214">Önceki koşulların tümü doğruysa, komut satırı bağımsız değişkenleri geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-214">If all the preceding conditions are true, the command-line arguments are overridden.</span></span>

<span data-ttu-id="f7c19-215">ASP.NET Core 2.x uygulama kullanabileceğiniz [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) yerine `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f7c19-215">ASP.NET Core 2.x app can use [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) instead of `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f7c19-216">Kullanırken `WebHostBuilder`, el ile kümesi yapılandırması ile [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder).</span><span class="sxs-lookup"><span data-stu-id="f7c19-216">When using `WebHostBuilder`, manually set configuration with [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder).</span></span> <span data-ttu-id="f7c19-217">Daha fazla bilgi için ASP.NET Core 1.x sekmesine bakın.</span><span class="sxs-lookup"><span data-stu-id="f7c19-217">See the ASP.NET Core 1.x tab for more information.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f7c19-218">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f7c19-218">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="f7c19-219">Oluşturma bir [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) ve arama `AddCommandLine` yöntemi CommandLine yapılandırma sağlayıcısı kullanın.</span><span class="sxs-lookup"><span data-stu-id="f7c19-219">Create a [ConfigurationBuilder](/api/microsoft.extensions.configuration.configurationbuilder) and call the `AddCommandLine` method to use the CommandLine configuration provider.</span></span> <span data-ttu-id="f7c19-220">Sağlayıcı son çağırma yapılandırması bir yapılandırma sağlayıcıları tarafından ayarlanmış geçersiz kılmak için çalışma zamanında geçirilen komut satırı bağımsız değişkenleri önceki adlı sağlar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-220">Calling the provider last allows the command-line arguments passed at runtime to override configuration set by the other configuration providers called earlier.</span></span> <span data-ttu-id="f7c19-221">Yapılandırmasını uygulamak [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) ile `UseConfiguration` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="f7c19-221">Apply the configuration to [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) with the `UseConfiguration` method:</span></span>

[!code-csharp[](index/sample_snapshot//CommandLine/Program2.cs?highlight=11,15,19)]

---

### <a name="arguments"></a><span data-ttu-id="f7c19-222">Arguments</span><span class="sxs-lookup"><span data-stu-id="f7c19-222">Arguments</span></span>

<span data-ttu-id="f7c19-223">Komut satırına geçirilen bağımsız değişkenler aşağıdaki tabloda gösterilen iki biçim birine uymalıdır:</span><span class="sxs-lookup"><span data-stu-id="f7c19-223">Arguments passed on the command line must conform to one of two formats shown in the following table:</span></span>

| <span data-ttu-id="f7c19-224">Bağımsız değişken biçimi</span><span class="sxs-lookup"><span data-stu-id="f7c19-224">Argument format</span></span>                                                     | <span data-ttu-id="f7c19-225">Örnek</span><span class="sxs-lookup"><span data-stu-id="f7c19-225">Example</span></span>        |
| ------------------------------------------------------------------- | :------------: |
| <span data-ttu-id="f7c19-226">Tek bağımsız değişken: bir anahtar-değer çifti ayrılmış bir eşittir işareti (`=`)</span><span class="sxs-lookup"><span data-stu-id="f7c19-226">Single argument: a key-value pair separated by an equals sign (`=`)</span></span> | `key1=value`   |
| <span data-ttu-id="f7c19-227">İki bağımsız değişken bir dizi: boşlukla ayrılmış bir anahtar-değer çifti</span><span class="sxs-lookup"><span data-stu-id="f7c19-227">Sequence of two arguments: a key-value pair separated by a space</span></span>    | `/key1 value1` |

<span data-ttu-id="f7c19-228">**Tek bir bağımsız değişken**</span><span class="sxs-lookup"><span data-stu-id="f7c19-228">**Single argument**</span></span>

<span data-ttu-id="f7c19-229">Değer bir eşittir işareti gelmelidir (`=`).</span><span class="sxs-lookup"><span data-stu-id="f7c19-229">The value must follow an equals sign (`=`).</span></span> <span data-ttu-id="f7c19-230">Değer null olabilir (örneğin, `mykey=`).</span><span class="sxs-lookup"><span data-stu-id="f7c19-230">The value can be null (for example, `mykey=`).</span></span>

<span data-ttu-id="f7c19-231">Anahtar bir önek olabilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-231">The key may have a prefix.</span></span>

| <span data-ttu-id="f7c19-232">Anahtar öneki</span><span class="sxs-lookup"><span data-stu-id="f7c19-232">Key prefix</span></span>               | <span data-ttu-id="f7c19-233">Örnek</span><span class="sxs-lookup"><span data-stu-id="f7c19-233">Example</span></span>         |
| ------------------------ | :-------------: |
| <span data-ttu-id="f7c19-234">Önek</span><span class="sxs-lookup"><span data-stu-id="f7c19-234">No prefix</span></span>                | `key1=value1`   |
| <span data-ttu-id="f7c19-235">Tek bir tire (`-`)&#8224;</span><span class="sxs-lookup"><span data-stu-id="f7c19-235">Single dash (`-`)&#8224;</span></span> | `-key2=value2`  |
| <span data-ttu-id="f7c19-236">İki kısa çizgi (`--`)</span><span class="sxs-lookup"><span data-stu-id="f7c19-236">Two dashes (`--`)</span></span>        | `--key3=value3` |
| <span data-ttu-id="f7c19-237">Eğik çizgi (`/`)</span><span class="sxs-lookup"><span data-stu-id="f7c19-237">Forward slash (`/`)</span></span>      | `/key4=value4`  |

<span data-ttu-id="f7c19-238">&#8224;Tek tire öneke sahip bir anahtar (`-`) içinde sağlanmalıdır [geçiş eşlemeleri](#switch-mappings), aşağıda açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-238">&#8224;A key with a single dash prefix (`-`) must be provided in [switch mappings](#switch-mappings), described below.</span></span>

<span data-ttu-id="f7c19-239">Örnek komut:</span><span class="sxs-lookup"><span data-stu-id="f7c19-239">Example command:</span></span>

```console
dotnet run key1=value1 -key2=value2 --key3=value3 /key4=value4
```

<span data-ttu-id="f7c19-240">Not: Varsa `-key2` mevcut değilse [geçiş eşlemeleri](#switch-mappings) yapılandırma sağlayıcısı için verilen bir `FormatException` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f7c19-240">Note: If `-key2` isn't present in the [switch mappings](#switch-mappings) given to the configuration provider, a `FormatException` is thrown.</span></span>

<span data-ttu-id="f7c19-241">**İki bağımsız değişken dizisi**</span><span class="sxs-lookup"><span data-stu-id="f7c19-241">**Sequence of two arguments**</span></span>

<span data-ttu-id="f7c19-242">Değer null olamaz ve boşlukla ayrılmış anahtar izlemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-242">The value can't be null and must follow the key separated by a space.</span></span>

<span data-ttu-id="f7c19-243">Anahtar bir önekine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-243">The key must have a prefix.</span></span>

| <span data-ttu-id="f7c19-244">Anahtar öneki</span><span class="sxs-lookup"><span data-stu-id="f7c19-244">Key prefix</span></span>               | <span data-ttu-id="f7c19-245">Örnek</span><span class="sxs-lookup"><span data-stu-id="f7c19-245">Example</span></span>         |
| ------------------------ | :-------------: |
| <span data-ttu-id="f7c19-246">Tek bir tire (`-`)&#8224;</span><span class="sxs-lookup"><span data-stu-id="f7c19-246">Single dash (`-`)&#8224;</span></span> | `-key1 value1`  |
| <span data-ttu-id="f7c19-247">İki kısa çizgi (`--`)</span><span class="sxs-lookup"><span data-stu-id="f7c19-247">Two dashes (`--`)</span></span>        | `--key2 value2` |
| <span data-ttu-id="f7c19-248">Eğik çizgi (`/`)</span><span class="sxs-lookup"><span data-stu-id="f7c19-248">Forward slash (`/`)</span></span>      | `/key3 value3`  |

<span data-ttu-id="f7c19-249">&#8224;Tek tire öneke sahip bir anahtar (`-`) içinde sağlanmalıdır [geçiş eşlemeleri](#switch-mappings), aşağıda açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-249">&#8224;A key with a single dash prefix (`-`) must be provided in [switch mappings](#switch-mappings), described below.</span></span>

<span data-ttu-id="f7c19-250">Örnek komut:</span><span class="sxs-lookup"><span data-stu-id="f7c19-250">Example command:</span></span>

```console
dotnet run -key1 value1 --key2 value2 /key3 value3
```

<span data-ttu-id="f7c19-251">Not: Varsa `-key1` mevcut değilse [geçiş eşlemeleri](#switch-mappings) yapılandırma sağlayıcısı için verilen bir `FormatException` oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f7c19-251">Note: If `-key1` isn't present in the [switch mappings](#switch-mappings) given to the configuration provider, a `FormatException` is thrown.</span></span>

### <a name="duplicate-keys"></a><span data-ttu-id="f7c19-252">Yinelenen anahtarlar</span><span class="sxs-lookup"><span data-stu-id="f7c19-252">Duplicate keys</span></span>

<span data-ttu-id="f7c19-253">Yinelenen anahtarlarla verdiyse, son anahtar-değer çifti kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-253">If duplicate keys are provided, the last key-value pair is used.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="f7c19-254">Geçiş eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="f7c19-254">Switch mappings</span></span>

<span data-ttu-id="f7c19-255">El ile yapılandırma ile oluştururken `ConfigurationBuilder`, bir anahtar eşlemeleri sözlüğü eklenebilir `AddCommandLine` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="f7c19-255">When manually building configuration with `ConfigurationBuilder`, a switch mappings dictionary can be added to the `AddCommandLine` method.</span></span> <span data-ttu-id="f7c19-256">Anahtar eşlemeleri anahtar adı değiştirme mantığı izin verir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-256">Switch mappings allow key name replacement logic.</span></span>

<span data-ttu-id="f7c19-257">Anahtar eşlemeleri sözlüğü kullanıldığında, sözlük komut satırı bağımsız değişkeni tarafından sağlanan anahtarıyla eşleşen bir anahtarı için denetlenir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-257">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="f7c19-258">Komut satırı anahtarı sözlük içinde bulunursa, sözlük değeri (Anahtar değişimini) yapılandırma döndürülmek geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-258">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the configuration.</span></span> <span data-ttu-id="f7c19-259">Tek bir çizgiyle önekli herhangi bir komut satırı anahtarı için bir anahtar eşlemesi gereklidir (`-`).</span><span class="sxs-lookup"><span data-stu-id="f7c19-259">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="f7c19-260">Eşlemeleri sözlüğü anahtar kuralları anahtarı:</span><span class="sxs-lookup"><span data-stu-id="f7c19-260">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="f7c19-261">Anahtarları kısa çizgiyle başlamalıdır (`-`) veya çift tire (`--`).</span><span class="sxs-lookup"><span data-stu-id="f7c19-261">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="f7c19-262">Anahtar eşlemeleri sözlüğü yinelenen anahtarlar içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-262">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="f7c19-263">Aşağıdaki örnekte, `GetSwitchMappings` yöntemi sağlayan tek bir çizgi kullanmak komut satırı bağımsız değişkenleri (`-`) anahtarı öneki ve önde gelen alt anahtar önekleri kaçının.</span><span class="sxs-lookup"><span data-stu-id="f7c19-263">In the following example, the `GetSwitchMappings` method allows command-line arguments to use a single dash (`-`) key prefix and avoid leading subkey prefixes.</span></span>

[!code-csharp[](index/sample/CommandLine/Program.cs?highlight=10-19,32)]

<span data-ttu-id="f7c19-264">Komut satırı bağımsız değişkenleri sağlamadan sözlüğü için sağlanan `AddInMemoryCollection` yapılandırma değerlerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f7c19-264">Without providing command-line arguments, the dictionary provided to `AddInMemoryCollection` sets the configuration values.</span></span> <span data-ttu-id="f7c19-265">Uygulama ile aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f7c19-265">Run the app with the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="f7c19-266">Konsol penceresinde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="f7c19-266">The console window displays:</span></span>

```console
MachineName: RickPC
Left: 1980
```

<span data-ttu-id="f7c19-267">Yapılandırma ayarlarını geçirmek için aşağıdakileri kullanın:</span><span class="sxs-lookup"><span data-stu-id="f7c19-267">Use the following to pass in configuration settings:</span></span>

```console
dotnet run /Profile:MachineName=DahliaPC /App:MainWindow:Left=1984
```

<span data-ttu-id="f7c19-268">Konsol penceresinde görüntüler:</span><span class="sxs-lookup"><span data-stu-id="f7c19-268">The console window displays:</span></span>

```console
MachineName: DahliaPC
Left: 1984
```

<span data-ttu-id="f7c19-269">Anahtar eşlemeleri sözlüğü oluşturulduktan sonra aşağıdaki tabloda gösterilen veriler içerir:</span><span class="sxs-lookup"><span data-stu-id="f7c19-269">After the switch mappings dictionary is created, it contains the data shown in the following table:</span></span>

| <span data-ttu-id="f7c19-270">Anahtar</span><span class="sxs-lookup"><span data-stu-id="f7c19-270">Key</span></span>            | <span data-ttu-id="f7c19-271">Değer</span><span class="sxs-lookup"><span data-stu-id="f7c19-271">Value</span></span>                 |
| -------------- | --------------------- |
| `-MachineName` | `Profile:MachineName` |
| `-Left`        | `App:MainWindow:Left` |

<span data-ttu-id="f7c19-272">Anahtar geçişi sözlüğünü kullanarak göstermek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f7c19-272">To demonstrate key switching using the dictionary, run the following command:</span></span>

```console
dotnet run -MachineName=ChadPC -Left=1988
```

<span data-ttu-id="f7c19-273">Komut satırı anahtarları değiştirilen.</span><span class="sxs-lookup"><span data-stu-id="f7c19-273">The command-line keys are swapped.</span></span> <span data-ttu-id="f7c19-274">Konsol penceresinde yapılandırma değerlerini görüntüler `Profile:MachineName` ve `App:MainWindow:Left`:</span><span class="sxs-lookup"><span data-stu-id="f7c19-274">The console window displays the configuration values for `Profile:MachineName` and `App:MainWindow:Left`:</span></span>

```console
MachineName: ChadPC
Left: 1988
```

## <a name="webconfig-file"></a><span data-ttu-id="f7c19-275">Web.config dosyası</span><span class="sxs-lookup"><span data-stu-id="f7c19-275">web.config file</span></span>

<span data-ttu-id="f7c19-276">A *web.config* dosya, IIS veya IIS Express uygulamasında barındırdığında gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-276">A *web.config* file is required when hosting the app in IIS or IIS Express.</span></span> <span data-ttu-id="f7c19-277">Ayarlarında *web.config* etkinleştirmek [ASP.NET Core Modülü](xref:fundamentals/servers/aspnet-core-module) uygulamayı başlatın ve diğer IIS ayarlarını ve modülleri yapılandırmak için.</span><span class="sxs-lookup"><span data-stu-id="f7c19-277">Settings in *web.config* enable the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to launch the app and configure other IIS settings and modules.</span></span> <span data-ttu-id="f7c19-278">Varsa *web.config* dosyası mevcut değil ve proje dosyasını içeren `<Project Sdk="Microsoft.NET.Sdk.Web">`, projeyi yayımlama oluşturur bir *web.config* yayımlanan çıktı dosyasında ( *Yayımlama* klasörü).</span><span class="sxs-lookup"><span data-stu-id="f7c19-278">If the *web.config* file isn't present and the project file includes `<Project Sdk="Microsoft.NET.Sdk.Web">`, publishing the project creates a *web.config* file in the published output (the *publish* folder).</span></span> <span data-ttu-id="f7c19-279">Daha fazla bilgi için bkz: [konak ASP.NET Core IIS ile Windows](xref:host-and-deploy/iis/index#webconfig-file).</span><span class="sxs-lookup"><span data-stu-id="f7c19-279">For more information, see [Host ASP.NET Core on Windows with IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span>

## <a name="access-configuration-during-startup"></a><span data-ttu-id="f7c19-280">Başlatma sırasında erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f7c19-280">Access configuration during startup</span></span>

<span data-ttu-id="f7c19-281">Erişim Yapılandırması içinde `ConfigureServices` veya `Configure` başlatma sırasında örneklere bakın [uygulama başlangıç](xref:fundamentals/startup) konu.</span><span class="sxs-lookup"><span data-stu-id="f7c19-281">To access configuration within `ConfigureServices` or `Configure` during startup, see the examples in the [Application startup](xref:fundamentals/startup) topic.</span></span>

## <a name="adding-configuration-from-an-external-assembly"></a><span data-ttu-id="f7c19-282">Bir dış derlemesinden ekleme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f7c19-282">Adding configuration from an external assembly</span></span>

<span data-ttu-id="f7c19-283">Bir [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) uygulama sağlayan uygulamanın dışında bir dış derlemesinden başlatma sırasında bir uygulama için geliştirmeler ekleme `Startup` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="f7c19-283">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="f7c19-284">Daha fazla bilgi için bkz: [dış bütünleştirilmiş uygulama geliştirmek](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="f7c19-284">For more information, see [Enhance an app from an external assembly](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="access-configuration-in-a-razor-page-or-mvc-view"></a><span data-ttu-id="f7c19-285">Bir Razor sayfasını veya MVC görünümündeki erişim yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f7c19-285">Access configuration in a Razor Page or MVC view</span></span>

<span data-ttu-id="f7c19-286">Bir Razor sayfalarının sayfası veya bir MVC görünümündeki yapılandırma ayarlarına erişmek için ekleme bir [using yönergesi](xref:mvc/views/razor#using) ([C# başvurusu: using yönergesi](/dotnet/csharp/language-reference/keywords/using-directive)) için [Microsoft.Extensions.Configuration ad alanı ](/dotnet/api/microsoft.extensions.configuration) ve ekleme [IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) sayfa ya da görünüm.</span><span class="sxs-lookup"><span data-stu-id="f7c19-286">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](/dotnet/api/microsoft.extensions.configuration) and inject [IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) into the page or view.</span></span>

<span data-ttu-id="f7c19-287">Bir Razor sayfalarının sayfasında:</span><span class="sxs-lookup"><span data-stu-id="f7c19-287">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel

@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration[&quot;key&quot;]: @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="f7c19-288">MVC görünümü içinde:</span><span class="sxs-lookup"><span data-stu-id="f7c19-288">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration[&quot;key&quot;]: @Configuration["key"]</p>
</body>
</html>
```

## <a name="additional-notes"></a><span data-ttu-id="f7c19-289">Ek Notlar</span><span class="sxs-lookup"><span data-stu-id="f7c19-289">Additional notes</span></span>

* <span data-ttu-id="f7c19-290">Bağımlılık ekleme (dı) değil ayarlamak kadar yukarı sonra `ConfigureServices` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-290">Dependency Injection (DI) isn't set up until after `ConfigureServices` is invoked.</span></span>
* <span data-ttu-id="f7c19-291">Yapılandırma sistemi dı farkında değildir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-291">The configuration system isn't DI aware.</span></span>
* <span data-ttu-id="f7c19-292">`IConfiguration` iki özelleştirmeleri sahiptir:</span><span class="sxs-lookup"><span data-stu-id="f7c19-292">`IConfiguration` has two specializations:</span></span>
  * <span data-ttu-id="f7c19-293">`IConfigurationRoot` Kök düğüm için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-293">`IConfigurationRoot` Used for the root node.</span></span> <span data-ttu-id="f7c19-294">Bir yeniden yükleme tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="f7c19-294">Can trigger a reload.</span></span>
  * <span data-ttu-id="f7c19-295">`IConfigurationSection` Yapılandırma değerlerini bir bölümünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f7c19-295">`IConfigurationSection` Represents a section of configuration values.</span></span> <span data-ttu-id="f7c19-296">`GetSection` Ve `GetChildren` yöntemleri döndürür bir `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="f7c19-296">The `GetSection` and `GetChildren` methods return an `IConfigurationSection`.</span></span>
  * <span data-ttu-id="f7c19-297">Kullanım [IConfigurationRoot](/dotnet/api/microsoft.extensions.configuration.iconfigurationroot) yapılandırma yeniden yükleme veya erişim her sağlayıcı için.</span><span class="sxs-lookup"><span data-stu-id="f7c19-297">Use [IConfigurationRoot](/dotnet/api/microsoft.extensions.configuration.iconfigurationroot) when reloading configuration or for access to each provider.</span></span> <span data-ttu-id="f7c19-298">Bunlardan hiçbirine yaygındır.</span><span class="sxs-lookup"><span data-stu-id="f7c19-298">Neither of these situations are common.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7c19-299">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f7c19-299">Additional resources</span></span>

* [<span data-ttu-id="f7c19-300">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="f7c19-300">Options</span></span>](xref:fundamentals/configuration/options)
* [<span data-ttu-id="f7c19-301">Birden çok ortam kullanma</span><span class="sxs-lookup"><span data-stu-id="f7c19-301">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="f7c19-302">Geliştirmede uygulama gizli anahtarlarının güvenli bir şekilde depolanması</span><span class="sxs-lookup"><span data-stu-id="f7c19-302">Safe storage of app secrets in development</span></span>](xref:security/app-secrets)
* [<span data-ttu-id="f7c19-303">ASP.NET Core ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="f7c19-303">Host in ASP.NET Core</span></span>](xref:fundamentals/host/index)
* [<span data-ttu-id="f7c19-304">Bağımlılık Ekleme</span><span class="sxs-lookup"><span data-stu-id="f7c19-304">Dependency Injection</span></span>](xref:fundamentals/dependency-injection)
* [<span data-ttu-id="f7c19-305">Azure Key Vault yapılandırma sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="f7c19-305">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
