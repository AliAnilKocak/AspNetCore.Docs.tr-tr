---
title: Bir ASP.NET Core MVC uygulamasına arama ekleyin
author: rick-anderson
description: Basit ASP.NET Core MVC uygulaması için arama eklemeyi gösterir
ms.author: riande
ms.date: 03/07/2017
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: d0581142b505323385feba441b707d29b3f6db5d
ms.sourcegitcommit: 356c8d394aaf384c834e9c90cabab43bfe36e063
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960846"
---
[!INCLUDE [adding-model](~/includes/mvc-intro/search1.md)]

<span data-ttu-id="4071e-103">Hızlı bir şekilde adlandırabilirsiniz `searchString` parametresi `id` ile **yeniden adlandırma** komutu.</span><span class="sxs-lookup"><span data-stu-id="4071e-103">You can quickly rename the `searchString` parameter to `id` with the **rename** command.</span></span> <span data-ttu-id="4071e-104">Sağ tıklayın `searchString` **> yeniden adlandırma**.</span><span class="sxs-lookup"><span data-stu-id="4071e-104">Right click on `searchString` **> Rename**.</span></span>

![Bağlam menüsü](search/_static/rename.png)

<span data-ttu-id="4071e-106">Yeniden Adlandır hedefleri vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="4071e-106">The rename targets are highlighted.</span></span>

![Dizin ActionResult yöntemi vurgulanmış değişkeni gösteren kod Düzenleyicisi](search/_static/rename2.png)

<span data-ttu-id="4071e-108">Parametre değiştirme `id` ve tüm oluşumlarını `searchString` değiştirmek `id`.</span><span class="sxs-lookup"><span data-stu-id="4071e-108">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

![Kod Düzenleyicisi değişkeni gösteren kimliğine değiştirildi](search/_static/rename3.png)

[!INCLUDE [adding-model](~/includes/mvc-intro/search2.md)]

<span data-ttu-id="4071e-110">IntelliSense bize işaretleme güncelleştirme nasıl yardımcı olduğunu dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="4071e-110">Notice how intelliSense helps us update the markup.</span></span>

![Form öğesi için öznitelikler listesinde seçili yöntemiyle IntelliSense bağlamsal menüsü](search/_static/int_m.png)

![IntelliSense bağlamsal menüsüyle yöntemi öznitelik değerleri listesinde seçili](search/_static/int_get.png)

<span data-ttu-id="4071e-113">Ayırt edici yazı tipini fark `<form>` etiketi.</span><span class="sxs-lookup"><span data-stu-id="4071e-113">Notice the distinctive font in the `<form>` tag.</span></span> <span data-ttu-id="4071e-114">Etiket tarafından desteklenen farklı yazı gösterir [etiket Yardımcıları](~/mvc/views/tag-helpers/intro.md).</span><span class="sxs-lookup"><span data-stu-id="4071e-114">That distinctive font indicates the tag is supported by [Tag Helpers](~/mvc/views/tag-helpers/intro.md).</span></span>

![Form etiketi mor metin ile](search/_static/th_font.png)

[!INCLUDE [adding-model](~/includes/mvc-intro/search3.md)]

> [!div class="step-by-step"]
> <span data-ttu-id="4071e-116">[Önceki](controller-methods-views.md)
> [sonraki](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="4071e-116">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>  
