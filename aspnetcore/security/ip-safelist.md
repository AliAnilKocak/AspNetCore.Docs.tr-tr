---
title: ASP.NET Core için istemci IP SafeList
author: damienbod
description: Uzak IP adreslerini onaylanan IP adresleri listesine göre doğrulamak için ara yazılım veya eylem filtreleri yazmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca5b0f8088773027f7403120247cbeca8900bcf5
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034340"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core için istemci IP SafeList

By [Davmıen Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)
 
Bu makalede bir ASP.NET Core uygulamasında bir IP SafeList (beyaz liste olarak da bilinir) uygulamanın üç yolu gösterilmektedir. Şunu kullanabilirsiniz:

* Her isteğin uzak IP adresini denetlemek için ara yazılım.
* Belirli denetleyiciler veya eylem yöntemlerine yönelik isteklerin uzak IP adresini denetlemek için eylem filtreleri.
* Razor sayfalarına yönelik isteklerin uzak IP adresini denetlemek için filtreler Razor Pages.

Her durumda, onaylanan istemci IP adreslerini içeren bir dize bir uygulama ayarında saklanır. Ara yazılım veya filtre, dizeyi bir liste olarak ayrıştırır ve uzak IP 'nin listede olup olmadığını denetler. Aksi takdirde, HTTP 403 yasaklanmış durum kodu döndürülür.

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="the-safelist"></a>SafeList

Liste *appSettings. JSON* dosyasında yapılandırılır. Bu, noktalı virgülle ayrılmış bir liste ve IPv4 ve IPv6 adresleri içerebilir.

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>Ara yazılım

`Configure` yöntemi, ara yazılımı ekler ve bir Oluşturucu parametresinde SafeList dizesini buna geçirir.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

Ara yazılım, dizeyi bir dizi olarak ayrıştırır ve dizideki uzak IP adresini arar. Uzak IP adresi bulunamazsa, ara yazılım HTTP 401 yasak değerini döndürür. Bu doğrulama işlemi HTTP GET istekleri için atlanır.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Eylem filtresi

Yalnızca belirli denetleyiciler veya eylem yöntemleri için bir SafeList istiyorsanız, bir eylem filtresi kullanın. Örnek buradadır: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckFilter.cs)]

Eylem filtresi, hizmetler kapsayıcısına eklenir.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Filtre daha sonra bir denetleyici veya eylem yönteminde kullanılabilir.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

Örnek uygulamada, filtre `Get` yöntemine uygulanır. Bu nedenle, uygulamayı bir `Get` API isteği göndererek test ettiğinizde, öznitelik istemci IP adresini doğruluyor. API 'YI başka bir HTTP yöntemiyle çağırarak test ettiğinizde, ara yazılım istemci IP 'sini doğruluyor.

## <a name="razor-pages-filter"></a>Razor Pages filtresi 

Razor Pages bir uygulama için bir SafeList istiyorsanız, bir Razor Pages filtresi kullanın. Örnek buradadır: 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckPageFilter.cs)]

Bu filtre, MVC filtreleri koleksiyonuna eklenerek etkinleştirilir.

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

Uygulamayı çalıştırıp bir Razor sayfası istediğinizde Razor Pages filtresi istemci IP 'sini doğruluyor.

## <a name="next-steps"></a>Sonraki adımlar

[ASP.NET Core ara yazılım hakkında daha fazla bilgi edinin](xref:fundamentals/middleware/index).
