---
title: ASP.NET Core yerelleştirme sorunlarını giderme
author: hishamco
description: ASP.NET Core uygulamalarında Yerelleştirmede sorunları tanılamayı öğrenin.
ms.author: riande
ms.date: 01/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: f5c2be93be4f896b1822bf93deef24f091e30442
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774294"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="b8983-103">ASP.NET Core yerelleştirme sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="b8983-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="b8983-104">, [Hisham bin Ateya](https://github.com/hishamco) tarafından</span><span class="sxs-lookup"><span data-stu-id="b8983-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="b8983-105">Bu makalede ASP.NET Core uygulaması yerelleştirme sorunlarını tanılamaya yönelik yönergeler sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b8983-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="b8983-106">Yerelleştirme yapılandırma sorunları</span><span class="sxs-lookup"><span data-stu-id="b8983-106">Localization configuration issues</span></span>

<span data-ttu-id="b8983-107">**Yerelleştirme ara yazılım sırası**</span><span class="sxs-lookup"><span data-stu-id="b8983-107">**Localization middleware order**</span></span>  
<span data-ttu-id="b8983-108">Yerelleştirme ara yazılımı beklenen şekilde sıralandığı için uygulama yerelleştirilemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="b8983-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="b8983-109">Bu sorunu çözmek için, yerelleştirme ara yazılımı 'nın MVC ara yazılım öncesinde kayıtlı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="b8983-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="b8983-110">Aksi takdirde, yerelleştirme ara yazılımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="b8983-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="b8983-111">**Yerelleştirme kaynakları yolu bulunamadı**</span><span class="sxs-lookup"><span data-stu-id="b8983-111">**Localization resources path not found**</span></span>

<span data-ttu-id="b8983-112">**RequestCultureProvider içindeki desteklenen kültürler, kayıtlı bir kez ile eşleşmiyor**</span><span class="sxs-lookup"><span data-stu-id="b8983-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="b8983-113">Kaynak dosyası adlandırma sorunları</span><span class="sxs-lookup"><span data-stu-id="b8983-113">Resource file naming issues</span></span>

<span data-ttu-id="b8983-114">ASP.NET Core, [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)ayrıntılı olarak açıklanan yerelleştirme kaynakları dosya adlandırmasına yönelik önceden tanımlanmış kurallara ve yönergelere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b8983-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="b8983-115">Eksik kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b8983-115">Missing resources</span></span>

<span data-ttu-id="b8983-116">Kaynakların Bulunamamasının yaygın nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b8983-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="b8983-117">Kaynak adları, `resx` dosyada veya yorumdur isteğinde yanlış yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b8983-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="b8983-118">Kaynak bazı diller `resx` için öğesinde yok, ancak başkaları içinde var.</span><span class="sxs-lookup"><span data-stu-id="b8983-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="b8983-119">Sorun yaşamaya devam ediyorsanız, eksik kaynaklar hakkında daha fazla ayrıntı için yerelleştirme günlüğü iletilerini ( `Debug` günlük düzeyinde) kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="b8983-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="b8983-120">_**İpucu:** Kullanırken `CookieRequestCultureProvider`, tek tırnak, yerelleştirme tanımlama bilgisi değeri içindeki kültürler ile kullanılmaz. Örneğin, `c='en-UK'|uic='en-US'` geçersiz bir tanımlama bilgisi değeridir, ancak `c=en-UK|uic=en-US` geçerli olur._</span><span class="sxs-lookup"><span data-stu-id="b8983-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="b8983-121">Sınıf kitaplığı sorunlarını & kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b8983-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="b8983-122">Varsayılan olarak ASP.NET Core, sınıf kitaplıklarının kaynak dosyalarını [Resourcelocationattribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)aracılığıyla bulmasını sağlamak için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8983-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="b8983-123">Sınıf kitaplıklarıyla ilgili yaygın sorunlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b8983-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="b8983-124">Bir sınıf `ResourceLocationAttribute` kitaplığında eksik olan, kaynakları keşfetmesini engelleyecek `ResourceManagerStringLocalizerFactory` .</span><span class="sxs-lookup"><span data-stu-id="b8983-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="b8983-125">Kaynak dosyası adlandırma.</span><span class="sxs-lookup"><span data-stu-id="b8983-125">Resource file naming.</span></span> <span data-ttu-id="b8983-126">Daha fazla bilgi için bkz. [kaynak dosyası adlandırma sorunları](#resource-file-naming-issues) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8983-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="b8983-127">Sınıf kitaplığının kök ad alanı değiştiriliyor.</span><span class="sxs-lookup"><span data-stu-id="b8983-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="b8983-128">Daha fazla bilgi için bkz. [kök ad alanı sorunları](#root-namespace-issues) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8983-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="b8983-129">CustomRequestCultureProvider beklendiği gibi çalışmıyor</span><span class="sxs-lookup"><span data-stu-id="b8983-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="b8983-130">`RequestLocalizationOptions` Sınıfı üç varsayılan sağlayıcıya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="b8983-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="b8983-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) , yerelleştirme kültürünün uygulamanızda nasıl sağlandığını özelleştirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8983-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="b8983-132">, `CustomRequestCultureProvider` Varsayılan sağlayıcılar gereksinimlerinizi karşılamadığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b8983-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="b8983-133">Yaygın bir nedenden dolayı özel sağlayıcı, `RequestCultureProviders` listedeki ilk sağlayıcı değildir.</span><span class="sxs-lookup"><span data-stu-id="b8983-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="b8983-134">Bu sorunu çözmek için:</span><span class="sxs-lookup"><span data-stu-id="b8983-134">To resolve this issue:</span></span>

- <span data-ttu-id="b8983-135">Özel sağlayıcıyı `RequestCultureProviders` listedeki 0 konumuna aşağıdaki gibi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b8983-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="b8983-136">Özel `AddInitialRequestCultureProvider` sağlayıcıyı ilk sağlayıcı olarak ayarlamak için genişletme yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8983-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="b8983-137">Kök ad alanı sorunları</span><span class="sxs-lookup"><span data-stu-id="b8983-137">Root Namespace issues</span></span>

<span data-ttu-id="b8983-138">Bir derlemenin kök ad alanı derleme adından farklıysa, yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="b8983-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="b8983-139">Bu sorundan kaçınmak için, [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) ayrıntılı olarak açıklanan [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)kullanın</span><span class="sxs-lookup"><span data-stu-id="b8983-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="b8983-140">Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir.</span><span class="sxs-lookup"><span data-stu-id="b8983-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="b8983-141">`my-project-name.csproj` Örneğin, bu hata için kök ad `my_project_name` alanını ve derleme adını `my-project-name` kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8983-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="b8983-142">Kaynak & oluşturma eylemi</span><span class="sxs-lookup"><span data-stu-id="b8983-142">Resources & Build Action</span></span>

<span data-ttu-id="b8983-143">Yerelleştirme için kaynak dosyaları kullanıyorsanız, uygun bir yapı eylemi olması önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b8983-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="b8983-144">Bunlara **gömülü kaynak**olmaları gerekir, aksi takdirde `ResourceStringLocalizer` bu kaynakları bulamaz.</span><span class="sxs-lookup"><span data-stu-id="b8983-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
