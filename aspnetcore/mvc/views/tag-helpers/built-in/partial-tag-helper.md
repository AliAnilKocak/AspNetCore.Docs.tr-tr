---
title: ASP.NET Core kısmi etiket Yardımcısı
author: scottaddie
description: Kısmi bir görünüm işlenirken ASP.NET Core kısmi etiket yardımcısını ve onun özniteliklerinin her birinin rolünü bulur.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: d3207969dfbeb9a81e0da88f38a38c6889bbfba9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775875"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>ASP.NET Core kısmi etiket Yardımcısı

[Scott Ade](https://github.com/scottaddie) tarafından

Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro>..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Genel Bakış

Kısmi etiket Yardımcısı, Razor sayfalarda ve MVC uygulamalarında kısmi bir [Görünüm](xref:mvc/views/partial) oluşturmak için kullanılır. Bunu göz önünde bulundurun:

* ASP.NET Core 2,1 veya üstünü gerektirir.
* , [HTML yardımcı söz dizimine](xref:mvc/views/partial#reference-a-partial-view)bir alternatiftir.
* Kısmi görünümü zaman uyumsuz olarak işler.

Kısmi görünümü işlemeye yönelik HTML yardımcı seçenekleri şunlardır:

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

*Ürün* modeli bu belgenin tamamında örneklerde kullanılır:

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Kısmi etiket Yardımcısı özniteliklerinin bir stoku aşağıda verilmiştir.

## <a name="name"></a>ad

`name` Özniteliği gereklidir. İşlenecek kısmi görünümün adını veya yolunu gösterir. Kısmi bir görünüm adı sağlandığında, [görünüm bulma](xref:mvc/views/overview#view-discovery) işlemi başlatılır. Açık bir yol sağlandığında bu işlem atlanır. Tüm kabul edilebilir `name` değerler için bkz. [kısmi görünüm bulma](xref:mvc/views/partial#partial-view-discovery).

Aşağıdaki biçimlendirme, *_ProductPartial. cshtml* 'nin *paylaşılan* klasörden yükleneceğini belirten açık bir yol kullanır. [For](#for) özniteliği kullanılarak, bağlama için kısmi görünüme bir model geçirilir.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>for

`for` Özniteliği, geçerli modele göre değerlendirilecek bir [modelexpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) atar. `ModelExpression` Sözdizimi, `@Model.` sözdizimini anlar. Örneğin, `for="Product"` yerine kullanılabilir `for="@Model.Product"`. Bu varsayılan çıkarım davranışı, `@` bir satır içi ifade tanımlamak için simgesi kullanılarak geçersiz kılınır.

Aşağıdaki biçimlendirme *_ProductPartial. cshtml*'yi yükler:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

Kısmi görünüm, ilişkili sayfa modelinin `Product` özelliğine bağlanır:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

`model` Özniteliği kısmi görünüme geçirilecek bir model örneği atar. `model` Özniteliği [for](#for) özniteliğiyle birlikte kullanılamaz.

Aşağıdaki biçimlendirmede, yeni `Product` bir nesne örneği oluşturulur ve bağlama için `model` özniteliğine geçirilir:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>verileri görüntüle

`view-data` Özniteliği, kısmi görünüme geçirilecek bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) atar. Aşağıdaki biçimlendirme tüm ViewData toplamasını kısmi görünüm için erişilebilir hale getirir:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

Yukarıdaki kodda, `IsNumberReadOnly` anahtar değeri olarak `true` ayarlanır ve ViewData koleksiyonuna eklenir. Sonuç olarak `ViewData["IsNumberReadOnly"]` , aşağıdaki kısmi görünüm içinde erişilebilir hale getirilir:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

Bu örnekte, değeri, *sayı* alanının `ViewData["IsNumberReadOnly"]` salt okunurdur olarak görüntülenip görüntülenmeyeceğini belirler.

## <a name="migrate-from-an-html-helper"></a>HTML yardımcısından geçiş yapma

Aşağıdaki zaman uyumsuz HTML Yardımcısı örneğini göz önünde bulundurun. Bir ürün koleksiyonu tekrarlandırılır ve görüntülenir. `PartialAsync` Metodun ilk parametresine göre *_ProductPartial. cshtml* kısmi görünümü yüklenir. `Product` Modelin bir örneği bağlama için kısmi görünüme geçirilir.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

Aşağıdaki kısmi etiket Yardımcısı `PartialAsync` HTML Yardımcısı ile aynı zaman uyumsuz işleme davranışına erişir. `model` Özniteliğe kısmi görünüme bağlama için `Product` bir model örneği atanır.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
