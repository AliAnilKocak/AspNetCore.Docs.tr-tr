---
title: "Denetleyici yöntemlerine ve ASP.NET Core MVC uygulama görünümler"
author: rick-anderson
description: "Denetleyici yöntemlerine, görünümleri ve DataAnnotations ile çalışma"
ms.author: riande
manager: wpickett
ms.date: 04/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app-mac/controller-methods-views
ms.openlocfilehash: 71cdf9f0a4a72f375af094c7c0a446278f8aeeb5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2018
---
# <a name="controller-methods-and-views-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="98783-103">Denetleyici yöntemlerine ve ASP.NET Core MVC uygulama görünümler</span><span class="sxs-lookup"><span data-stu-id="98783-103">Controller methods and views in an ASP.NET Core MVC app</span></span>

<span data-ttu-id="98783-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="98783-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="98783-105">Film uygulaması için iyi bir başlangıç sahip olduğumuz ancak sunu ideal değil.</span><span class="sxs-lookup"><span data-stu-id="98783-105">We have a good start to the movie app, but the presentation isn't ideal.</span></span> <span data-ttu-id="98783-106">Biz (12:00: 00'da aşağıdaki görüntüde) zaman görmek istemediğiniz ve **ReleaseDate** iki sözcük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="98783-106">We don't want to see the time (12:00:00 AM in the following image) and **ReleaseDate** should be two words.</span></span>

![Dizin görünümü: yayın tarihi bir sözcük (boşluksuz) ve her film yayın tarihi 00: 00 süresini gösterir](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

<span data-ttu-id="98783-108">Açık *Models/Movie.cs* dosya ve aşağıda gösterilen vurgulanan satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="98783-108">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDate.cs?name=snippet_1&highlight=2,11-12)]

<span data-ttu-id="98783-109">Derleme ve uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="98783-109">Build and run the app.</span></span>

<!-- include start
![MVC Movie application open browser showing movie data](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)

 -->

[!INCLUDE[adding-model](../../includes/mvc-intro/controller-methods-views.md)]

>[!div class="step-by-step"]
<span data-ttu-id="98783-110">[Önceki - SQLite ile çalışma](working-with-sql.md)
[sonraki - arama ekleyin](search.md)</span><span class="sxs-lookup"><span data-stu-id="98783-110">[Previous - Working with SQLite](working-with-sql.md)
[Next - Add search](search.md)</span></span>
