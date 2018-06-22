---
title: ASP.NET Core görünümlerde içine bağımlılık ekleme
author: ardalis
description: ASP.NET Core bağımlılık ekleme MVC görünümleri içine nasıl destekler? öğrenin.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: 753a335ec4f9f6a62fd20851af43da078b6f6a37
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36277338"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="2d4fc-103">ASP.NET Core görünümlerde içine bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="2d4fc-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="2d4fc-104">Tarafından [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2d4fc-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2d4fc-105">ASP.NET çekirdeği destekler [bağımlılık ekleme](xref:fundamentals/dependency-injection) görünümleri içine.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="2d4fc-106">Bu, yerelleştirme veya yalnızca görünüm öğeleri yerleştirmek için gereken verileri gibi görünüm özgü Hizmetleri için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="2d4fc-107">Korumak denemelisiniz [sorunları ayrılması](http://deviq.com/separation-of-concerns/) görünümleri ve denetleyicilerini arasında.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-107">You should try to maintain [separation of concerns](http://deviq.com/separation-of-concerns/) between your controllers and views.</span></span> <span data-ttu-id="2d4fc-108">Kendi görünümlerinizi görüntülemek verilerin çoğu denetleyicisinden geçirilmesi.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="2d4fc-109">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d4fc-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="a-simple-example"></a><span data-ttu-id="2d4fc-110">Basit bir örnek</span><span class="sxs-lookup"><span data-stu-id="2d4fc-110">A Simple Example</span></span>

<span data-ttu-id="2d4fc-111">Bir görünümü kullanarak bir hizmet ekleyemezsiniz `@inject` yönergesi.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-111">You can inject a service into a view using the `@inject` directive.</span></span> <span data-ttu-id="2d4fc-112">Düşünebilirsiniz `@inject` görünümünüze özellik ekleme ve DI kullanan özellik doldurmak olarak.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-112">You can think of `@inject` as adding a property to your view, and populating the property using DI.</span></span>

<span data-ttu-id="2d4fc-113">Sözdizimi `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="2d4fc-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="2d4fc-114">Bir örneği `@inject` eylem:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-114">An example of `@inject` in action:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="2d4fc-115">Bu görünüm bir listesini görüntüler `ToDoItem` genel istatistiklerini gösteren bir Özet birlikte örnekleri.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-115">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="2d4fc-116">Özet doldurulur eklenen gelen `StatisticsService`.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-116">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="2d4fc-117">Bu hizmet bağımlılık ekleme için kayıtlı `ConfigureServices` içinde *haline*:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-117">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="2d4fc-118">`StatisticsService` Kümesi üzerinde bazı hesaplamalar gerçekleştirir `ToDoItem` depo erişen örnekleri:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-118">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="2d4fc-119">Örnek deposu, bir bellek içi koleksiyonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-119">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="2d4fc-120">Yukarıda gösterilen uygulama (tüm verilerin bellekte faaliyet) büyük, uzaktan erişilen veri kümeleri için önerilmez.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-120">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="2d4fc-121">Örnek verileri modele görünüme bağlı ve görünüme eklenen hizmet görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-121">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Toplam öğeleri listeleme görüntülemek için öğeleri, ortalama öncelik ve bunların öncelik düzeyleri ve tamamlanma gösteren Boole değerleri ile görevlerin bir listesi tamamlandı.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="2d4fc-123">Arama veri doldurma</span><span class="sxs-lookup"><span data-stu-id="2d4fc-123">Populating Lookup Data</span></span>

<span data-ttu-id="2d4fc-124">Görünüm ekleme açılır listeleri gibi kullanıcı Arabirimi öğeleri seçeneklerinde doldurmak yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-124">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="2d4fc-125">Cinsiyetiniz, durumu ve diğer tercihlerinizi belirlemek için seçenekleri içeren bir kullanıcı profili form göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-125">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="2d4fc-126">Standart bir MVC yaklaşım kullanarak formu işlemeye veri erişim Hizmetleri, bu seçenekleri her istemek ve bir modeli doldurmak için denetleyici duyar veya `ViewBag` her bağlanması için seçenekler kümesi.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-126">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="2d4fc-127">Alternatif bir yaklaşım Hizmetleri seçenekleri elde etmek için doğrudan görünümüne yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-127">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="2d4fc-128">Bu görünüm öğesi yapım mantığı görünüme taşıma denetleyicisi tarafından gerekli kod miktarını azaltır.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-128">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="2d4fc-129">Profil örneği form geçirmek bir profil düzenleme formu görüntülemek için denetleyici eylemi yalnızca gerekir:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-129">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="2d4fc-130">Bu tercihler güncelleştirmek için kullanılan HTML formu açılır listeleri üç özellikleri içerir:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-130">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Profil görünümü adını, cinsiyetiniz, durumu ve sık kullanılan renk girişi sağlayan bir formla güncelleştirin.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="2d4fc-132">Bu listeler görünüme eklenen hizmet tarafından doldurulur:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-132">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="2d4fc-133">`ProfileOptionsService` Yalnızca bu form için gereken verileri sağlamak üzere tasarlanmış bir kullanıcı Arabirimi düzeyi hizmeti:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-133">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="2d4fc-134">Bağımlılık ekleme aracılığıyla istek türlerinin kaydetmeyi unutmayın `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-134">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2d4fc-135">Hizmet sağlayıcısı dahili aracılığıyla sorgulanan çünkü bir kaydı türü çalışma zamanında aykırı [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="2d4fc-135">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="2d4fc-136">Hizmetleri geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="2d4fc-136">Overriding Services</span></span>

<span data-ttu-id="2d4fc-137">Yeni hizmetler injecting yanı sıra bu tekniği de daha önce eklenen Hizmetleri sayfasında geçersiz kılmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-137">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="2d4fc-138">Aşağıdaki şekilde tüm kullanılabilir alanlar ilk örnekte kullanılan sayfasında gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-138">The figure below shows all of the fields available on the page used in the first example:</span></span>

![IntelliSense bağlam menüsünde yazılmış bir @ simgesinden Html, bileşen, StatsService ve Url alanlarını listeleme](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="2d4fc-140">Gördüğünüz gibi varsayılan alanlar `Html`, `Component`, ve `Url` (yanı sıra `StatsService` biz hatalara).</span><span class="sxs-lookup"><span data-stu-id="2d4fc-140">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="2d4fc-141">Örneği için varsayılan HTML Yardımcıları kendi ile değiştirmek istiyorsanız, kolayca bunu kullanarak yapabilirsiniz, `@inject`:</span><span class="sxs-lookup"><span data-stu-id="2d4fc-141">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="2d4fc-142">Var olan hizmetleri genişletmek istiyorsanız, içinden devralma veya var olan bir uygulama ile kendi kaydırma sırasında yalnızca bu tekniği kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-142">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="2d4fc-143">Ayrıca Bkz.</span><span class="sxs-lookup"><span data-stu-id="2d4fc-143">See Also</span></span>

* <span data-ttu-id="2d4fc-144">Simon Timms Blog: [, görünüme arama verilerini alma](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="2d4fc-144">Simon Timms Blog: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
