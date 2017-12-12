---
title: "Denetleyici yöntemlerine ve ASP.NET Core MVC uygulama görünümler"
author: rick-anderson
description: "Denetleyici yöntemlerine, görünümleri ve DataAnnotations ile çalışma"
keywords: "ASP.NET Çekirdeği"
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: c7313211-babe-babe-babe-8e72603cc0ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/controller-methods-views
ms.openlocfilehash: 846a78a09cdde0cfdbcf35bb899c1822ebe8fea2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
# <a name="controller-methods-and-views-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="86821-104">Denetleyici yöntemlerine ve ASP.NET Core MVC uygulama görünümler</span><span class="sxs-lookup"><span data-stu-id="86821-104">Controller methods and views in an ASP.NET Core MVC app</span></span>

<span data-ttu-id="86821-105">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="86821-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="86821-106">Film uygulaması için iyi bir başlangıç sahip olduğumuz ancak sunu ideal değil.</span><span class="sxs-lookup"><span data-stu-id="86821-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="86821-107">Biz (12:00: 00'da aşağıdaki görüntüde) zaman görmek istemediğiniz ve **ReleaseDate** iki sözcük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="86821-107">We don't want to see the time (12:00:00 AM in the following image) and **ReleaseDate** should be two words.</span></span>

![Dizin görünümü: yayın tarihi bir sözcük (boşluksuz) ve her film yayın tarihi 00: 00 süresini gösterir](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

<span data-ttu-id="86821-109">Açık *Models/Movie.cs* dosya ve aşağıda gösterilen vurgulanan satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="86821-109">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1&highlight=2,11-12)]

<span data-ttu-id="86821-110">Derleme ve uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="86821-110">Build and run the app.</span></span>

<!-- include start
![MVC Movie application open browser showing movie data](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

 -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
<span data-ttu-id="86821-111">[Önceki - SQLite ile çalışma](working-with-sql.md)
[sonraki - arama ekleyin](search.md)</span><span class="sxs-lookup"><span data-stu-id="86821-111">[Previous - Working with SQLite](working-with-sql.md)
[Next - Add search](search.md)</span></span>
