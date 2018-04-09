---
uid: mvc/overview/older-versions-1/controllers-and-routing/understanding-action-filters-vb
title: Eylem filtreleri (VB) Anlama | Microsoft Docs
author: microsoft
description: Bu öğretici eylem filtrelerini açıklamak için hedefidir. Bir eylem filtresi bir denetleyici eylemi--ya da tüm bir denetleyiciye uygulanan bir özniteliktir...
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/16/2008
ms.topic: article
ms.assetid: e83812f2-c53e-4a43-a7c1-d64c59ecf694
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/understanding-action-filters-vb
msc.type: authoredcontent
ms.openlocfilehash: 2796b67cba6a2ddaee7a006a170dfb7e5ff89888
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="understanding-action-filters-vb"></a>Eylem filtreleri (VB) Anlama
====================
tarafından [Microsoft](https://github.com/microsoft)

[PDF indirin](http://download.microsoft.com/download/e/f/3/ef3f2ff6-7424-48f7-bdaa-180ef64c3490/ASPNET_MVC_Tutorial_14_VB.pdf)

> Bu öğretici eylem filtrelerini açıklamak için hedefidir. Bir eylem filtresi bir denetleyici eylemi--veya eylem yürütüldüğü biçimini değiştirir tüm controller--uygulayabileceğiniz bir özniteliktir.


## <a name="understanding-action-filters"></a>Eylem filtreleri anlama

Bu öğretici eylem filtrelerini açıklamak için hedefidir. Bir eylem filtresi bir denetleyici eylemi--veya eylem yürütüldüğü biçimini değiştirir tüm controller--uygulayabileceğiniz bir özniteliktir. ASP.NET MVC çerçevesi birkaç eylem filtrelerini içerir:

- OutputCache – Bu eylem filtresi belirtilen bir süre için bir denetleyici eylemi çıktısını önbelleğe alır.
- HandleError – Bu eylem filtresi bir denetleyici eylemi yürüten tetiklenir hatalarını ele alır.
- Yetki – belirli bir kullanıcı veya rol için erişimi kısıtlamak Bu eylem filtresi sağlar.

Kendi özel eylem filtrelerini de oluşturabilirsiniz. Örneğin, bir özel kimlik doğrulama sistemi uygulamak için bir özel eylem filtresi oluşturmak isteyebilirsiniz. Ya da bir denetleyici eylemi tarafından döndürülen görünüm verileri değiştiren bir eylem filtresi oluşturmak isteyebilirsiniz.

Bu öğreticide, sıfırdan bir eylem filtresi oluşturmak öğrenin. Visual Studio çıkış penceresine farklı bir eylem işleme aşamalarını günlüklerini günlük eylem filtresi oluşturuyoruz.

### <a name="using-an-action-filter"></a>Bir eylem filtresini kullanma

Bir eylem filtresi bir özniteliktir. Tek tek denetleyici eylem veya tüm denetleyicisi için çoğu eylemi filtre uygulayabilirsiniz.

Örneğin, veri denetleyici listeleme 1 adlı bir eylem sunan `Index()` geçerli saati döndürür. Bu eylem ile donatılmış `OutputCache` eylem filtresi. Bu filtre 10 saniye için önbelleğe alınacak eylemi tarafından döndürülen değer neden olur.

**Kod 1 – `Controllers\DataController.vb`**

[!code-vb[Main](understanding-action-filters-vb/samples/sample1.vb)]

Art arda çağırma varsa `Index()` URL/Data/dizin tarayıcınızın adres çubuğuna girerek ve yenileme basarsa eylem düğmesi birden çok kez aynı anda 10 saniye görürsünüz. Çıktısını `Index()` eylem 10 (bkz. Şekil 1) saniye için önbelleğe alınır.


[![Önbelleğe alınan zaman](understanding-action-filters-vb/_static/image2.png)](understanding-action-filters-vb/_static/image1.png)

**Şekil 01**: zaman önbelleğe ([tam boyutlu görüntüyü görüntülemek için tıklatın](understanding-action-filters-vb/_static/image3.png))


Bir tek eylem filtresi – listeleme 1'deki `OutputCache` eylem filtresi – uygulanan `Index()` yöntemi. Gerekirse, birden çok eylem filtrelerini aynı eylem uygulayabilirsiniz. Örneğin, her ikisini de uygulamak isteyebilirsiniz `OutputCache` ve `HandleError` aynı eylem için eylem filtreleri.

1. listeleme `OutputCache` eylem filtresi uygulanan `Index()` eylem. Bu öznitelik için geçerli olabilir `DataController` sınıfının kendisini. Bu durumda, denetleyici tarafından kullanıma sunulan herhangi bir eylem tarafından döndürülen sonuç 10 saniye için önbelleğe.

### <a name="the-different-types-of-filters"></a>Farklı türde filtreler

ASP.NET MVC çerçevesi filtre dört farklı türlerini destekler:

1. Yetkilendirme filtreleri – uygulayan `IAuthorizationFilter` özniteliği.
2. Eylem filtreleri – uygulayan `IActionFilter` özniteliği.
3. Sonuç filtreleri – uygulayan `IResultFilter` özniteliği.
4. Özel durum filtreleri – uygulayan `IExceptionFilter` özniteliği.

Filtreler, yukarıda listelenen sırada yürütülür. Örneğin, yetkilendirme filtreleri her zaman eylem filtrelerden önce yürütülür ve özel durum filtreleri filtre diğer her tür sonra her zaman yürütülür.

Yetkilendirme filtreleri, kimlik doğrulama ve yetkilendirme denetleyici eylemleri uygulamak için kullanılır. Örneğin, yetkilendirme filtresi bir yetkilendirme filtresi örneğidir.

Eylem filtreleri önce ve denetleyici Eylem yürütüldükten sonra yürütülür mantık içerir. Bir denetleyici eylemi döndürür görünüm verileri değiştirmek için bir eylem filtresi örneği için kullanabilirsiniz.

Sonuç filtreleri önce ve bir görünüm sonucu yürütüldükten sonra yürütülür mantık içerir. Örneğin, bir görünüm sonucu değiştirmek isteyebilirsiniz görünümü tarayıcıya işlenmeden önce sağ.

Özel durum filtreleri çalıştırmak için filtre son türüdür. Denetleyici eylem veya denetleyici eylem sonuçlarını tarafından oluşturulan hataları işlemek için bir özel durum filtresini kullanabilirsiniz. Özel durum filtreleri, hataları günlüğe kaydetmek için de kullanabilirsiniz.

Farklı her filtre türü belirli bir sırada yürütülür. Aynı türde filtrelerinin yürütüldüğü sırayı denetlemek istiyorsanız, filtrenin sırası özelliği ayarlayabilirsiniz.

Tüm eylem filtrelerini temel sınıfı olan `System.Web.Mvc.FilterAttribute` sınıfı. Belirli bir filtre türünü uygulamak istiyorsanız, temel filtre sınıfından devralan ve bir veya daha fazla IAuthorizationFilter, IActionFilter IResultFilter uygulayan bir sınıf oluşturmak için gereken veya ExceptionFilter arabirimleri.

### <a name="the-base-actionfilterattribute-class"></a>Temel ActionFilterAttribute sınıfı

Bir özel eylem filtresi uygulamak kolaylaştırmak için bir taban ASP.NET MVC çerçevesi içeren `ActionFilterAttribute` sınıfı. Bu sınıfın her ikisi de uygulayan `IActionFilter` ve `IResultFilter` arabirimleri ve devraldığı `Filter` sınıfı.

Burada terminoloji tamamen tutarlı değil. Teknik olarak, ActionFilterAttribute sınıfından devralan bir sınıf bir eylem filtresi ve bir sonuç Filtresi ' dir. Ancak, gevşek anlamda word eylem filtresi herhangi türde bir ASP.NET MVC çerçevesi filtrede başvurmak için kullanılır.

Temel ActionFilterAttribute sınıfındaki geçersiz kılabilirsiniz aşağıdaki yöntemleri içerir:

- Bir denetleyici eylemi yürütülmeden önce OnActionExecuting – bu yöntem çağrılır.
- Denetleyici Eylem yürütüldükten sonra OnActionExecuted – bu yöntem çağrılır.
- Denetleyici eylem sonucu yürütülmeden önce OnResultExecuting – bu yöntem çağrılır.
- Denetleyici eylem sonucu yürütüldükten sonra OnResultExecuted – bu yöntem çağrılır.

Sonraki bölümde, farklı bu yöntemlerin her biri uygulamak nasıl göreceğiz.

### <a name="creating-a-log-action-filter"></a>Günlük eylem filtresi oluşturma

Özel eylem filtresinin nasıl oluşturulacağına göstermek için bir denetleyici eylemi Visual Studio çıkış penceresi için işlem aşamaları günlüklerini bir özel eylem filtresi oluşturacağız. Bizim `LogActionFilter` listeleme 2'de yer alır.

**Kod 2 – `ActionFilters\LogActionFilter.vb`**

[!code-vb[Main](understanding-action-filters-vb/samples/sample2.vb)]

Listeleme 2'de, `OnActionExecuting()`, `OnActionExecuted()`, `OnResultExecuting()`, ve `OnResultExecuted()` tüm yöntemleri çağırmak `Log()` yöntemi. Yöntemin adı ve geçerli rota verilerini geçirilir `Log()` yöntemi. `Log()` Yöntemi için Visual Studio çıktı penceresinde bir ileti yazar (bkz: Şekil 2).


[![Visual Studio çıkış penceresine yazma](understanding-action-filters-vb/_static/image5.png)](understanding-action-filters-vb/_static/image4.png)

**Şekil 02**: Visual Studio çıkış penceresine yazma ([tam boyutlu görüntüyü görüntülemek için tıklatın](understanding-action-filters-vb/_static/image6.png))


Giriş denetleyicisi listeleme 3'te, nasıl bir tüm denetleyicinin sınıf günlük eylem filtresi uygulayabilirsiniz gösterilmektedir. Her giriş denetleyici tarafından kullanıma sunulan eylemlerden herhangi birini çağrılır – ya da `Index()` yöntemi veya `About()` yöntemi – eylem için Visual Studio çıkış penceresi günlüğe kaydedilen işleme aşamalarını.

**Kod 3 – `Controllers\HomeController.vb`**

[!code-vb[Main](understanding-action-filters-vb/samples/sample3.vb)]

### <a name="summary"></a>Özet

Bu öğreticide, ASP.NET MVC eylem filtrelerini tanıtıldı. Dört farklı türde filtreler hakkında öğrenilen: Yetkilendirme filtreleri, eylem filtreleri, sonuç filtreleri ve özel durum filtreleri. Base hakkında da öğrenilen `ActionFilterAttribute` sınıfı.

Son olarak, bir basit eylem filtresi uygulamak nasıl öğrendiniz. Visual Studio çıkış penceresi için bir denetleyici eylemi işleme aşamalarını günlüklerini günlük eylem filtresi oluşturduk.

> [!div class="step-by-step"]
> [Önceki](asp-net-mvc-routing-overview-vb.md)
> [sonraki](improving-performance-with-output-caching-vb.md)
