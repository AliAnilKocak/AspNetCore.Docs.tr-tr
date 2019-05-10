---
title: ASP.NET core'da görünümlere bağımlılık ekleme
author: ardalis
description: ASP.NET Core MVC görünümlere bağımlılık ekleme nasıl desteklediğini öğrenin.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: 940e237fd73a864d9fd59f5447fe486f93648f62
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087467"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="a5a50-103">ASP.NET core'da görünümlere bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="a5a50-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="a5a50-104">Tarafından [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a5a50-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a5a50-105">ASP.NET Core destekler [bağımlılık ekleme](xref:fundamentals/dependency-injection) görünümlere.</span><span class="sxs-lookup"><span data-stu-id="a5a50-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="a5a50-106">Bu, yerelleştirme veya yalnızca görünüm öğeleri doldurmak için gerekli veriler gibi özel görünüm Hizmetleri için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="a5a50-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="a5a50-107">Korunacak denemelisiniz [görev ayrımı nettir](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) denetleyici ve görünüm arasında.</span><span class="sxs-lookup"><span data-stu-id="a5a50-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="a5a50-108">Kendi görünümlerinizi görüntüleyin verilerden en iyi şekilde denetleyicisinden geçirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="a5a50-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="a5a50-109">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5a50-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="a-simple-example"></a><span data-ttu-id="a5a50-110">Basit bir örnek</span><span class="sxs-lookup"><span data-stu-id="a5a50-110">A Simple Example</span></span>

<span data-ttu-id="a5a50-111">Bir görünümü kullanarak bir hizmet ekleyebilir `@inject` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="a5a50-111">You can inject a service into a view using the `@inject` directive.</span></span> <span data-ttu-id="a5a50-112">Düşünebilirsiniz `@inject` görünümünüzü özellik ekleme ve DI kullanan özellik dolduruluyor.</span><span class="sxs-lookup"><span data-stu-id="a5a50-112">You can think of `@inject` as adding a property to your view, and populating the property using DI.</span></span>

<span data-ttu-id="a5a50-113">Sözdizimi `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="a5a50-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="a5a50-114">Örneği `@inject` eylem:</span><span class="sxs-lookup"><span data-stu-id="a5a50-114">An example of `@inject` in action:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="a5a50-115">Bu görünüm listesini görüntüler `ToDoItem` örnekleri, genel istatistiklerini gösteren bir özetiyle birlikte.</span><span class="sxs-lookup"><span data-stu-id="a5a50-115">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="a5a50-116">Özet doldurulur eklenen gelen `StatisticsService`.</span><span class="sxs-lookup"><span data-stu-id="a5a50-116">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="a5a50-117">Bu hizmet bağımlılık ekleme için kayıtlı `ConfigureServices` içinde *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5a50-117">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="a5a50-118">`StatisticsService` Dizi üzerinde bazı hesaplamalar yapan `ToDoItem` bir depo erişir örnekleri:</span><span class="sxs-lookup"><span data-stu-id="a5a50-118">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="a5a50-119">Örnek depoyu bir bellek içi koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="a5a50-119">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="a5a50-120">Yukarıda gösterilen uygulama (Bu, tüm verilerin bellek içinde çalışır), büyük, uzaktan erişim veri kümeleri için önerilmez.</span><span class="sxs-lookup"><span data-stu-id="a5a50-120">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="a5a50-121">Örnek verileri modele görünüme bağlı ve görünüme eklenen hizmet görüntüler:</span><span class="sxs-lookup"><span data-stu-id="a5a50-121">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Toplam öğe listesi görüntülemek için öğeleri, ortalama öncelik ve öncelik düzeyleri ve tamamlama belirten Boole değerleri görevlerinin listesi tamamlandı.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="a5a50-123">Arama verilerini doldurma</span><span class="sxs-lookup"><span data-stu-id="a5a50-123">Populating Lookup Data</span></span>

<span data-ttu-id="a5a50-124">Görünüm ekleme açılır listeleri gibi kullanıcı Arabirimi öğeleri seçeneklerinde doldurmak yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="a5a50-124">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="a5a50-125">Cinsiyet, durumunu ve diğer tercihlerinizi belirtmek için seçenekleri içeren bir kullanıcı profili form göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="a5a50-125">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="a5a50-126">Bir standart MVC yaklaşımı kullanarak form işleme her biri, bu seçenekler için veri erişim Hizmetleri isteyin ve ardından bir model doldurmak için denetleyici içerseydi veya `ViewBag` her bağlanacak seçenek kümesi ile.</span><span class="sxs-lookup"><span data-stu-id="a5a50-126">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="a5a50-127">Alternatif bir yaklaşım Hizmetleri seçenekleri elde etmek için doğrudan görünümüne ekler.</span><span class="sxs-lookup"><span data-stu-id="a5a50-127">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="a5a50-128">Bu görünüme bu görünüm öğesi oluşturma mantığı taşıma denetleyicisi tarafından gereken kod miktarını azaltır.</span><span class="sxs-lookup"><span data-stu-id="a5a50-128">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="a5a50-129">Profil örneği form geçirmek bir profil düzenleme formu görüntülemek için denetleyici eylemi yeterlidir:</span><span class="sxs-lookup"><span data-stu-id="a5a50-129">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="a5a50-130">Bu tercihler güncelleştirmek için kullanılan HTML formu açılır listeleri üç özellikleri içerir:</span><span class="sxs-lookup"><span data-stu-id="a5a50-130">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Profil görünümü adı, cinsiyet, durum ve sık kullanılan renk girişi sağlayan bir formla güncelleştirin.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="a5a50-132">Bu listeler, görünüme eklenmiş bir hizmet tarafından doldurulur:</span><span class="sxs-lookup"><span data-stu-id="a5a50-132">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="a5a50-133">`ProfileOptionsService` Yalnızca bu form için gereken verileri sağlamak üzere tasarlanmış bir UI düzeyi hizmeti:</span><span class="sxs-lookup"><span data-stu-id="a5a50-133">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="a5a50-134">Bağımlılık ekleme aracılığıyla istek türleri kaydedilecek unutmayın `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a5a50-134">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a5a50-135">Hizmet sağlayıcısı aracılığıyla dahili olarak sorgulanır çünkü bir kaydı türü çalışma zamanında bir özel durum oluşturur. [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="a5a50-135">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="a5a50-136">Hizmetleri geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="a5a50-136">Overriding Services</span></span>

<span data-ttu-id="a5a50-137">Yeni hizmet ekleme ek olarak, bu tekniği de bir sayfada daha önce eklenen Hizmetleri geçersiz kılmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a5a50-137">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="a5a50-138">Aşağıdaki şekilde ilk örnekte kullanılan sayfasında, kullanılabilir alanların tümünü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="a5a50-138">The figure below shows all of the fields available on the page used in the first example:</span></span>

![IntelliSense bağlam menüsünde bir türü belirtilmiş @ sembolünü Html, bileşen, StatsService ve Url alanları listeleme](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="a5a50-140">Gördüğünüz gibi varsayılan alanları dahil `Html`, `Component`, ve `Url` (yanı sıra `StatsService` biz hatalara).</span><span class="sxs-lookup"><span data-stu-id="a5a50-140">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="a5a50-141">Örneği için varsayılan HTML Yardımcıları kendinizinkilerle değiştirildiğinden isteseydiniz, kolayca kullanarak bunu `@inject`:</span><span class="sxs-lookup"><span data-stu-id="a5a50-141">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="a5a50-142">Var olan hizmetleri genişletmek isterseniz, dan devralan veya mevcut bir uygulama ile kendi sarmalama sırasında yalnızca bu tekniği kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a5a50-142">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="a5a50-143">Ayrıca Bkz.</span><span class="sxs-lookup"><span data-stu-id="a5a50-143">See Also</span></span>

* <span data-ttu-id="a5a50-144">Simon Timms Blog: [Arama verileri görünümünüzü alma](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="a5a50-144">Simon Timms Blog: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
