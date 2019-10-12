---
title: Yerelleştirme genişletilebilirliği
author: hishamco
description: ASP.NET Core uygulamalarında yerelleştirme API 'Lerini genişletmeyi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: dfa2efe78b2e1e118e6b3f09bfc41f3330e1d721
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288944"
---
# <a name="localization-extensibility"></a><span data-ttu-id="d8c4f-103">Yerelleştirme genişletilebilirliği</span><span class="sxs-lookup"><span data-stu-id="d8c4f-103">Localization Extensibility</span></span>

<span data-ttu-id="d8c4f-104">, [Hisham bin Ateya](https://github.com/hishamco) tarafından</span><span class="sxs-lookup"><span data-stu-id="d8c4f-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="d8c4f-105">Bu makalede:</span><span class="sxs-lookup"><span data-stu-id="d8c4f-105">This article:</span></span>

* <span data-ttu-id="d8c4f-106">Yerelleştirme API 'Lerinde genişletilebilirlik noktalarını listeler.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="d8c4f-107">ASP.NET Core uygulama yerelleştirmesini genişletme hakkında yönergeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="d8c4f-108">Yerelleştirme API 'Lerinde Genişletilebilir noktaları</span><span class="sxs-lookup"><span data-stu-id="d8c4f-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="d8c4f-109">ASP.NET Core yerelleştirme API 'Leri genişletilebilir olacak şekilde oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="d8c4f-110">Genişletilebilirlik, geliştiricilerin gereksinimlerine göre yerelleştirmeyi özelleştirmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="d8c4f-111">Örneğin, [Orchardcore](https://github.com/orchardCMS/OrchardCore/) `POStringLocalizer` ' dir.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="d8c4f-112">`POStringLocalizer`, Yerelleştirme kaynaklarını depolamak için `PO` dosyalarını kullanmak üzere [Taşınabilir nesne yerelleştirmesini](xref:fundamentals/portable-object-localization) kullanarak ayrıntılı olarak açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="d8c4f-113">Bu makalede, yerelleştirme API 'Lerinin sağladığı iki ana genişletilebilirlik noktası listelenmektedir:</span><span class="sxs-lookup"><span data-stu-id="d8c4f-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="d8c4f-114">Yerelleştirme kültür sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="d8c4f-114">Localization Culture Providers</span></span>

<span data-ttu-id="d8c4f-115">ASP.NET Core yerelleştirme API 'Leri, yürütülen bir isteğin geçerli kültürünü belirleyebilmesi için dört varsayılan sağlayıcıya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d8c4f-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="d8c4f-116">Önceki sağlayıcılar, [Yerelleştirme ara yazılımı](xref:fundamentals/localization) belgelerinde ayrıntılı olarak açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="d8c4f-117">Varsayılan sağlayıcılar gereksinimlerinizi karşılamıyorsa, aşağıdaki yaklaşımlardan birini kullanarak özel bir sağlayıcı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d8c4f-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="d8c4f-118">CustomRequestCultureProvider kullanma</span><span class="sxs-lookup"><span data-stu-id="d8c4f-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="d8c4f-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>, geçerli yerelleştirme kültürünü tespit etmek için basit bir temsilci kullanan özel bir <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> sağlar:</span><span class="sxs-lookup"><span data-stu-id="d8c4f-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="d8c4f-120">RequestCultureProvider 'in yeni bir ımplemei kullanın</span><span class="sxs-lookup"><span data-stu-id="d8c4f-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="d8c4f-121">Özel bir kaynaktan istek kültür bilgilerini belirleyen <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> ' ın yeni bir uygulanması oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="d8c4f-122">Örneğin, özel kaynak bir yapılandırma dosyası veya veritabanı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="d8c4f-123">Aşağıdaki örnekte, *appSettings. JSON*' dan gelen istek kültür bilgilerini belirleyen <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> ' i genişleten `AppSettingsRequestCultureProvider` gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="d8c4f-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="d8c4f-124">Yerelleştirme kaynakları</span><span class="sxs-lookup"><span data-stu-id="d8c4f-124">Localization resources</span></span>

<span data-ttu-id="d8c4f-125">ASP.NET Core yerelleştirme @no__t sağlar.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="d8c4f-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>, Yerelleştirme kaynaklarını depolamak için `resx` kullanan <xref:Microsoft.Extensions.Localization.IStringLocalizer> ' in bir uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="d8c4f-127">@No__t-0 dosyalarını kullanma sınırlı değilsiniz.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="d8c4f-128">@No__t-0 uygulayarak, herhangi bir veri kaynağı kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d8c4f-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="d8c4f-129">Aşağıdaki örnek projeler @no__t uygular-0:</span><span class="sxs-lookup"><span data-stu-id="d8c4f-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="d8c4f-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="d8c4f-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="d8c4f-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="d8c4f-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="d8c4f-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="d8c4f-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
