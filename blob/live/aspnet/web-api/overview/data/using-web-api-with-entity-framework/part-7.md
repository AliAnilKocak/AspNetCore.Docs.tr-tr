---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-7
title: "Görünüm (UI) oluşturma | Microsoft Docs"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: b2445062-a1fe-4133-8994-f510280f6d9a
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-7
msc.type: authoredcontent
ms.openlocfilehash: 8c5cc662e2e3c9cb07ca9e30ff57eb084d58e1bb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="create-the-view-ui"></a><span data-ttu-id="d47c9-102">Görünüm (UI) oluşturun</span><span class="sxs-lookup"><span data-stu-id="d47c9-102">Create the View (UI)</span></span>
====================
<span data-ttu-id="d47c9-103">tarafından [CAN Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="d47c9-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="d47c9-104">Tamamlanan projenizi indirin</span><span class="sxs-lookup"><span data-stu-id="d47c9-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="d47c9-105">Bu bölümde, uygulama için HTML tanımlamak ve HTML ve görünüm modeli arasında veri bağlama eklemek başlar.</span><span class="sxs-lookup"><span data-stu-id="d47c9-105">In this section, you will start to define the HTML for the app, and add data binding between the HTML and the view model.</span></span>

<span data-ttu-id="d47c9-106">Views/Home/Index.cshtml dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="d47c9-106">Open the file Views/Home/Index.cshtml.</span></span> <span data-ttu-id="d47c9-107">Bu dosyanın tüm içeriğini aşağıdakiyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d47c9-107">Replace the entire contents of that file with the following.</span></span>

[!code-cshtml[Main](part-7/samples/sample1.cshtml)]

<span data-ttu-id="d47c9-108">Çoğu `div` öğeleri bulunduğundan için [önyükleme](http://getbootstrap.com/) stil oluşturma.</span><span class="sxs-lookup"><span data-stu-id="d47c9-108">Most of the `div` elements are there for [Bootstrap](http://getbootstrap.com/) styling.</span></span> <span data-ttu-id="d47c9-109">Raporlarla önemli öğeleridir `data-bind` öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="d47c9-109">The important elements are the ones with `data-bind` attributes.</span></span> <span data-ttu-id="d47c9-110">Bu öznitelik HTML görünüm modeli bağlar.</span><span class="sxs-lookup"><span data-stu-id="d47c9-110">This attribute links the HTML to the view model.</span></span>

<span data-ttu-id="d47c9-111">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d47c9-111">For example:</span></span>

[!code-html[Main](part-7/samples/sample2.html)]

<span data-ttu-id="d47c9-112">Bu örnekte, &quot; `text` &quot; nedenler bağlama `<p>` değerini göstermek için öğesi `error` görünüm modeli özelliğinden.</span><span class="sxs-lookup"><span data-stu-id="d47c9-112">In this example, the &quot;`text`&quot; binding causes the `<p>` element to show the value of the `error` property from the view model.</span></span> <span data-ttu-id="d47c9-113">Sözcüğünün `error` olarak bildirilen bir `ko.observable`:</span><span class="sxs-lookup"><span data-stu-id="d47c9-113">Recall that `error` was declared as a `ko.observable`:</span></span>

[!code-javascript[Main](part-7/samples/sample3.js)]

<span data-ttu-id="d47c9-114">Her yeni bir değer atanması `error`, çakıştırma güncelleştirmeleri metinde `<p>` öğesi.</span><span class="sxs-lookup"><span data-stu-id="d47c9-114">Whenever a new value is assigned to `error`, Knockout updates the text in the `<p>` element.</span></span>

<span data-ttu-id="d47c9-115">`foreach` Bağlama içeriğini döngü Boşaltılan söyler `books` dizi.</span><span class="sxs-lookup"><span data-stu-id="d47c9-115">The `foreach` binding tells Knockout to loop through the contents of the `books` array.</span></span> <span data-ttu-id="d47c9-116">Dizideki her öğe için çakıştırma yeni bir oluşturur &lt;li&gt; öğesi.</span><span class="sxs-lookup"><span data-stu-id="d47c9-116">For each item in the array, Knockout creates a new &lt;li&gt; element.</span></span> <span data-ttu-id="d47c9-117">Bağlamaları bağlamı içinde `foreach` dizi öğenin özelliklerini bakın.</span><span class="sxs-lookup"><span data-stu-id="d47c9-117">Bindings inside the context of the `foreach` refer to properties on the array item.</span></span> <span data-ttu-id="d47c9-118">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d47c9-118">For example:</span></span>

[!code-html[Main](part-7/samples/sample4.html)]

<span data-ttu-id="d47c9-119">Burada `text` bağlama her rehberi Yazar özelliği okur.</span><span class="sxs-lookup"><span data-stu-id="d47c9-119">Here the `text` binding reads the Author property of each book.</span></span>

<span data-ttu-id="d47c9-120">Uygulamayı şimdi çalıştırırsanız, aşağıdaki gibi görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="d47c9-120">If you run the application now, it should look like this:</span></span>

![](part-7/_static/image1.png)

<span data-ttu-id="d47c9-121">Sayfa yüklendikten sonra defterleri listesi zaman uyumsuz olarak yükler.</span><span class="sxs-lookup"><span data-stu-id="d47c9-121">The list of books loads asynchronously, after the page loads.</span></span> <span data-ttu-id="d47c9-122">Şimdi &quot;ayrıntıları&quot; Bağlantılar işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="d47c9-122">Right now, the &quot;Details&quot; links are not functional.</span></span> <span data-ttu-id="d47c9-123">Bu işlevsellik sonraki bölümde ekleyeceğiz.</span><span class="sxs-lookup"><span data-stu-id="d47c9-123">We'll add this functionality in the next section.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="d47c9-124">[Önceki](part-6.md)
[sonraki](part-8.md)</span><span class="sxs-lookup"><span data-stu-id="d47c9-124">[Previous](part-6.md)
[Next](part-8.md)</span></span>
