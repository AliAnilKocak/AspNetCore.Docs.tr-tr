---
uid: mvc/overview/older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-vb
title: "ASP.NET MVC yönlendirmeye genel bakış (VB) | Microsoft Docs"
author: StephenWalther
description: "Bu öğreticide, ASP.NET MVC çerçevesi tarayıcı isteklerini denetleyici eylemleri için nasıl eşlendiğini Stephen Walther gösterir."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/19/2008
ms.topic: article
ms.assetid: 4bc8d19a-80f1-44b4-adbf-95ed22d691ca
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/asp-net-mvc-routing-overview-vb
msc.type: authoredcontent
ms.openlocfilehash: 1e4c74e61b1a0d5f5020154756e34dd2fa507034
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-mvc-routing-overview-vb"></a>ASP.NET MVC yönlendirmeye genel bakış (VB)
====================
tarafından [Stephen Walther](https://github.com/StephenWalther)

> Bu öğreticide, ASP.NET MVC çerçevesi tarayıcı isteklerini denetleyici eylemleri için nasıl eşlendiğini Stephen Walther gösterir.


Bu öğreticide adlı her ASP.NET MVC uygulaması için bir önemli özellik sunulan *ASP.NET yönlendirme*. ASP.NET yönlendirme modülü, belirli MVC denetleyici eylemleri için gelen tarayıcı istekleri eşlemek için sorumludur. Bu öğreticide sonuna tarafından standart yol tablosu istekleri denetleyici eylemleri için nasıl eşlendiğini anlayacaksınız.

## <a name="using-the-default-route-table"></a>Varsayılan rota tablosu kullanma

Yeni bir ASP.NET MVC uygulaması oluşturduğunuzda uygulama ASP.NET yönlendirmeyi kullanmak için zaten yapılandırıldı. ASP.NET yönlendirme iki yerde kurulur.

İlk olarak, ASP.NET yönlendirme uygulamanızın Web yapılandırma dosyasında (Web.config dosyası) etkindir. Yapılandırma dosyasında yönlendirme için uygun olan dört bölüm vardır: system.web.httpModules bölümü, system.web.httpHandlers bölümü, system.webserver.modules bölüm ve system.webserver.handlers bölümü. Bu bölümler yönlendirme artık çalışmaz çünkü bu bölümleri silmemeye dikkat edin.

İkinci ve daha da önemlisi, uygulamanın Global.asax dosyasında bir yol tablosu oluşturulur. Global.asax dosyası, ASP.NET uygulama yaşam döngüsü olayları için olay işleyicilerini içeren özel bir dosyadır. Yol tablosu sırasında uygulama başlangıç olayı oluşturulur.

Listeleme 1 dosyasında bir ASP.NET MVC uygulaması için varsayılan Global.asax dosyası içerir.

**1 - Global.asax.vb listeleme**

[!code-vb[Main](asp-net-mvc-routing-overview-vb/samples/sample1.vb)]

Bir MVC uygulaması ilk kez başlatıldığında, uygulama\_Start() yöntemi çağrılır. Bu yöntem, sırasıyla RegisterRoutes() yöntemini çağırır. RegisterRoutes() yöntemi yol tablosu oluşturur.

Varsayılan rota tablosu (varsayılan olarak adlandırılır) tek bir yol içeriyor. Bir denetleyici adı, ikinci bir denetleyici eylemi için bir URL kesimi ve üçüncü segment adlı bir parametre için bir URL ilk bölümü varsayılan rotayı eşler **kimliği**.

Web tarayıcınızın adres çubuğuna aşağıdaki URL'yi girin düşünün:

/ Home/Index/3

Varsayılan rota bu URL için aşağıdaki parametreleri eşler:

- Denetleyici giriş =

- Eylem dizini =

- ID = 3

URL /Home/dizin/3 istediğinizde, aşağıdaki kod çalıştırılır:

HomeController.Index(3)

Varsayılan yol, tüm üç parametre için varsayılan değerleri içerir. Bir denetleyici girmezseniz, denetleyici parametre değerine varsayılanları **giriş**. Bir eylem girmezseniz, eylem parametresi değeri varsayılan olarak **dizin**. Son olarak, bir kimlik girmezseniz, boş bir dize olarak ID parametresi varsayılan olarak ayarlanır.

Varsayılan rota URL'leri denetleyici eylemleri için nasıl eşlendiğini birkaç örnekleri bakalım. Tarayıcınızın adres çubuğuna aşağıdaki URL'yi girin düşünün:

/ Giriş

Varsayılan rota parametre varsayılanlarını nedeniyle bu URL girerek listeleme çağrılacak 2'de HomeController sınıfının İNDİS() yöntemi neden olur.

**2 - HomeController.vb listeleme**

[!code-vb[Main](asp-net-mvc-routing-overview-vb/samples/sample2.vb)]

Listeleme 2'de HomeController sınıfı kimliği adlı tek bir parametre kabul eden İNDİS() adlı bir yöntem içerir. URL /Home değeriyle kimliği parametresinin değeri olarak bir şey çağrılacak İNDİS() yöntemi neden olur.

MVC çerçevesi, denetleyici eylemleri çağırır yöntemi nedeniyle, URL /Home listeleme 3'te HomeController sınıfının İNDİS() yöntemi de eşleşir.

**Kod 3 - HomeController.vb (dizin eylem hiçbir parametre ile)**

[!code-vb[Main](asp-net-mvc-routing-overview-vb/samples/sample3.vb)]

İNDİS() yöntemi listeleme 3'te herhangi bir parametre kabul etmiyor. URL /Home bu İNDİS() yöntemi çağrılmasına neden olur. URL /Home/dizin/3 de (kimliği göz ardı edilir) bu yöntemi çağırır.

URL /Home ayrıca listeleme 4'te HomeController sınıfının İNDİS() yöntemi eşleşir.

**4 - HomeController.vb (dizin eylem boş değer atanabilir parametresiyle) listeleme**

[!code-vb[Main](asp-net-mvc-routing-overview-vb/samples/sample4.vb)]

Listeleme 4'te İNDİS() yöntemi bir tamsayı parametresine sahiptir. Parametre boş değer atanabilir bir parametre (değeri hiçbir şey olabilir) olduğundan, bir hata yükseltmeden İNDİS() çağrılabilir.

Son olarak, bir özel durum ID parametresi bu yana listeleme 5 URL /Home ile İNDİS() yönteminin çağrılması neden olan *değil* boş değer atanabilir bir parametre. İNDİS() yöntemini çağırmak çalışırsanız, Şekil 1'de görüntülenen hata alırsınız.

**5 - HomeController.vb (ID parametresi eylemiyle dizin) listeleme**

[!code-vb[Main](asp-net-mvc-routing-overview-vb/samples/sample5.vb)]


[![Bir parametre değeri bekler bir denetleyici eylemi çağırma](asp-net-mvc-routing-overview-vb/_static/image1.jpg)](asp-net-mvc-routing-overview-vb/_static/image1.png)

**Şekil 01**: bir parametre değeri bekler bir denetleyici Eylemi Çağırma ([tam boyutlu görüntüyü görüntülemek için tıklatın](asp-net-mvc-routing-overview-vb/_static/image2.png))


URL /Home/dizin/3, diğer yandan düzgün listeleme 5'te dizin denetleyici eylemi ile çalışır. İstek /Home/Index/3 3 değerine sahip bir ID parametresi çağrılacak İNDİS() yöntemi neden olur.

## <a name="summary"></a>Özet

ASP.NET yönlendirme için kısa bir giriş sağlamak için bu öğreticinin amacı oluştu. Yeni bir ASP.NET MVC uygulamasını almak varsayılan rota tablosu bileşen başvuru incelenir. Varsayılan rota URL'leri denetleyici eylemleri için nasıl eşlendiğini öğrendiniz.

>[!div class="step-by-step"]
[Önceki](creating-an-action-cs.md)
[sonraki](understanding-action-filters-vb.md)
