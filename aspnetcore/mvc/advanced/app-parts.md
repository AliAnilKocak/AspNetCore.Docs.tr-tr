---
title: "ASP.NET Core uygulama bölümleri"
author: ardalis
description: "Bir uygulama abstrations kaynaklardır uygulama bölümleri bulmak veya bir derlemeye ait özelliklerin yüklenmesini önlemek için uygulamanızı yapılandırmak için nasıl kullanılacağını öğrenin."
keywords: "ASP.NET Core, uygulama bölümü, uygulama bölümü"
ms.author: riande
manager: wpickett
ms.date: 01/04/2017
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899963613a98
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a260675e7461105d4f6a0c61fd13971663c268f2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="51703-104">ASP.NET Core uygulama bölümleri</span><span class="sxs-lookup"><span data-stu-id="51703-104">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="51703-105">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51703-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="51703-106">Bir *uygulama bölümü* MVC denetleyicileri, görünüm bileşenleri gibi özellikleri, bir uygulama kaynakları üzerinden bir soyutlamadır veya etiket Yardımcıları saptanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="51703-106">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="51703-107">Bir uygulama bölümü bir derleme başvurusu ve düzenlemenizi sağlayan türleri ve derleme başvurularını yalıtan bir AssemblyPart örnektir.</span><span class="sxs-lookup"><span data-stu-id="51703-107">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="51703-108">*Özellik sağlayıcıları* ASP.NET Core MVC uygulama özelliklerini doldurmak için uygulama bölümleri ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="51703-108">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="51703-109">Ana kullanım örneği uygulama bölümleri için Bul (veya yüklenmesini önlemek için) Uygulamanızı yapılandırmak izin vermektir bütünleştirilmiş MVC özelliklerinden.</span><span class="sxs-lookup"><span data-stu-id="51703-109">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="51703-110">Uygulama bölümlerini Tanıtımı</span><span class="sxs-lookup"><span data-stu-id="51703-110">Introducing Application Parts</span></span>

<span data-ttu-id="51703-111">MVC uygulamaları kendi özelliklerinden yük [uygulama bölümleri](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span><span class="sxs-lookup"><span data-stu-id="51703-111">MVC apps load their features from [application parts](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="51703-112">Özellikle, [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) sınıfı, bir derlemeyi tarafından yedeklenen bir uygulama bölümü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="51703-112">In particular, the [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) class represents an application part that is backed by an assembly.</span></span> <span data-ttu-id="51703-113">Bu sınıfların bulmak ve denetleyicileri, görünümü bileşenler, etiket yardımcıları ve razor derleme kaynakları gibi MVC özellikleri yüklemek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51703-113">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="51703-114">[ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) MVC uygulamasını uygulama bölümleri ve özellik sağlayıcıları kullanılabilir izlemek için sorumludur.</span><span class="sxs-lookup"><span data-stu-id="51703-114">The [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="51703-115">Etkileşim kurabildikleri `ApplicationPartManager` içinde `Startup` MVC yapılandırırken:</span><span class="sxs-lookup"><span data-stu-id="51703-115">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => p.ApplicationParts.Add(part));
```

<span data-ttu-id="51703-116">Varsayılan olarak MVC bağımlılığı ağacı arayın ve denetleyicileri (hatta diğer derlemelerde) bulun.</span><span class="sxs-lookup"><span data-stu-id="51703-116">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="51703-117">Bir rastgele derlemeden (örneğin, derleme zamanında başvurulan değil bir eklenti) yüklemek için bir uygulama bölümü kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51703-117">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="51703-118">Uygulama bölümleri için kullanabileceğiniz *kaçının* belirli derleme veya konum denetleyicileri aranıyor.</span><span class="sxs-lookup"><span data-stu-id="51703-118">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="51703-119">Hangi bölümleri (veya derlemeler) değiştirerek uygulamaya kullanılabilir olacağını kontrol `ApplicationParts` koleksiyonu `ApplicationPartManager`.</span><span class="sxs-lookup"><span data-stu-id="51703-119">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="51703-120">Girdileri sırasını `ApplicationParts` koleksiyonu önemli değil.</span><span class="sxs-lookup"><span data-stu-id="51703-120">The order of the entries in the `ApplicationParts` collection is not important.</span></span> <span data-ttu-id="51703-121">Tam olarak yapılandırılması önemlidir `ApplicationPartManager` kapsayıcısında Hizmetleri'ni yapılandırmak için kullanmadan önce.</span><span class="sxs-lookup"><span data-stu-id="51703-121">It is important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="51703-122">Örneğin, tam olarak yapılandırmanız gerekiyor `ApplicationPartManager` çağırmadan önce `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="51703-122">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="51703-123">Bunu yapmak, başarısız olan anlamına gelir uygulama bölümleri denetleyicileri sonra yöntem çağrısı etkilenmeyecek eklediğiniz (hizmet olarak kaydolur değil), uygulamanızın yanlış bevavior neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="51703-123">Failing to do so, will mean that controllers in application parts added after that method call will not be affected (will not get registered as services) which might result in incorrect bevavior of your application.</span></span>

<span data-ttu-id="51703-124">Kullanılacak istemediğiniz denetleyicileri içeren bir derleme varsa kaldırmadan `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="51703-124">If you have an assembly that contains controllers you do not want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p =>
    {
        var dependentLibrary = p.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           p.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="51703-125">Proje derleme ve bağımlı derlemeleri, ek olarak `ApplicationPartManager` bölümleri için içerecektir `Microsoft.AspNetCore.Mvc.TagHelpers` ve `Microsoft.AspNetCore.Mvc.Razor` varsayılan olarak.</span><span class="sxs-lookup"><span data-stu-id="51703-125">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="51703-126">Uygulama özellik sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="51703-126">Application Feature Providers</span></span>

<span data-ttu-id="51703-127">Uygulama özellik sağlayıcıları uygulama bölümleri inceleyin ve bu bölümleri için özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="51703-127">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="51703-128">Aşağıdaki MVC özellikler için yerleşik özellik sağlayıcıları vardır:</span><span class="sxs-lookup"><span data-stu-id="51703-128">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="51703-129">Denetleyicileri</span><span class="sxs-lookup"><span data-stu-id="51703-129">Controllers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="51703-130">Meta veri başvurusu</span><span class="sxs-lookup"><span data-stu-id="51703-130">Metadata Reference</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="51703-131">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="51703-131">Tag Helpers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="51703-132">Görünüm bileşenleri</span><span class="sxs-lookup"><span data-stu-id="51703-132">View Components</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="51703-133">Özellik sağlayıcıları devral `IApplicationFeatureProvider<T>`, burada `T` özellik türüdür.</span><span class="sxs-lookup"><span data-stu-id="51703-133">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="51703-134">MVC'ın özellik türlerinin herhangi biriyle sağlayıcıları yukarıda listelenen kendi özellik uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="51703-134">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="51703-135">Özellik sağlayıcıların sırası `ApplicationPartManager.FeatureProviders` sonraki sağlayıcıları önceki sağlayıcıları tarafından gerçekleştirilen eylemler için tepki gösterebilmesi beri koleksiyonu önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="51703-135">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="51703-136">Örnek: Genel denetleyicisi özelliği</span><span class="sxs-lookup"><span data-stu-id="51703-136">Sample: Generic controller feature</span></span>

<span data-ttu-id="51703-137">Varsayılan olarak, ASP.NET Core MVC genel denetleyicileri göz ardı eder (örneğin, `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="51703-137">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="51703-138">Bu örnek sonra varsayılan Sağlayıcısı'nı çalıştıran ve belirtilen liste için genel denetleyici örnekleri türlerinin ekleyen bir denetleyici özellik sağlayıcısı kullanır (tanımlanan `EntityTypes.Types`):</span><span class="sxs-lookup"><span data-stu-id="51703-138">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="51703-139">Varlık türleri:</span><span class="sxs-lookup"><span data-stu-id="51703-139">The entity types:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="51703-140">Özellik sağlayıcısı eklenir `Startup`:</span><span class="sxs-lookup"><span data-stu-id="51703-140">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p => 
        p.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="51703-141">Varsayılan olarak, yönlendirme için kullanılan genel denetleyicisi adları biçiminde olacaktır *GenericController'1 [pencere]* yerine *pencere öğesi*.</span><span class="sxs-lookup"><span data-stu-id="51703-141">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="51703-142">Aşağıdaki öznitelik adı denetleyici tarafından kullanılan genel tür karşılık gelecek şekilde değiştirmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="51703-142">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="51703-143">`GenericController` Sınıfı:</span><span class="sxs-lookup"><span data-stu-id="51703-143">The `GenericController` class:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="51703-144">Eşleşen bir rota istendiğinde sonucu:</span><span class="sxs-lookup"><span data-stu-id="51703-144">The result, when a matching route is requested:</span></span>

![Örnek uygulamadan çıktı örneği okuma ve 'Hello genel Sproket denetleyicisinden.'](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="51703-146">Örnek: Görüntü kullanılabilir özellikler</span><span class="sxs-lookup"><span data-stu-id="51703-146">Sample: Display available features</span></span>

<span data-ttu-id="51703-147">İsteyerek uygulamanıza doldurulan kullanılabilen özellikleri yineleyebilirsiniz bir `ApplicationPartManager` aracılığıyla [bağımlılık ekleme](../../fundamentals/dependency-injection.md) ve uygun özelliklerin örneklerini doldurmak için kullanma:</span><span class="sxs-lookup"><span data-stu-id="51703-147">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[Main](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="51703-148">Örnek çıktı:</span><span class="sxs-lookup"><span data-stu-id="51703-148">Example output:</span></span>

![Örnek uygulaması örnek çıkışı](app-parts/_static/available-features.png)
