---
uid: web-forms/overview/ajax-control-toolkit/dropshadow/manipulating-dropshadow-properties-from-client-code-cs
title: İstemci kodu (C#) DropShadow özelliklerinden düzenleme | Microsoft Docs
author: wenz
description: DataList'ın düzenleme arabirimi özelleştirme
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: c83ca3e6-c0bf-4158-a166-40c1ab0f33da
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dropshadow/manipulating-dropshadow-properties-from-client-code-cs
msc.type: authoredcontent
ms.openlocfilehash: 37a7784e1d42477e31938e1d15495993ac86fc56
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="manipulating-dropshadow-properties-from-client-code-c"></a><span data-ttu-id="711f1-103">İstemci kodu (C#) DropShadow özelliklerini düzenleme</span><span class="sxs-lookup"><span data-stu-id="711f1-103">Manipulating DropShadow Properties from Client Code (C#)</span></span>
====================
<span data-ttu-id="711f1-104">tarafından [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="711f1-104">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="711f1-105">[Kodu indirme](http://download.microsoft.com/download/5/1/6/51652a81-500b-4f6b-88d3-617103e7941e/DropShadow2.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dropshadow2CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="711f1-105">[Download Code](http://download.microsoft.com/download/5/1/6/51652a81-500b-4f6b-88d3-617103e7941e/DropShadow2.cs.zip) or [Download PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dropshadow2CS.pdf)</span></span>

> <span data-ttu-id="711f1-106">AJAX Denetim Araç Seti DropShadow denetiminde gölge paneliyle genişletir.</span><span class="sxs-lookup"><span data-stu-id="711f1-106">The DropShadow control in the AJAX Control Toolkit extends a panel with a drop shadow.</span></span> <span data-ttu-id="711f1-107">Bu genişletici özelliklerini kullanarak istemci JavaScript kodu da değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="711f1-107">Properties of this extender can also be changed using client JavaScript code.</span></span>


## <a name="overview"></a><span data-ttu-id="711f1-108">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="711f1-108">Overview</span></span>

<span data-ttu-id="711f1-109">AJAX Denetim Araç Seti DropShadow denetiminde gölge paneliyle genişletir.</span><span class="sxs-lookup"><span data-stu-id="711f1-109">The DropShadow control in the AJAX Control Toolkit extends a panel with a drop shadow.</span></span> <span data-ttu-id="711f1-110">Bu genişletici özelliklerini kullanarak istemci JavaScript kodu da değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="711f1-110">Properties of this extender can also be changed using client JavaScript code.</span></span>

## <a name="steps"></a><span data-ttu-id="711f1-111">Adımlar</span><span class="sxs-lookup"><span data-stu-id="711f1-111">Steps</span></span>

<span data-ttu-id="711f1-112">Kod bazı satırlık metin içeren bir panel ile başlar:</span><span class="sxs-lookup"><span data-stu-id="711f1-112">The code starts with a panel containing some lines of text:</span></span>

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-cs/samples/sample1.aspx)]

<span data-ttu-id="711f1-113">İlişkili CSS sınıf Masası iyi arka plan rengi sağlar:</span><span class="sxs-lookup"><span data-stu-id="711f1-113">The associated CSS class gives the panel a nice background color:</span></span>

[!code-css[Main](manipulating-dropshadow-properties-from-client-code-cs/samples/sample2.css)]

<span data-ttu-id="711f1-114">`DropShadowExtender` Bir gölge efekti, % 50 olarak belirlenmiş opaklık paneliyle genişletmek için eklendi:</span><span class="sxs-lookup"><span data-stu-id="711f1-114">The `DropShadowExtender` is added to extend the panel with a drop shadow effect, opacity set to 50%:</span></span>

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-cs/samples/sample3.aspx)]

<span data-ttu-id="711f1-115">Ardından, ASP.NET AJAX `ScriptManager` denetimi çalışmak Denetim Araç Seti sağlar:</span><span class="sxs-lookup"><span data-stu-id="711f1-115">Then, the ASP.NET AJAX `ScriptManager` control enables the Control Toolkit to work:</span></span>

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-cs/samples/sample4.aspx)]

<span data-ttu-id="711f1-116">Başka bir panel gölge geçirgenliğini ayarlamak için iki JavaScript bağlantılar içerir: eksi bağlantı gölgenin opaklık azaltır, artı bağlantı da artırır.</span><span class="sxs-lookup"><span data-stu-id="711f1-116">Another panel contains two JavaScript links for setting the opacity of the drop shadow: the minus link decreases the shadow's opacity, the plus link increases it.</span></span>

[!code-aspx[Main](manipulating-dropshadow-properties-from-client-code-cs/samples/sample5.aspx)]

<span data-ttu-id="711f1-117">JavaScript işlevinin `changeOpacity()` sonra ilk bulmalıdır `DropShadowExtender` sayfasında denetimi.</span><span class="sxs-lookup"><span data-stu-id="711f1-117">The JavaScript function `changeOpacity()` must then first find the `DropShadowExtender` control on the page.</span></span> <span data-ttu-id="711f1-118">ASP.NET AJAX tanımlar `$find()` tam olarak bu görev için yöntem.</span><span class="sxs-lookup"><span data-stu-id="711f1-118">ASP.NET AJAX defines the `$find()` method for exactly that task.</span></span> <span data-ttu-id="711f1-119">Ardından, `get_Opacity()` yöntemi alır Geçerli Opaklık `set_Opacity()` yöntemini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="711f1-119">Then, the `get_Opacity()` method retrieves the current opacity, the `set_Opacity()` method sets it.</span></span> <span data-ttu-id="711f1-120">JavaScript kodu ardından geçerli geçirgenlik değeri geçirir `<label>` öğe:</span><span class="sxs-lookup"><span data-stu-id="711f1-120">The JavaScript code then puts the current opacity value in the `<label>` element:</span></span>

[!code-html[Main](manipulating-dropshadow-properties-from-client-code-cs/samples/sample6.html)]


<span data-ttu-id="711f1-121">[![Opaklık istemci tarafında değiştirilir](manipulating-dropshadow-properties-from-client-code-cs/_static/image2.png)](manipulating-dropshadow-properties-from-client-code-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="711f1-121">[![The opacity is changed on the client side](manipulating-dropshadow-properties-from-client-code-cs/_static/image2.png)](manipulating-dropshadow-properties-from-client-code-cs/_static/image1.png)</span></span>

<span data-ttu-id="711f1-122">Opaklık istemci tarafında değiştirilir ([tam boyutlu görüntüyü görüntülemek için tıklatın](manipulating-dropshadow-properties-from-client-code-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="711f1-122">The opacity is changed on the client side ([Click to view full-size image](manipulating-dropshadow-properties-from-client-code-cs/_static/image3.png))</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="711f1-123">[Önceki](adjusting-the-z-index-of-a-dropshadow-cs.md)
> [sonraki](adjusting-the-z-index-of-a-dropshadow-vb.md)</span><span class="sxs-lookup"><span data-stu-id="711f1-123">[Previous](adjusting-the-z-index-of-a-dropshadow-cs.md)
[Next](adjusting-the-z-index-of-a-dropshadow-vb.md)</span></span>
