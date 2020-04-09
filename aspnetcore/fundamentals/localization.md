---
title: ASP.NET Çekirdek'te küreselleşme ve yerelleşme
author: rick-anderson
description: ASP.NET Core'un içeriği farklı dillerde ve kültürlerde yerelleştirmek için nasıl hizmet ve ara yazılım sağladığını öğrenin.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: b175354220a8a71c029e005f27443d5a72749a11
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662122"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="ab18e-103">ASP.NET Çekirdek'te küreselleşme ve yerelleşme</span><span class="sxs-lookup"><span data-stu-id="ab18e-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="ab18e-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), Bart [Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="ab18e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="ab18e-105">ASP.NET Core ile çok dilli bir web sitesi oluşturmak, sitenizin daha geniş bir kitleye ulaşmasını sağlayacaktır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="ab18e-106">ASP.NET Core, farklı dillerde ve kültürlere yerelleştirme hizmetleri ve ara yazılımlar sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="ab18e-107">Uluslararasılaşma [Küreselleşme](/dotnet/api/system.globalization) ve [Yerelleşme](/dotnet/standard/globalization-localization/localization)içerir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="ab18e-108">Küreselleşme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="ab18e-109">Genelleştirme, belirli coğrafi bölgelerle ilgili tanımlı bir dil komut dosyası kümesinin girişi, görüntülenmesi ve çıktısı için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="ab18e-110">Yerelleştirme, yerelleştirilebilirlik için zaten işlemiş olduğunuz küreselleştirilmiş bir uygulamayı belirli bir kültüre/yerelliğe uyarlama işlemidir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="ab18e-111">Daha fazla bilgi için bu belgenin sonuna yakın **Küreselleşme ve yerelleştirme terimlerini** görün.</span><span class="sxs-lookup"><span data-stu-id="ab18e-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="ab18e-112">Uygulama yerelleştirme aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-112">App localization involves the following:</span></span>

1. <span data-ttu-id="ab18e-113">Uygulamanın içeriğini yerelleştirilebilir hale getirin</span><span class="sxs-lookup"><span data-stu-id="ab18e-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="ab18e-114">Desteklediğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="ab18e-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="ab18e-115">Her istek için dili/kültürü seçmek için bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="ab18e-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="ab18e-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab18e-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="ab18e-117">Uygulamanın içeriğini yerelleştirilebilir hale getirin</span><span class="sxs-lookup"><span data-stu-id="ab18e-117">Make the app's content localizable</span></span>

<span data-ttu-id="ab18e-118">ASP.NET Core'da `IStringLocalizer` tanıtıldı `IStringLocalizer<T>` ve yerelleştirilmiş uygulamalar geliştirirken üretkenliği artırmak için geliştirildi.</span><span class="sxs-lookup"><span data-stu-id="ab18e-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="ab18e-119">`IStringLocalizer`çalışma zamanında kültüre özgü kaynaklar sağlamak için [ResourceManager](/dotnet/api/system.resources.resourcemanager) ve [ResourceReader'ı](/dotnet/api/system.resources.resourcereader) kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="ab18e-120">Basit arabirimde bir dizinleyici ve yerelleştirilmiş dizeleri döndüren için bir `IEnumerable` vardır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="ab18e-121">`IStringLocalizer`varsayılan dil dizelerini bir kaynak dosyasında depolamanızı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="ab18e-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="ab18e-122">Yerelleştirme için hedeflenen bir uygulama geliştirebilirsiniz ve geliştirme nin başında kaynak dosyaları oluşturmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ab18e-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="ab18e-123">Aşağıdaki kod, yerelleştirme için "Başlık Hakkında" dizesini nasıl sarın gösterir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="ab18e-124">Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [Bağımlılık Enjeksiyon](dependency-injection.md)geliyor.</span><span class="sxs-lookup"><span data-stu-id="ab18e-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="ab18e-125">"Başlık Hakkında"nın yerelleştirilmiş değeri bulunamazsa, dizinleyici anahtarı döndürülür, yani "Başlık Hakkında" dizesi.</span><span class="sxs-lookup"><span data-stu-id="ab18e-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="ab18e-126">Uygulamayı geliştirmeye odaklanabilmek için varsayılan dil deki edebi dizeleri uygulamada bırakabilir ve yerelleştiriciye sarabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="ab18e-127">Varsayılan dilinizle uygulamanızı geliştirir ve varsayılan kaynak dosyası oluşturmadan yerelleştirme adımına hazırlarsınız.</span><span class="sxs-lookup"><span data-stu-id="ab18e-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="ab18e-128">Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="ab18e-129">Birçok geliştirici için varsayılan bir dile sahip olmamak *.resx* dosyası ve yalnızca dize literallerini sarmak, bir uygulamayı yerelleştirmenin ek yüküne neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="ab18e-130">Diğer geliştiriciler, daha uzun dize gerçekleriyle çalışmayı ve yerelleştirilmiş dizeleri güncelleştirmeyi kolaylaştırabileceğinden geleneksel iş akışını tercih eder.</span><span class="sxs-lookup"><span data-stu-id="ab18e-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="ab18e-131">`IHtmlLocalizer<T>` HTML içeren kaynaklar için uygulamayı kullanın.</span><span class="sxs-lookup"><span data-stu-id="ab18e-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="ab18e-132">`IHtmlLocalizer`HTML, kaynak dizesinde biçimlendirilmiş bağımsız değişkenleri kodlar, ancak KAYNAK dizesini HTML kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="ab18e-133">Aşağıda vurgulanan örnekte, yalnızca `name` parametrenin değeri HTML kodlanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="ab18e-134">**Not:** Genellikle html değil, yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ab18e-135">En düşük düzeyde, Bağımlılık `IStringLocalizerFactory` [Enjeksiyon](dependency-injection.md)ulabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ab18e-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="ab18e-136">Yukarıdaki kod, her iki fabrika oluşturma yöntemlerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="ab18e-137">Yerelleştirilmiş dizelerinizi denetleyiciye, alana göre bölümlere alabilir veya yalnızca bir kapsayıcınız olabilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="ab18e-138">Örnek uygulamada, paylaşılan kaynaklar için `SharedResource` adlandırılmış bir sahte sınıf kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="ab18e-139">Bazı geliştiriciler `Startup` sınıfı genel veya paylaşılan dizeleri içerecek şekilde kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="ab18e-140">Aşağıdaki örnekte, `InfoController` yerelleştiriciler ve `SharedResource` yerelleştiriciler kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ab18e-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="ab18e-141">Yerelleştirmeyi görüntüleme</span><span class="sxs-lookup"><span data-stu-id="ab18e-141">View localization</span></span>

<span data-ttu-id="ab18e-142">Hizmet, `IViewLocalizer` [görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="ab18e-143">Sınıf `ViewLocalizer` bu arabirimi uygular ve kaynak konumunu görünüm dosyası yolundan bulur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="ab18e-144">Aşağıdaki kod, varsayılan uygulamanın nasıl `IViewLocalizer`kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="ab18e-145">Varsayılan uygulama, `IViewLocalizer` görünümün dosya adını temel alan kaynak dosyasını bulur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="ab18e-146">Genel paylaşılan kaynak dosyasını kullanma seçeneği yoktur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="ab18e-147">`ViewLocalizer`kullanarak yerelleştiriciyi `IHtmlLocalizer`uygular, böylece Razor yerelleştirilmiş dizeyi HTML kodlamaz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="ab18e-148">Kaynak dizeleri parametrenize `IViewLocalizer` edebilirsiniz ve HTML parametreleri kodlar, ancak kaynak dizesini kodlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="ab18e-149">Aşağıdaki Jilet biçimlendirmesini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="ab18e-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="ab18e-150">Bir Fransızca kaynak dosyası aşağıdakileri içerebilir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="ab18e-151">Anahtar</span><span class="sxs-lookup"><span data-stu-id="ab18e-151">Key</span></span> | <span data-ttu-id="ab18e-152">Değer</span><span class="sxs-lookup"><span data-stu-id="ab18e-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="ab18e-153">İşlenen görünüm, kaynak dosyasındaki HTML biçimlendirmesini içerir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="ab18e-154">**Not:** Genellikle html değil, yalnızca metni yerelleştirmek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="ab18e-155">Paylaşılan kaynak dosyasını görünümde kullanmak `IHtmlLocalizer<T>`için şunları enjekte edin:</span><span class="sxs-lookup"><span data-stu-id="ab18e-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="ab18e-156">DataAnnotations yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="ab18e-156">DataAnnotations localization</span></span>

<span data-ttu-id="ab18e-157">DataAnnotations hata iletileri ile `IStringLocalizer<T>`yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="ab18e-158">`ResourcesPath = "Resources"`Seçeneğini kullanarak, hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde saklanabilir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="ab18e-159">*Kaynaklar/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ab18e-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="ab18e-160">*Kaynaklar/Görünüm Modelleri/Hesap/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ab18e-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="ab18e-161">Core MVC 1.1.0 ve daha yüksek ASP.NET, doğrulama olmayan öznitelikler yerelleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="ab18e-162">ASP.NET Core MVC 1.0, doğrulama olmayan öznitelikler için yerelleştirilmiş dizeleri **aramaz.**</span><span class="sxs-lookup"><span data-stu-id="ab18e-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="ab18e-163">Birden çok sınıf için tek kaynak dizesini kullanma</span><span class="sxs-lookup"><span data-stu-id="ab18e-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="ab18e-164">Aşağıdaki kod, birden çok sınıfiçeren doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="ab18e-165">Önceki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx'e karşılık gelen sınıf dır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="ab18e-166">Bu yaklaşımla, DataAnnotations `SharedResource`yalnızca , yerine her sınıf için kaynak kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="ab18e-167">Desteklediğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın</span><span class="sxs-lookup"><span data-stu-id="ab18e-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="ab18e-168">Desteklenen Kültürler ve DesteklenenUICultures</span><span class="sxs-lookup"><span data-stu-id="ab18e-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="ab18e-169">ASP.NET Core iki kültür değeri `SupportedCultures` belirtmenizi sağlar ve `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="ab18e-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="ab18e-170">[CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="ab18e-171">`SupportedCultures`ayrıca metnin sıralama sırasını, kasa kurallarını ve dize karşılaştırmalarını belirler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="ab18e-172">Sunucunun Kültürü nasıl aldığı hakkında daha fazla bilgi için [CultureInfo.CurrentCulture'a](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) bakın.</span><span class="sxs-lookup"><span data-stu-id="ab18e-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="ab18e-173">Dizeleri çeviren `SupportedUICultures` belirlemeler *(.resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından aranır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-173">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="ab18e-174">Basitçe, `ResourceManager` kültüre özgü dizeleri `CurrentUICulture`arar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="ab18e-175">.NET'teki her `CurrentCulture` `CurrentUICulture` iş parçacığı ve nesne vardır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="ab18e-176">ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="ab18e-177">Örneğin, geçerli iş parçacığının kültürü "en-US" (İngilizce, AMERIKA `DateTime.Now.ToLongDateString()` Birleşik Devletleri) olarak ayarlanmışsa, "18 Şubat `CurrentCulture` 2016 Perşembe" görüntülenirse, ancak "es-ES" (İspanyolca, İspanya) olarak ayarlanırsa çıktı "jueves, 18 de febrero de 2016" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="ab18e-178">Kaynak dosyalar</span><span class="sxs-lookup"><span data-stu-id="ab18e-178">Resource files</span></span>

<span data-ttu-id="ab18e-179">Kaynak dosyası, yerelleştirilebilir dizeleri koddan ayırmak için yararlı bir mekanizmadır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="ab18e-180">Varsayılan olmayan dil için çevrilmiş dizeleri yalıtılmış *.resx* kaynak dosyaları.</span><span class="sxs-lookup"><span data-stu-id="ab18e-180">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="ab18e-181">Örneğin, çevrilmiş dizeleri içeren *Welcome.es.resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="ab18e-182">"es" İspanyolca'nın dil kodudur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="ab18e-183">Visual Studio'da bu kaynak dosyasını oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="ab18e-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="ab18e-184">**Çözüm Gezgini'nde,** Kaynak dosyasını içerecek klasöre sağ tıklayın >**Yeni Öğe** **Ekle.** > </span><span class="sxs-lookup"><span data-stu-id="ab18e-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![İç içe bağlamsal menü: Solution Explorer'da Kaynaklar için bağlamsal bir menü açıktır.](localization/_static/newi.png)

2. <span data-ttu-id="ab18e-187">Arama **yüklü şablonlar** kutusuna "kaynak" girin ve dosyayı adlandırın.</span><span class="sxs-lookup"><span data-stu-id="ab18e-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Yeni Öğe iletişim kutusu ekle](localization/_static/res.png)

3. <span data-ttu-id="ab18e-189">**Ad** sütununa anahtar değerini (yerel dizeyi) ve **Değer** sütununa çevrilmiş dizeyi girin.</span><span class="sxs-lookup"><span data-stu-id="ab18e-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx dosyası (İspanyolca için hoş geldiniz kaynak dosyası) Ad sütununda Merhaba sözcüğü ve Değer sütununda Hola (İspanyolca Merhaba) sözcüğü ile](localization/_static/hola.png)

    <span data-ttu-id="ab18e-191">Visual Studio *Welcome.es.resx* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Hoş Geldiniz İspanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="ab18e-193">Kaynak dosyası adlandırma</span><span class="sxs-lookup"><span data-stu-id="ab18e-193">Resource file naming</span></span>

<span data-ttu-id="ab18e-194">Kaynaklar, sınıflarının tam tür adı eksi derleme adı için adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="ab18e-195">Örneğin, ana derlemesi sınıf `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` için olan bir projedeki Bir Fransızca kaynak *Başlangıç.fr.resx*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="ab18e-196">Sınıf `LocalizationWebsite.Web.Controllers.HomeController` için bir kaynak *Controllers.HomeController.fr.resx*adlı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ab18e-197">Hedeflediğiniz sınıfın ad alanı derleme adı ile aynı değilse, tam tür adı gerekir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="ab18e-198">Örneğin, örnek projede türü `ExtraNamespace.Tools` için bir kaynak *ExtraNamespace.Tools.fr.resx*adlı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="ab18e-199">Örnek projede, `ConfigureServices` yöntem "Kaynaklar" `ResourcesPath` olarak ayarlar, bu nedenle ev denetleyicisinin Fransızca kaynak dosyasıiçin proje göreli yolu *Kaynaklar/Controllers.HomeController.fr.resx'tir.*</span><span class="sxs-lookup"><span data-stu-id="ab18e-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ab18e-200">Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="ab18e-201">Ev denetleyicisi için yol *Kaynaklar/Denetleyiciler/HomeController.fr.resx*olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="ab18e-202">Seçeneği kullanmazsanız, `ResourcesPath` *.resx* dosyası proje temel dizinine gider.</span><span class="sxs-lookup"><span data-stu-id="ab18e-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="ab18e-203">Kaynak `HomeController` dosyası *controllers.HomeController.fr.resx*adlı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="ab18e-204">Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="ab18e-205">Kaynak adı</span><span class="sxs-lookup"><span data-stu-id="ab18e-205">Resource name</span></span> | <span data-ttu-id="ab18e-206">Nokta veya yol adlandırma</span><span class="sxs-lookup"><span data-stu-id="ab18e-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="ab18e-207">Kaynaklar/Denetleyiciler.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ab18e-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="ab18e-208">Nokta</span><span class="sxs-lookup"><span data-stu-id="ab18e-208">Dot</span></span>  |
| <span data-ttu-id="ab18e-209">Kaynaklar/Denetleyiciler/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ab18e-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="ab18e-210">Yol</span><span class="sxs-lookup"><span data-stu-id="ab18e-210">Path</span></span> |
|    |     |

<span data-ttu-id="ab18e-211">Razor görünümlerinde kullanan `@inject IViewLocalizer` kaynak dosyaları da benzer bir desen izler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="ab18e-212">Görünüm için kaynak dosyası nokta adlandırma veya yol adlandırma kullanarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="ab18e-213">Jilet görünümü kaynak dosyaları ilişkili görünüm dosyalarının yolunu taklit.</span><span class="sxs-lookup"><span data-stu-id="ab18e-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="ab18e-214">"Kaynaklar" `ResourcesPath` olarak ayarladiğimizı varsayarsak, *Görünümler/Ana Sayfa/About.cshtml* görünümüyle ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="ab18e-215">Kaynaklar/Görünümler/Ana Sayfa/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ab18e-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="ab18e-216">Kaynaklar/Görünümler.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="ab18e-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="ab18e-217">Seçeneği kullanmazsanız, `ResourcesPath` görünüm için *.resx* dosyası görünümle aynı klasörde bulunur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="ab18e-218">Rootnamespaceattribute</span><span class="sxs-lookup"><span data-stu-id="ab18e-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="ab18e-219">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="ab18e-220">Bu, bir projenin adı geçerli bir .NET tanımlayıcısı olmadığında oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="ab18e-221">Örneğin, `my-project-name.csproj` kök ad alanını `my_project_name` ve bu `my-project-name` hataya yol açan derleme adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="ab18e-222">Bir derlemenin kök ad alanı derleme adından farklıysa:</span><span class="sxs-lookup"><span data-stu-id="ab18e-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="ab18e-223">Yerelleştirme varsayılan olarak çalışmıyor.</span><span class="sxs-lookup"><span data-stu-id="ab18e-223">Localization does not work by default.</span></span>
* <span data-ttu-id="ab18e-224">Yerelleştirme, derleme içinde kaynakların aranması nedeniyle başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="ab18e-225">`RootNamespace`yürütme işlemi için kullanılamayan bir yapı zamanı değeridir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="ab18e-226">Farklı `RootNamespace` `AssemblyName`ise, *AssemblyInfo.cs* (parametre değerleri gerçek değerlerle değiştirilir) aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="ab18e-227">Önceki kod, resx dosyalarının başarılı bir şekilde çözümlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="ab18e-228">Kültür geri dönüş davranışı</span><span class="sxs-lookup"><span data-stu-id="ab18e-228">Culture fallback behavior</span></span>

<span data-ttu-id="ab18e-229">Bir kaynak ararken, yerelleştirme "kültür geri dönüş" meşgul.</span><span class="sxs-lookup"><span data-stu-id="ab18e-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="ab18e-230">İstenen kültürden başlayarak, bulunamazsa, o kültürün ana kültürüne geri döner.</span><span class="sxs-lookup"><span data-stu-id="ab18e-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="ab18e-231">Bir kenara, [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="ab18e-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="ab18e-232">Bu genellikle (ama her zaman değil) ISO gelen ulusal signifier kaldırılması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="ab18e-233">Örneğin, Meksika'da konuşulan İspanyolca lehçesi "es-MX".</span><span class="sxs-lookup"><span data-stu-id="ab18e-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="ab18e-234">Bu ebeveyn "es"&mdash;İspanyolca olmayan herhangi bir ülkeye özgü vardır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="ab18e-235">Sitenizin "fr-CA" kültürünü kullanarak bir "Hoş Geldiniz" kaynağı için bir istek aldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="ab18e-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="ab18e-236">Yerelleştirme sistemi sırayla aşağıdaki kaynakları arar ve ilk eşleşmeyi seçer:</span><span class="sxs-lookup"><span data-stu-id="ab18e-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="ab18e-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="ab18e-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="ab18e-238">*Hoşgeldiniz.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="ab18e-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="ab18e-239">*Welcome.resx* (eğer `NeutralResourcesLanguage` "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="ab18e-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="ab18e-240">Örnek olarak, ".fr" kültür atası kaldırırsanız ve kültür Fransızca olarak ayarlanmışsa, varsayılan kaynak dosyası okunur ve dizeleri yerelleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="ab18e-241">Kaynak yöneticisi, hiçbir şey istediğiniz kültürünüzü karşılamadığında varsayılan veya geri dönüş kaynağı belirler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="ab18e-242">İstenen kültür için bir kaynak eksikken anahtarı döndürmek istiyorsanız, varsayılan bir kaynak dosyanız olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="ab18e-243">Visual Studio ile kaynak dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="ab18e-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="ab18e-244">Visual Studio'da dosya adında kültürü olmayan bir kaynak dosyası oluşturursanız (örneğin, *Welcome.resx),* Visual Studio her dize için bir özelliği olan bir C# sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="ab18e-245">ASP.NET Core'da genelde böyle olmasını istemezsin.</span><span class="sxs-lookup"><span data-stu-id="ab18e-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="ab18e-246">Genellikle varsayılan *.resx* kaynak dosyanız (kültür adı olmayan bir *.resx* dosyası) yoktur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="ab18e-247">*.resx* dosyasını bir kültür adı içeren oluşturmanızı öneririz (örneğin *Welcome.fr.resx).*</span><span class="sxs-lookup"><span data-stu-id="ab18e-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="ab18e-248">Bir kültür *adındabir .resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyasını oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="ab18e-249">Diğer kültürleri ekleme</span><span class="sxs-lookup"><span data-stu-id="ab18e-249">Add other cultures</span></span>

<span data-ttu-id="ab18e-250">Her dil ve kültür birleşimi (varsayılan dil dışında) benzersiz bir kaynak dosyası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="ab18e-251">ISO dil kodlarının dosya adının bir parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel dosyalar için kaynak dosyaları oluşturursunuz (örneğin, **en-us**, **fr-ca**, ve **en-gb).**</span><span class="sxs-lookup"><span data-stu-id="ab18e-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="ab18e-252">Bu ISO *kodları, Welcome.es-MX.resx* (İspanyolca/Meksika) gibi dosya adı ve *.resx* dosya uzantısı arasına yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="ab18e-253">Her istek için dili/kültürü seçmek için bir strateji uygulayın</span><span class="sxs-lookup"><span data-stu-id="ab18e-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="ab18e-254">Yerelleştirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ab18e-254">Configure localization</span></span>

<span data-ttu-id="ab18e-255">Yerelleştirme `Startup.ConfigureServices` yönteminde yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="ab18e-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="ab18e-256">`AddLocalization`Yerelleştirme hizmetlerini hizmet kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="ab18e-257">Yukarıdaki kod da "Kaynaklar" için kaynak yolunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="ab18e-258">`AddViewLocalization`Yerelleştirilmiş görünüm dosyaları için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="ab18e-259">Bu örnek görünümünde yerelleştirme görünüm dosyası sonekine dayanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="ab18e-260">Örneğin *Index.fr.cshtml* dosyasındaki "fr"</span><span class="sxs-lookup"><span data-stu-id="ab18e-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="ab18e-261">`AddDataAnnotationsLocalization`Soyutlamalar aracılığıyla `IStringLocalizer` `DataAnnotations` yerelleştirilmiş doğrulama iletileri için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="ab18e-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="ab18e-262">Yerelleştirme ara ware</span><span class="sxs-lookup"><span data-stu-id="ab18e-262">Localization middleware</span></span>

<span data-ttu-id="ab18e-263">Bir istekteki geçerli kültür, yerelleştirme [Middleware'inde](xref:fundamentals/middleware/index)ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="ab18e-264">`Startup.Configure` Yöntemde yerelleştirme ara ware etkindir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="ab18e-265">Yerelleştirme ara ware istek kültürünü kontrol edebilir herhangi bir ara yazılım `app.UseMvcWithDefaultRoute()`önce yapılandırılmalıdır (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="ab18e-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ab18e-266">`UseRequestLocalization`bir `RequestLocalizationOptions` nesneyi başharfe alar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="ab18e-267">Her `RequestCultureProvider` istekte `RequestLocalizationOptions` liste numaralandırılır ve istek kültürünü başarıyla belirleyebilen ilk sağlayıcı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="ab18e-268">Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="ab18e-269">Varsayılan liste en çok belirli den en az özel gider.</span><span class="sxs-lookup"><span data-stu-id="ab18e-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="ab18e-270">Makalenin ilerleyen saatlerinde, siparişi nasıl değiştirebileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğinizi göreceğiz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="ab18e-271">Sağlayıcılardan hiçbiri istek kültürünü belirleyemezse, bu `DefaultRequestCulture` durum kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="ab18e-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ab18e-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="ab18e-273">Bazı uygulamalar [kültür ve ui kültürünü](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)ayarlamak için bir sorgu dizesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="ab18e-274">Çerez veya Kabul Dili üstbilgisi yaklaşımını kullanan uygulamalar için, URL'ye sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="ab18e-275">Varsayılan olarak, `QueryStringRequestCultureProvider` `RequestCultureProvider` listedeki ilk yerelleştirme sağlayıcısı olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="ab18e-276">Sorgu dize parametrelerini `culture` ve `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="ab18e-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="ab18e-277">Aşağıdaki örnek, Belirli bir kültürü (dil ve bölge) İspanyolca/Meksika olarak belirlemektedir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="ab18e-278">Yalnızca iki (veya),`culture` `ui-culture`sorgu dize sağlayıcısından birini geçerseniz, geçiş yaptığınız değeri kullanarak her iki değeri de ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="ab18e-279">Örneğin, sadece kültür ayarı hem `Culture` ve `UICulture`hem de ayarlar:</span><span class="sxs-lookup"><span data-stu-id="ab18e-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="ab18e-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="ab18e-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="ab18e-281">Üretim uygulamaları genellikle ASP.NET Çekirdek kültür çerezi ile kültürü ayarlamak için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="ab18e-282">Bir `MakeCookieValue` çerez oluşturmak için yöntemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="ab18e-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="ab18e-283">Kullanıcının `CookieRequestCultureProvider` `DefaultCookieName` tercih edilen kültür bilgilerini izlemek için kullanılan varsayılan çerez adını döndürür.</span><span class="sxs-lookup"><span data-stu-id="ab18e-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="ab18e-284">Varsayılan çerez adı. `.AspNetCore.Culture`</span><span class="sxs-lookup"><span data-stu-id="ab18e-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="ab18e-285">Çerez biçimi `c=%LANGCODE%|uic=%LANGCODE%`, `c` nerede `Culture` `uic` ve, `UICulture`örneğin:</span><span class="sxs-lookup"><span data-stu-id="ab18e-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="ab18e-286">Yalnızca kültür bilgileri ve Kullanıcı Arabirimi kültüründen birini belirtirseniz, belirtilen kültür hem kültür bilgisi hem de Kullanıcı Arabirimi kültürü için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="ab18e-287">Kabul Dili HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="ab18e-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="ab18e-288">[Kabul Dili üstbilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmek için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="ab18e-289">Bu ayar, tarayıcının ne göndermek üzere ayarlandığını veya temel işletim sisteminden devralındığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="ab18e-290">Tarayıcı isteğinden kabul dili HTTP üstbilgisi, kullanıcının tercih ettiği dili algılamanın yanılmaz [bir](https://www.w3.org/International/questions/qa-lang-priorities.en.php)yolu değildir (bkz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="ab18e-291">Bir üretim uygulaması, bir kullanıcının kültür seçimini özelleştirmesi için bir yol içermelidir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="ab18e-292">IE'de Kabul Dili HTTP üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="ab18e-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="ab18e-293">Dişli simgesinden **Internet Seçenekleri'ne**dokunun.</span><span class="sxs-lookup"><span data-stu-id="ab18e-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="ab18e-294">**Dillere**dokunun.</span><span class="sxs-lookup"><span data-stu-id="ab18e-294">Tap **Languages**.</span></span>

    ![İnternet Seçenekleri](localization/_static/lang.png)

3. <span data-ttu-id="ab18e-296">**Dil Tercihlerini Ayarla'ya**dokunun.</span><span class="sxs-lookup"><span data-stu-id="ab18e-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="ab18e-297">**Dil ekle'ye**dokunun.</span><span class="sxs-lookup"><span data-stu-id="ab18e-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="ab18e-298">Dili ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ab18e-298">Add the language.</span></span>

6. <span data-ttu-id="ab18e-299">Dile dokunun, ardından **Yukarı Taşı'ya**dokunun.</span><span class="sxs-lookup"><span data-stu-id="ab18e-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="ab18e-300">İçerik-Dil HTTP üstbilgisi</span><span class="sxs-lookup"><span data-stu-id="ab18e-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="ab18e-301">[İçerik-Dil](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) varlık üstbilgisi:</span><span class="sxs-lookup"><span data-stu-id="ab18e-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="ab18e-302">Dinleyicilere yönelik dil(ler) tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="ab18e-303">Kullanıcının kendi tercih ettiği dile göre ayırt etmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="ab18e-304">Varlık üstleleri hem HTTP isteklerinde hem de yanıtlarında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="ab18e-305">Üstbilgi `Content-Language` özelliği `ApplyCurrentCultureToResponseHeaders`ayarlayarak eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="ab18e-306">Üstbilgi `Content-Language` ekleme:</span><span class="sxs-lookup"><span data-stu-id="ab18e-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="ab18e-307">RequestLocalizationMiddleware ile `Content-Language` üstbilgi ayarlamak için `CurrentUICulture`izin verir .</span><span class="sxs-lookup"><span data-stu-id="ab18e-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="ab18e-308">Yanıt üstbilgisini `Content-Language` açıkça ayarlama gereksinimini ortadan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="ab18e-309">Özel bir sağlayıcı kullanma</span><span class="sxs-lookup"><span data-stu-id="ab18e-309">Use a custom provider</span></span>

<span data-ttu-id="ab18e-310">Müşterilerinizin dillerini ve kültürlerini veritabanlarınızda depolamasına izin vermek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="ab18e-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="ab18e-311">Kullanıcı için bu değerleri aramak için bir sağlayıcı yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab18e-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="ab18e-312">Aşağıdaki kod, özel bir sağlayıcının nasıl ekleyeceğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="ab18e-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="ab18e-313">Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın. `RequestLocalizationOptions`</span><span class="sxs-lookup"><span data-stu-id="ab18e-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="ab18e-314">Kültürü programlı olarak ayarlayın</span><span class="sxs-lookup"><span data-stu-id="ab18e-314">Set the culture programmatically</span></span>

<span data-ttu-id="ab18e-315">[GitHub](https://github.com/aspnet/entropy) bu örnek **Localization.StarterWeb** proje ayarlamak `Culture`için UI içerir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="ab18e-316">*Views/Shared/_SelectLanguagePartial.cshtml* dosyası, desteklenen kültürler listesinden kültürü seçmenize olanak tanır:</span><span class="sxs-lookup"><span data-stu-id="ab18e-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="ab18e-317">*Görünümler/Paylaşılan/_SelectLanguagePartial.cshtml* dosyası düzen `footer` dosyasının bölümüne eklenir, böylece tüm görünümler için kullanılabilir olacaktır:</span><span class="sxs-lookup"><span data-stu-id="ab18e-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="ab18e-318">Yöntem `SetLanguage` kültür çerezayarlar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="ab18e-319">Bu proje için örnek kod için *_SelectLanguagePartial.cshtml* takamazsınız.</span><span class="sxs-lookup"><span data-stu-id="ab18e-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="ab18e-320">[GitHub'daki](https://github.com/aspnet/entropy) **Localization.StarterWeb** projesi, Bağımlılık `RequestLocalizationOptions` [Enjeksiyonu](dependency-injection.md) kabından bir Razor kısmi akışı için koda sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="ab18e-321">Yol verilerini ve sorgu dizelerini model bağlama</span><span class="sxs-lookup"><span data-stu-id="ab18e-321">Model binding route data and query strings</span></span>

<span data-ttu-id="ab18e-322">Bkz. [Model bağlama rota verileri ve sorgu dizeleri Küreselleşme davranışı.](xref:mvc/models/model-binding#glob)</span><span class="sxs-lookup"><span data-stu-id="ab18e-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="ab18e-323">Küreselleşme ve yerelleştirme terimleri</span><span class="sxs-lookup"><span data-stu-id="ab18e-323">Globalization and localization terms</span></span>

<span data-ttu-id="ab18e-324">Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan alakalı karakter kümelerinin temel bir şekilde anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını da gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="ab18e-325">Tüm bilgisayarlar metni sayı (kod) olarak depolamak la birlikte, farklı sistemler farklı sayılar kullanarak aynı metni depolar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="ab18e-326">Yerelleştirme işlemi, uygulama kullanıcı arabiriminin (UI) belirli bir kültür/yerel alan için çevrilmesi anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="ab18e-327">[Yerelleştirilebilirlik,](/dotnet/standard/globalization-localization/localizability-review) küreselleştirilmiş bir uygulamanın yerelleştirme için hazır olduğunu doğrulamak için bir ara işlemdir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="ab18e-328">Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) `<languagecode2>-<country/regioncode2>`biçimi `<languagecode2>` , nerede `<country/regioncode2>` dil kodu ve alt kültür kodudur.</span><span class="sxs-lookup"><span data-stu-id="ab18e-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="ab18e-329">Örneğin, `es-CL` İspanyolca (Şili), `en-US` İngilizce (AMERIKA Birleşik `en-AU` Devletleri) ve İngilizce (Avustralya) için.</span><span class="sxs-lookup"><span data-stu-id="ab18e-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="ab18e-330">[RFC 4646,](https://www.ietf.org/rfc/rfc4646.txt) bir dille ilişkili bir ISO 639 iki harfli küçük harfli kültür kodu ile bir ülke veya bölgeyle ilişkili bir ISO 3166 iki harfli büyük harfli alt kültür kodunun birleşimidir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="ab18e-331">[Bkz. Dil Kültür Adı](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="ab18e-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="ab18e-332">Uluslararasılaşma genellikle "I18N" olarak kısaltılır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="ab18e-333">Kısaltma ilk ve son harfleri ve aralarındaki harf sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harf sayısını temsil alır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="ab18e-334">Aynı şey Küreselleşme (G11N) ve Yerelleştirme (L10N) için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ab18e-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="ab18e-335">Terim:</span><span class="sxs-lookup"><span data-stu-id="ab18e-335">Terms:</span></span>

* <span data-ttu-id="ab18e-336">Küreselleşme (G11N): Bir uygulamanın farklı dilleri ve bölgeleri desteklemesi süreci.</span><span class="sxs-lookup"><span data-stu-id="ab18e-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="ab18e-337">Yerelleştirme (L10N): Belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.</span><span class="sxs-lookup"><span data-stu-id="ab18e-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="ab18e-338">Uluslararasılaşma (I18N): Küreselleşmeyi ve yerelleşmeyi açıklar.</span><span class="sxs-lookup"><span data-stu-id="ab18e-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="ab18e-339">Kültür: Bu bir dil ve isteğe bağlı olarak, bir bölge.</span><span class="sxs-lookup"><span data-stu-id="ab18e-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="ab18e-340">Tarafsız kültür: Belirli bir dile sahip, ancak bölgeye sahip olmayan bir kültür.</span><span class="sxs-lookup"><span data-stu-id="ab18e-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="ab18e-341">(örneğin "en", "es")</span><span class="sxs-lookup"><span data-stu-id="ab18e-341">(for example "en", "es")</span></span>
* <span data-ttu-id="ab18e-342">Özel kültür: Belirli bir dil ve bölgeye sahip bir kültür.</span><span class="sxs-lookup"><span data-stu-id="ab18e-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="ab18e-343">(örneğin "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="ab18e-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="ab18e-344">Üst kültür: Belirli bir kültürü içeren tarafsız kültür.</span><span class="sxs-lookup"><span data-stu-id="ab18e-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="ab18e-345">(örneğin, "en" "en-US" ve "en-GB" ana kültürüdür)</span><span class="sxs-lookup"><span data-stu-id="ab18e-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="ab18e-346">Yerel: Yerel bir kültürle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ab18e-347">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ab18e-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="ab18e-348">[Localization.StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) makalede kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="ab18e-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="ab18e-349">Küreselleştirme ve yerelleştirme .NET uygulamaları</span><span class="sxs-lookup"><span data-stu-id="ab18e-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="ab18e-350">.resx Dosyalarındaki Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ab18e-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="ab18e-351">Microsoft Çok Dilli Uygulama Araç Seti</span><span class="sxs-lookup"><span data-stu-id="ab18e-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="ab18e-352">Generics & Yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="ab18e-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)
