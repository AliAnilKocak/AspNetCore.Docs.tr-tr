---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-8
title: "Öğe ayrıntıları görüntülemek | Microsoft Docs"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 75ef94b1-bbec-4681-9210-452dba816144
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-8
msc.type: authoredcontent
ms.openlocfilehash: 0b6ae9384843712cae824ea662b984a40f021e57
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="display-item-details"></a><span data-ttu-id="eeba7-102">Görüntü öğe ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="eeba7-102">Display Item Details</span></span>
====================
<span data-ttu-id="eeba7-103">tarafından [CAN Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="eeba7-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="eeba7-104">Tamamlanan projenizi indirin</span><span class="sxs-lookup"><span data-stu-id="eeba7-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="eeba7-105">Bu bölümde, her kitap ayrıntılarını görüntülemek için özelliğini ekleyecektir.</span><span class="sxs-lookup"><span data-stu-id="eeba7-105">In this section, you will add the ability to view details for each book.</span></span> <span data-ttu-id="eeba7-106">App.js içinde görünüm modeli aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="eeba7-106">In app.js, add to the following code to the view model:</span></span>

[!code-javascript[Main](part-8/samples/sample1.js)]

<span data-ttu-id="eeba7-107">Views/Home/Index.cshtml içinde veri bağlama öğesi ayrıntıları bağlantısını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="eeba7-107">In Views/Home/Index.cshtml, add a data-bind element to the Details link:</span></span>

[!code-html[Main](part-8/samples/sample2.html?highlight=5)]

<span data-ttu-id="eeba7-108">Bu click işleyicisi bağlar &lt;bir&gt; öğesine `getBookDetail` görünüm modeli üzerinde işlevi.</span><span class="sxs-lookup"><span data-stu-id="eeba7-108">This binds the click handler for the &lt;a&gt; element to the `getBookDetail` function on the view model.</span></span>

<span data-ttu-id="eeba7-109">Aynı dosyada aşağıdaki işaretleme değiştirin:</span><span class="sxs-lookup"><span data-stu-id="eeba7-109">In the same file, replace the following mark-up:</span></span>

[!code-html[Main](part-8/samples/sample3.html)]

<span data-ttu-id="eeba7-110">şununla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="eeba7-110">with this:</span></span>

[!code-html[Main](part-8/samples/sample4.html)]

<span data-ttu-id="eeba7-111">Bu biçimlendirme verilere özelliklerine bağlı bir tablo oluşturur `detail` observable görünüm modeli.</span><span class="sxs-lookup"><span data-stu-id="eeba7-111">This markup creates a table that is data-bound to the properties of the `detail` observable in the view model.</span></span>

<span data-ttu-id="eeba7-112">"&lt;!--Ko--&gt; &quot; sözdizimi Boşaltılan bağlamasını DOM öğesi dışında dahil olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="eeba7-112">The "&lt;!-- ko --&gt;&quot; syntax lets you include a Knockout binding outside of a DOM element.</span></span> <span data-ttu-id="eeba7-113">Bu durumda, `if` bağlama neden olur, bu bölümde görüntülenecek biçimlendirme yalnızca `details` null olmayan bir değer.</span><span class="sxs-lookup"><span data-stu-id="eeba7-113">In this case, the `if` binding causes this section of markup to be displayed only when `details` is non-null.</span></span>

[!code-html[Main](part-8/samples/sample5.html)]

<span data-ttu-id="eeba7-114">Uygulamayı çalıştırın ve birine tıklayın, şimdi &quot;ayrıntı&quot; bağlantılar, uygulama rehberi ayrıntılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="eeba7-114">Now if you run the app and click one of the &quot;Detail&quot; links, the app will display the book details.</span></span>

[![](part-8/_static/image2.png)](part-8/_static/image1.png)

>[!div class="step-by-step"]
<span data-ttu-id="eeba7-115">[Önceki](part-7.md)
[sonraki](part-9.md)</span><span class="sxs-lookup"><span data-stu-id="eeba7-115">[Previous](part-7.md)
[Next](part-9.md)</span></span>
