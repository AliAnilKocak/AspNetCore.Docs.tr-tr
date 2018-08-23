---
title: ASP.NET Core desende seçenekleri
author: guardrex
description: ASP.NET Core uygulamalarında ilgili ayar gruplarını temsil etmek için seçenekleri deseni kullanmayı keşfedin.
ms.author: riande
ms.custom: mvc
ms.date: 11/28/2017
uid: fundamentals/configuration/options
ms.openlocfilehash: c553062bbec31ba5bd437eb0bd29e007ae93c65e
ms.sourcegitcommit: d53e0cc71542b92de867bcce51575b054886f529
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41756285"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="e2254-103">ASP.NET Core desende seçenekleri</span><span class="sxs-lookup"><span data-stu-id="e2254-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="e2254-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e2254-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e2254-105">Seçenekleri deseni sınıfları, ilgili ayar gruplarını temsil etmek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="e2254-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="e2254-106">Yapılandırma ayarları ayrı sınıflara özelliği tarafından ayrılan, uygulama için iki önemli yazılım Mühendisliği ilkeden uyar:</span><span class="sxs-lookup"><span data-stu-id="e2254-106">When configuration settings are isolated by feature into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="e2254-107">[Arabirimi ayırma ilkesi (ISS)](http://deviq.com/interface-segregation-principle/): yapılandırma ayarlarına bağlı olan özellikleri (sınıflar) kullandıkları yapılandırma ayarlarını bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e2254-107">The [Interface Segregation Principle (ISP)](http://deviq.com/interface-segregation-principle/): Features (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="e2254-108">[Görev ayrımı nettir](http://deviq.com/separation-of-concerns/): uygulamanın farklı kısımlarını ayarları bağımlı veya birbirine bağlı değil.</span><span class="sxs-lookup"><span data-stu-id="e2254-108">[Separation of Concerns](http://deviq.com/separation-of-concerns/): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="e2254-109">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample)) Bu makalede örnek uygulaması ile daha kolaydır.</span><span class="sxs-lookup"><span data-stu-id="e2254-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample)) This article is easier to follow with the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2254-110">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="e2254-110">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e2254-111">Başvuru [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) veya paket başvurusu ekleme [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paket.</span><span class="sxs-lookup"><span data-stu-id="e2254-111">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e2254-112">Başvuru [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) veya paket başvurusu ekleme [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paket.</span><span class="sxs-lookup"><span data-stu-id="e2254-112">Reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="e2254-113">Paket başvurusu ekleme [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) paket.</span><span class="sxs-lookup"><span data-stu-id="e2254-113">Add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

::: moniker-end

## <a name="basic-options-configuration"></a><span data-ttu-id="e2254-114">Temel Seçenekler yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e2254-114">Basic options configuration</span></span>

<span data-ttu-id="e2254-115">Temel Seçenekler yapılandırma, örnek olarak gösterilmiştir &num;1 [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="e2254-115">Basic options configuration is demonstrated as Example &num;1 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="e2254-116">Bir seçenek sınıfı soyut olmayan olmalıdır genel parametresiz oluşturucusu ile.</span><span class="sxs-lookup"><span data-stu-id="e2254-116">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="e2254-117">Aşağıdaki sınıf `MyOptions`, iki özelliğe sahiptir `Option1` ve `Option2`.</span><span class="sxs-lookup"><span data-stu-id="e2254-117">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="e2254-118">Varsayılan değerleri ayarlama, isteğe bağlıdır, ancak aşağıdaki örnekte sınıf oluşturucu varsayılan değerini ayarlar `Option1`.</span><span class="sxs-lookup"><span data-stu-id="e2254-118">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="e2254-119">`Option2` özelliği doğrudan başlatarak ayarlanmış varsayılan değerine sahip (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2254-119">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/sample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="e2254-120">`MyOptions` Sınıfı ile hizmet kapsayıcıya eklenir [yapılandırma&lt;TOptions&gt;(IServiceCollection, IConfiguration)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsconfigurationservicecollectionextensions.configure#Microsoft_Extensions_DependencyInjection_OptionsConfigurationServiceCollectionExtensions_Configure__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_Microsoft_Extensions_Configuration_IConfiguration_) ve yapılandırmasına bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="e2254-120">The `MyOptions` class is added to the service container with [Configure&lt;TOptions&gt;(IServiceCollection, IConfiguration)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsconfigurationservicecollectionextensions.configure#Microsoft_Extensions_DependencyInjection_OptionsConfigurationServiceCollectionExtensions_Configure__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_Microsoft_Extensions_Configuration_IConfiguration_) and bound to configuration:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="e2254-121">Sayfa modeli kullanan aşağıdaki [Oluşturucusu bağımlılık ekleme](xref:mvc/controllers/dependency-injection) ile [IOptions&lt;TOptions&gt; ](/dotnet/api/Microsoft.Extensions.Options.IOptions-1) ayarlara erişmek için (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2254-121">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with [IOptions&lt;TOptions&gt;](/dotnet/api/Microsoft.Extensions.Options.IOptions-1) to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="e2254-122">Örnek kullanıcının *appsettings.json* dosya için değerler belirten `option1` ve `option2`:</span><span class="sxs-lookup"><span data-stu-id="e2254-122">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/sample/appsettings.json?highlight=2-3)]

<span data-ttu-id="e2254-123">Ne zaman uygulamayı çalıştırın, sayfa modelin `OnGet` yöntemi seçenek sınıfı değerleri gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="e2254-123">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="e2254-124">Özel bir kullanırken [ConfigurationBuilder](/dotnet/api/system.configuration.configurationbuilder) seçenekleri yapılandırma ayarları dosyadan yüklemek için temel yolu doğru şekilde ayarlandığından emin olun:</span><span class="sxs-lookup"><span data-stu-id="e2254-124">When using a custom [ConfigurationBuilder](/dotnet/api/system.configuration.configurationbuilder) to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="e2254-125">Temel yol açık olarak ayarlama gerekli değildir seçenekleri yapılandırma ile ayarları dosyasından yüklenirken [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span><span class="sxs-lookup"><span data-stu-id="e2254-125">Explicitly setting the base path isn't required when loading options configuration from the settings file via [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="e2254-126">Bir temsilci ile basit seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="e2254-126">Configure simple options with a delegate</span></span>

<span data-ttu-id="e2254-127">Bir temsilci ile basit seçeneklerini yapılandırma örnek olarak gösterilmiştir &num;2'de [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="e2254-127">Configuring simple options with a delegate is demonstrated as Example &num;2 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="e2254-128">Bir temsilci seçenekleri değerleri ayarlamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="e2254-128">Use a delegate to set options values.</span></span> <span data-ttu-id="e2254-129">Örnek uygulama kullandığı `MyOptionsWithDelegateConfig` sınıfı (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2254-129">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/sample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="e2254-130">Aşağıdaki kodda, ikinci bir `IConfigureOptions<TOptions>` hizmet, hizmet kapsayıcıya eklenir.</span><span class="sxs-lookup"><span data-stu-id="e2254-130">In the following code, a second `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="e2254-131">Bir temsilci ile yapılandırmak için kullandığı `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="e2254-131">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="e2254-132">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e2254-132">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="e2254-133">Birden çok yapılandırma sağlayıcısı ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e2254-133">You can add multiple configuration providers.</span></span> <span data-ttu-id="e2254-134">Yapılandırma sağlayıcıları NuGet paketleri içinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e2254-134">Configuration providers are available in NuGet packages.</span></span> <span data-ttu-id="e2254-135">Kayıtlı edebilmesi uygulandıkları.</span><span class="sxs-lookup"><span data-stu-id="e2254-135">They're applied in order that they're registered.</span></span>

<span data-ttu-id="e2254-136">Her çağrı [yapılandırma&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1.configure) ekler bir `IConfigureOptions<TOptions>` hizmet kapsayıcıya hizmet.</span><span class="sxs-lookup"><span data-stu-id="e2254-136">Each call to [Configure&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1.configure) adds an `IConfigureOptions<TOptions>` service to the service container.</span></span> <span data-ttu-id="e2254-137">Yukarıdaki örnekte, değerlerini `Option1` ve `Option2` her ikisi de belirtilmiş *appsettings.json*, ancak değerlerini `Option1` ve `Option2` yapılandırılmış temsilci tarafından geçersiz kılınır.</span><span class="sxs-lookup"><span data-stu-id="e2254-137">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="e2254-138">Son yapılandırma kaynağı birden fazla Yapılandırma hizmeti etkinleştirildiğinde, belirtilen *WINS* ve yapılandırma değeri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e2254-138">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="e2254-139">Ne zaman uygulamayı çalıştırın, sayfa modelin `OnGet` yöntemi seçenek sınıfı değerleri gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="e2254-139">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delgate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="e2254-140">Suboptions yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e2254-140">Suboptions configuration</span></span>

<span data-ttu-id="e2254-141">Suboptions yapılandırma, örnek olarak gösterilmiştir &num;3'te [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="e2254-141">Suboptions configuration is demonstrated as Example &num;3 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="e2254-142">Uygulamaları, belirli özellik gruplar (sınıflar) uygulamasında ilgili seçenekleri sınıflar oluşturmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="e2254-142">Apps should create options classes that pertain to specific feature groups (classes) in the app.</span></span> <span data-ttu-id="e2254-143">Yapılandırma değerleri gerektiren uygulama bölümleri, yalnızca kullandıkları yapılandırma değerleri için erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e2254-143">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="e2254-144">Seçenekleri yapılandırmayı bağlanırken, seçenek türünün her bir özellik form için bir yapılandırma anahtarı bağlı `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="e2254-144">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="e2254-145">Örneğin, `MyOptions.Option1` özelliğe anahtarına `Option1`, den okunan `option1` özelliğinde *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e2254-145">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="e2254-146">Aşağıdaki kodda, üçüncü `IConfigureOptions<TOptions>` hizmet, hizmet kapsayıcıya eklenir.</span><span class="sxs-lookup"><span data-stu-id="e2254-146">In the following code, a third `IConfigureOptions<TOptions>` service is added to the service container.</span></span> <span data-ttu-id="e2254-147">Bunu bağlar `MySubOptions` bölümüne `subsection` , *appsettings.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="e2254-147">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="e2254-148">`GetSection` Genişletme yöntemi gerektiren [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="e2254-148">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="e2254-149">Uygulama kullanıyorsa [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 veya üzeri), paket otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="e2254-149">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="e2254-150">Örnek'ın *appsettings.json* dosyasını tanımlayan bir `subsection` tuşları üyesiyle `suboption1` ve `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="e2254-150">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/sample/appsettings.json?highlight=4-7)]

<span data-ttu-id="e2254-151">`MySubOptions` Sınıf özelliklerini tanımlayan `SubOption1` ve `SubOption2`seçenekleri değerleri tutmak için (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2254-151">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/sample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="e2254-152">Sayfa modeli `OnGet` yöntemi seçenekleri değerlere sahip bir dize döndürür (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2254-152">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="e2254-153">Uygulamayı çalıştırdığınızda `OnGet` yöntemi sınıfı değerleri alt seçeneğini gösteren bir dize döndürür:</span><span class="sxs-lookup"><span data-stu-id="e2254-153">When the app is run, the `OnGet` method returns a string showing the sub-option class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="e2254-154">Bir görünüm modeli veya doğrudan görünümü ekleme ile sağlanan seçenekleri</span><span class="sxs-lookup"><span data-stu-id="e2254-154">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="e2254-155">Seçenekler ile doğrudan görünümü ekleme veya bir görünüm modeli tarafından sağlanan örnek olarak gösterilen &num;4'te [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="e2254-155">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="e2254-156">Bir görünüm modeli veya ekleme seçenekleri sağlanabilir `IOptions<TOptions>` doğrudan bir görünüm (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2254-156">Options can be supplied in a view model or by injecting `IOptions<TOptions>` directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="e2254-157">Doğrudan ekleme işlemi için ekleme `IOptions<MyOptions>` ile bir `@inject` yönergesi:</span><span class="sxs-lookup"><span data-stu-id="e2254-157">For direct injection, inject `IOptions<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/sample/Pages/Index.cshtml?range=1-10&highlight=5)]

<span data-ttu-id="e2254-158">Uygulama çalıştırıldığında, oluşturulan sayfada seçenekleri değerler gösterilir:</span><span class="sxs-lookup"><span data-stu-id="e2254-158">When the app is run, the options values are shown in the rendered page:</span></span>

![Seçenek değerleri Seçenek1: value1_from_json ve Seçenek2: -1 modelinden ve ekleme görünümüne tarafından yüklenir.](options/_static/view.png)

::: moniker range=">= aspnetcore-1.1"

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="e2254-160">Yapılandırma verileri IOptionsSnapshot ile yeniden yükleyin</span><span class="sxs-lookup"><span data-stu-id="e2254-160">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="e2254-161">Yapılandırma verileri ile yeniden yüklemeyi `IOptionsSnapshot` gösterildiği gibi &num;5'te [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="e2254-161">Reloading configuration data with `IOptionsSnapshot` is demonstrated in Example &num;5 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="e2254-162">[IOptionsSnapshot](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1) seçenekleri en az işlem ek yükü ile yeniden yüklemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="e2254-162">[IOptionsSnapshot](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1) supports reloading options with minimal processing overhead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e2254-163">Seçenekler, erişilebilir ve istek ömrü boyunca önbelleğe istek başına bir kez hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="e2254-163">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="e2254-164">`IOptionsSnapshot` anlık görüntüsüdür [IOptionsMonitor&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) ve güncelleştirmeleri otomatik olarak her izleyici tetikler. Bu veri kaynağını değiştirme üzerinde bağlı olarak değişir.</span><span class="sxs-lookup"><span data-stu-id="e2254-164">`IOptionsSnapshot` is a snapshot of [IOptionsMonitor&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) and updates automatically whenever the monitor triggers changes based on the data source changing.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="e2254-165">Aşağıdaki örnek, yeni bir nasıl gösterir `IOptionsSnapshot` sonra oluşturulan *appsettings.json* değişiklikleri (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="e2254-165">The following example demonstrates how a new `IOptionsSnapshot` is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="e2254-166">Sunucuya birden çok istek tarafından sağlanan sabit değerler döndürür *appsettings.json* yapılandırma yeniden yükler ve dosya değiştirildiğinde kadar dosya.</span><span class="sxs-lookup"><span data-stu-id="e2254-166">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="e2254-167">Aşağıdaki resimde gösterilmektedir ilk `option1` ve `option2` yüklenen değerler *appsettings.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="e2254-167">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="e2254-168">Değerlerinde değişiklik *appsettings.json* dosyasını `value1_from_json UPDATED` ve `200`.</span><span class="sxs-lookup"><span data-stu-id="e2254-168">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="e2254-169">Kaydet *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="e2254-169">Save the *appsettings.json* file.</span></span> <span data-ttu-id="e2254-170">Seçenekleri değerleri güncelleştirildiğini görmek için tarayıcıyı yenileyin:</span><span class="sxs-lookup"><span data-stu-id="e2254-170">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="e2254-171">Adlı IConfigureNamedOptions seçenekleri desteği</span><span class="sxs-lookup"><span data-stu-id="e2254-171">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="e2254-172">Seçenekleri desteğiyle adlı [IConfigureNamedOptions](/dotnet/api/microsoft.extensions.options.iconfigurenamedoptions-1) örnek olarak gösterilmiştir &num;6'da [örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span><span class="sxs-lookup"><span data-stu-id="e2254-172">Named options support with [IConfigureNamedOptions](/dotnet/api/microsoft.extensions.options.iconfigurenamedoptions-1) is demonstrated as Example &num;6 in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).</span></span>

<span data-ttu-id="e2254-173">*Seçenekleri adlı* adlandırılmış seçeneklerini yapılandırmaları arasında ayrım yapmak uygulama desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="e2254-173">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="e2254-174">Örnek uygulamada, adlandırılmış seçenekleri ile bildirilen [OptionsServiceCollectionExtensions.Configure&lt;TOptions&gt;(IServiceCollection, dize, eylem&lt;TOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configure)genişletme yöntemini sırayla çağıran [ConfigureNamedOptions&lt;TOptions&gt;. Yapılandırma](/dotnet/api/microsoft.extensions.options.configurenamedoptions-1.configure) yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e2254-174">In the sample app, named options are declared with the [OptionsServiceCollectionExtensions.Configure&lt;TOptions&gt;(IServiceCollection, String, Action&lt;TOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configure) which in turn calls the extension method [ConfigureNamedOptions&lt;TOptions&gt;.Configure](/dotnet/api/microsoft.extensions.options.configurenamedoptions-1.configure) method:</span></span>

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="e2254-175">Örnek uygulamayı adlandırılmış seçeneklerle erişen [IOptionsSnapshot&lt;TOptions&gt;. Alma](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1.get) (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="e2254-175">The sample app accesses the named options with [IOptionsSnapshot&lt;TOptions&gt;.Get](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1.get) (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="e2254-176">Örnek uygulamayı çalıştırma, adlandırılmış seçenekleri döndürülür:</span><span class="sxs-lookup"><span data-stu-id="e2254-176">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="e2254-177">`named_options_1` hangi yüklenen değerleri yapılandırmadan okuyoruz, sağlanan *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="e2254-177">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="e2254-178">`named_options_2` değerler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="e2254-178">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="e2254-179">`named_options_2` İçindeki temsilci `ConfigureServices` için `Option1`.</span><span class="sxs-lookup"><span data-stu-id="e2254-179">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="e2254-180">İçin varsayılan değer `Option2` tarafından sağlanan `MyOptions` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="e2254-180">The default value for `Option2` provided by the `MyOptions` class.</span></span>

<span data-ttu-id="e2254-181">Tüm seçenekleri adlandırılmış örneklerle yapılandırma [OptionsServiceCollectionExtensions.ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) yöntemi.</span><span class="sxs-lookup"><span data-stu-id="e2254-181">Configure all named options instances with the [OptionsServiceCollectionExtensions.ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) method.</span></span> <span data-ttu-id="e2254-182">Aşağıdaki kod yapılandırır `Option1` tüm adlı yapılandırma örnekleri ortak bir değere sahip.</span><span class="sxs-lookup"><span data-stu-id="e2254-182">The following code configures `Option1` for all named configuration instances with a common value.</span></span> <span data-ttu-id="e2254-183">Aşağıdaki kodu el ile ekleyin `Configure` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="e2254-183">Add the following code manually to the `Configure` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="e2254-184">Kod ekledikten sonra örnek uygulamayı çalıştırma aşağıdaki sonucu verir:</span><span class="sxs-lookup"><span data-stu-id="e2254-184">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="e2254-185">Tüm seçenekleri örneği olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="e2254-185">All options are named instances.</span></span> <span data-ttu-id="e2254-186">Varolan `IConfigureOption` örnekleri hedefleyen olarak kabul edilir `Options.DefaultName` olan örneği `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="e2254-186">Existing `IConfigureOption` instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="e2254-187">`IConfigureNamedOptions` Ayrıca uygulayan `IConfigureOptions`.</span><span class="sxs-lookup"><span data-stu-id="e2254-187">`IConfigureNamedOptions` also implements `IConfigureOptions`.</span></span> <span data-ttu-id="e2254-188">Varsayılan uygulaması [IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) ([başvuru kaynağı](https://github.com/aspnet/Options/blob/release/2.0/src/Microsoft.Extensions.Options/IOptionsFactory.cs) her uygun şekilde kullanmak için mantığı vardır.</span><span class="sxs-lookup"><span data-stu-id="e2254-188">The default implementation of the [IOptionsFactory&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) ([reference source](https://github.com/aspnet/Options/blob/release/2.0/src/Microsoft.Extensions.Options/IOptionsFactory.cs) has logic to use each appropriately.</span></span> <span data-ttu-id="e2254-189">`null` Adlandırılmış seçeneği tüm adlandırılmış örnek yerine adlandırılmış örneği belirli bir hedef için kullanılır ([ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) ve [PostConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) bu kuralı kullanın).</span><span class="sxs-lookup"><span data-stu-id="e2254-189">The `null` named option is used to target all of the named instances instead of a specific named instance ([ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) and [PostConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) use this convention).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="options-validation"></a><span data-ttu-id="e2254-190">Doğrulama seçenekleri</span><span class="sxs-lookup"><span data-stu-id="e2254-190">Options validation</span></span>

<span data-ttu-id="e2254-191">Seçenekleri doğrulama seçenekleri yapılandırıldığında seçenekleri doğrulamanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="e2254-191">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="e2254-192">Çağrı `Validate` döndüren bir doğrulama yöntemiyle `true` seçenekleri geçerliyse ve `false` geçerli değilse:</span><span class="sxs-lookup"><span data-stu-id="e2254-192">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");
        
// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
} 
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="e2254-193">Yukarıdaki örnekte adlandırılmış seçenekleri örneği ayarlar `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="e2254-193">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="e2254-194">Varsayılan seçenekleri örneği `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="e2254-194">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="e2254-195">Doğrulama seçenekleri örneği oluşturulduğunda çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="e2254-195">Validation runs when the options instance is created.</span></span> <span data-ttu-id="e2254-196">Seçenekleri örneğinizin erişilebilir doğrulama ilk zaman geçmesi garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="e2254-196">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e2254-197">Seçenekler başlangıçta yapılandırılmış ve doğrulanmış sonra seçeneklerini doğrulama seçenekleri değişikliklere karşı önlem değil.</span><span class="sxs-lookup"><span data-stu-id="e2254-197">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="e2254-198">`Validate` Yöntemi kabul bir `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="e2254-198">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="e2254-199">Doğrulama tamamen özelleştirmek için uygulama `IValidateOptions<TOptions>`, sağlar:</span><span class="sxs-lookup"><span data-stu-id="e2254-199">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="e2254-200">Seçenekleri birden çok doğrulama: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="e2254-200">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="e2254-201">Bu doğrulama başka bir seçenek türüne bağlıdır: `public DependsOnAnotherOptionValidator(IOptions<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="e2254-201">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptions<AnotherOption> options)`</span></span>

<span data-ttu-id="e2254-202">`IValidateOptions` doğrular:</span><span class="sxs-lookup"><span data-stu-id="e2254-202">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="e2254-203">Belirli bir adlandırılmış seçenekleri örneği.</span><span class="sxs-lookup"><span data-stu-id="e2254-203">A specific named options instance.</span></span>
* <span data-ttu-id="e2254-204">Tüm seçenekleri zaman `name` olduğu `null`.</span><span class="sxs-lookup"><span data-stu-id="e2254-204">All options when `name` is `null`.</span></span>

<span data-ttu-id="e2254-205">Döndürür bir `ValidateOptionsResult` arabiriminin, bir uygulamadan:</span><span class="sxs-lookup"><span data-stu-id="e2254-205">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="e2254-206">Gelecekteki sürümlerde sunulması istekli doğrulama (hızlı başlangıçta başarısız) ve veri ek açıklama tabanlı doğrulama zamanlanır.</span><span class="sxs-lookup"><span data-stu-id="e2254-206">Eager validation (fail fast at startup) and data annotation-based validation are scheduled for a future release.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

## <a name="ipostconfigureoptions"></a><span data-ttu-id="e2254-207">IPostConfigureOptions</span><span class="sxs-lookup"><span data-stu-id="e2254-207">IPostConfigureOptions</span></span>

<span data-ttu-id="e2254-208">İle postconfiguration ayarlamak [IPostConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1).</span><span class="sxs-lookup"><span data-stu-id="e2254-208">Set postconfiguration with [IPostConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1).</span></span> <span data-ttu-id="e2254-209">Postconfiguration çalıştıran tüm [IConfigureOptions&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) yapılandırma gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="e2254-209">Postconfiguration runs after all [IConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2254-210">[PostConfigure&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1.postconfigure) sonrası adlandırılmış seçeneklerini yapılandırmak kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e2254-210">[PostConfigure&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1.postconfigure) is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2254-211">Kullanım [PostConfigureAll&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) yapılandırma örnekleri adlı sonrası yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="e2254-211">Use [PostConfigureAll&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) to post-configure all named configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

::: moniker-end

## <a name="options-factory-monitoring-and-cache"></a><span data-ttu-id="e2254-212">Seçenekleri Fabrika, izleme ve önbellek</span><span class="sxs-lookup"><span data-stu-id="e2254-212">Options factory, monitoring, and cache</span></span>

<span data-ttu-id="e2254-213">[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) bildirimler için kullanılan zaman `TOptions` örnekleri değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e2254-213">[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) is used for notifications when `TOptions` instances change.</span></span> <span data-ttu-id="e2254-214">`IOptionsMonitor` reloadable seçeneklerini destekler, bildirimler, değiştirmek ve `IPostConfigureOptions`.</span><span class="sxs-lookup"><span data-stu-id="e2254-214">`IOptionsMonitor` supports reloadable options, change notifications, and `IPostConfigureOptions`.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e2254-215">[IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) örnekleri yeni oluşturma seçenekleri için sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e2254-215">[IOptionsFactory&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) is responsible for creating new options instances.</span></span> <span data-ttu-id="e2254-216">Tek bir sahip [Oluştur](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1.create) yöntemi.</span><span class="sxs-lookup"><span data-stu-id="e2254-216">It has a single [Create](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1.create) method.</span></span> <span data-ttu-id="e2254-217">Varsayılan uygulama, tüm kayıtlı alan `IConfigureOptions` ve `IPostConfigureOptions` ve tüm çalışmaların önce yapılandırır, ardından sonrası yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e2254-217">The default implementation takes all registered `IConfigureOptions` and `IPostConfigureOptions` and runs all the configures first, followed by the post-configures.</span></span> <span data-ttu-id="e2254-218">Bunu ayırt `IConfigureNamedOptions` ve `IConfigureOptions` ve yalnızca uygun arabirimi çağırır.</span><span class="sxs-lookup"><span data-stu-id="e2254-218">It distinguishes between `IConfigureNamedOptions` and `IConfigureOptions` and only calls the appropriate interface.</span></span>

<span data-ttu-id="e2254-219">[IOptionsMonitorCache&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1) tarafından kullanılan `IOptionsMonitor` önbelleğine `TOptions` örnekleri.</span><span class="sxs-lookup"><span data-stu-id="e2254-219">[IOptionsMonitorCache&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1) is used by `IOptionsMonitor` to cache `TOptions` instances.</span></span> <span data-ttu-id="e2254-220">`IOptionsMonitorCache` Değeri yeniden böylece seçenekleri durumlarda, izleyici geçersiz kılar ([TryRemove](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryremove)).</span><span class="sxs-lookup"><span data-stu-id="e2254-220">The `IOptionsMonitorCache` invalidates options instances in the monitor so that the value is recomputed ([TryRemove](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryremove)).</span></span> <span data-ttu-id="e2254-221">Değerleri el ile yanı ile sunulan [TryAdd](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryadd).</span><span class="sxs-lookup"><span data-stu-id="e2254-221">Values can be manually introduced as well with [TryAdd](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryadd).</span></span> <span data-ttu-id="e2254-222">[Temizle](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.clear) yöntemi tüm adlandırılmış örnekler isteğe bağlı olarak yeniden oluşturulması sırasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e2254-222">The [Clear](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.clear) method is used when all named instances should be recreated on demand.</span></span>

::: moniker-end

## <a name="accessing-options-during-startup"></a><span data-ttu-id="e2254-223">Başlatma sırasında erişilebilirlik seçenekleri</span><span class="sxs-lookup"><span data-stu-id="e2254-223">Accessing options during startup</span></span>

<span data-ttu-id="e2254-224">`IOptions` kullanılabilir `Startup.Configure`, hizmetleri önce oluşturulan bu yana `Configure` yöntemini yürütür.</span><span class="sxs-lookup"><span data-stu-id="e2254-224">`IOptions` can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.Value.Option1;
}
```

<span data-ttu-id="e2254-225">`IOptions` içinde kullanılmaması `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e2254-225">`IOptions` shouldn't be used in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e2254-226">Hizmet Kayıtları sıralama nedeniyle tutarsız seçenekleri durumu bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e2254-226">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2254-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e2254-227">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
