---
uid: mvc/videos/mvc-2/how-do-i/how-do-i-use-httpverbs-attributes-in-an-mvc-application
title: "Nasıl Yaparım MVC uygulamasındaki kullanım HttpVerbs öznitelikleri? | Microsoft Docs"
author: rick-anderson
description: "Bu video Chris Pels HttpVerbs öznitelikleri MVC Eylemler erişimi denetlemek için nasıl kullanılacağını gösterir. İlk olarak, bir örnek uygulama ile bir varsayılan ortak oluşturulur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/30/2009
ms.topic: article
ms.assetid: d2488a1d-0f3f-4994-8fbe-4f59b8c9503e
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/videos/mvc-2/how-do-i/how-do-i-use-httpverbs-attributes-in-an-mvc-application
msc.type: video
ms.openlocfilehash: 278720a6762bedae357e23b368b6dc34e50568d6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="how-do-i-use-httpverbs-attributes-in-an-mvc-application"></a><span data-ttu-id="a64e3-105">Nasıl Yaparım MVC uygulamasındaki kullanım HttpVerbs öznitelikleri?</span><span class="sxs-lookup"><span data-stu-id="a64e3-105">How Do I: Use HttpVerbs Attributes in an MVC Application?</span></span>
====================
<span data-ttu-id="a64e3-106">tarafından [Chris Pels](https://twitter.com/chrispels)</span><span class="sxs-lookup"><span data-stu-id="a64e3-106">by [Chris Pels](https://twitter.com/chrispels)</span></span>

<span data-ttu-id="a64e3-107">Bu video Chris Pels HttpVerbs öznitelikleri MVC Eylemler erişimi denetlemek için nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="a64e3-107">In this video Chris Pels shows how to use the HttpVerbs attributes to control access to MVC actions.</span></span> <span data-ttu-id="a64e3-108">İlk olarak, örnek bir uygulama, bir varsayılan denetleyicisi ve bilgileri düzenleme görünümü ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a64e3-108">First, a sample application is created with a default controller and view for editing the information.</span></span> <span data-ttu-id="a64e3-109">Ardından, ikinci bir dizin eylem yalnızca HTTP POST kullanıldığında çağrılır için bir HttpPost özniteliği olan denetleyicisi eklenir.</span><span class="sxs-lookup"><span data-stu-id="a64e3-109">Next, a second Index action is added to the controller which has an HttpPost attribute which restricts it to being called only when an HTTP POST is used.</span></span> <span data-ttu-id="a64e3-110">Bir izleme AcceptVerbs() özniteliği için Visual Studio 2008 alternatif bir sözdizimi uygulanır.</span><span class="sxs-lookup"><span data-stu-id="a64e3-110">As a follow-up, the AcceptVerbs() attribute is implemented as an alternative syntax for Visual Studio 2008.</span></span> <span data-ttu-id="a64e3-111">Bir HTTP GET delete bir bağlantıdan kullanarak gerçekleştirmek için ile ilişkili güvenlik riskleri engellemek için HttpVerbs kullanımını daha sonra ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="a64e3-111">A use of the HttpVerbs for preventing the security risk associated with using an HTTP GET to perform a delete from a link is then discussed.</span></span>

[<span data-ttu-id="a64e3-112">&#9654; (16 dakika) videoyu izleyin</span><span class="sxs-lookup"><span data-stu-id="a64e3-112">&#9654; Watch video (16 minutes)</span></span>](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/how-do-i-use-httpverbs-attributes-in-an-mvc-application)

>[!div class="step-by-step"]
<span data-ttu-id="a64e3-113">[Önceki](how-do-i-work-with-model-binders-in-an-mvc-application.md)
[sonraki](mvc2-html-encoding.md)</span><span class="sxs-lookup"><span data-stu-id="a64e3-113">[Previous](how-do-i-work-with-model-binders-in-an-mvc-application.md)
[Next](mvc2-html-encoding.md)</span></span>
