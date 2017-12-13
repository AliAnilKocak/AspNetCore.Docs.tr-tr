---
uid: web-forms/overview/ajax-control-toolkit/animation/picking-one-animation-out-of-a-list-cs
title: "Bir liste (C#) dışında bir animasyon çekme | Microsoft Docs"
author: wenz
description: "ASP.NET AJAX Denetim Araç Seti animasyon denetiminde bir denetimi ancak animasyonları için bir denetim eklemek için tam bir çerçeve değil. Framework de izin ver..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 06a776fe-7c73-4ca7-8e02-5260a86edc03
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/picking-one-animation-out-of-a-list-cs
msc.type: authoredcontent
ms.openlocfilehash: a24c4ffe49df4eb663f833eb1814f7cbcf15e07e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="picking-one-animation-out-of-a-list-c"></a><span data-ttu-id="eaa3b-104">Bir liste (C#) dışında bir animasyon çekme</span><span class="sxs-lookup"><span data-stu-id="eaa3b-104">Picking One Animation Out Of a List (C#)</span></span>
====================
<span data-ttu-id="eaa3b-105">tarafından [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="eaa3b-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="eaa3b-106">[Kodu indirme](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation5.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation5CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="eaa3b-106">[Download Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation5.cs.zip) or [Download PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation5CS.pdf)</span></span>

> <span data-ttu-id="eaa3b-107">ASP.NET AJAX Denetim Araç Seti animasyon denetiminde bir denetimi ancak animasyonları için bir denetim eklemek için tam bir çerçeve değil.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-107">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="eaa3b-108">Çerçeve ayrıca bazı JavaScript kodları değerlendirmesine bağlı olarak bir animasyon listesi dışında bir animasyon seçmek Programcı sağlar.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-108">The framework also allows the programmer to pick one animation out of a list of animations, depending on the evaluation of some JavaScript code.</span></span>


## <a name="overview"></a><span data-ttu-id="eaa3b-109">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="eaa3b-109">Overview</span></span>

<span data-ttu-id="eaa3b-110">ASP.NET AJAX Denetim Araç Seti animasyon denetiminde bir denetimi ancak animasyonları için bir denetim eklemek için tam bir çerçeve değil.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-110">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="eaa3b-111">Çerçeve ayrıca bazı JavaScript kodları değerlendirmesine bağlı olarak bir animasyon listesi dışında bir animasyon seçmek Programcı sağlar.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-111">The framework also allows the programmer to pick one animation out of a list of animations, depending on the evaluation of some JavaScript code.</span></span>

## <a name="steps"></a><span data-ttu-id="eaa3b-112">Adımlar</span><span class="sxs-lookup"><span data-stu-id="eaa3b-112">Steps</span></span>

<span data-ttu-id="eaa3b-113">İlk olarak dahil `ScriptManager` sayfasında; daha sonra ASP.NET AJAX kitaplığı, Denetim Araç Seti kullanmayı mümkün hale getirme yüklenir:</span><span class="sxs-lookup"><span data-stu-id="eaa3b-113">First of all, include the `ScriptManager` in the page; then, the ASP.NET AJAX library is loaded, making it possible to use the Control Toolkit:</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample1.aspx)]

<span data-ttu-id="eaa3b-114">Animasyonun bir panel şöyle metin uygulanır:</span><span class="sxs-lookup"><span data-stu-id="eaa3b-114">The animation will be applied to a panel of text which looks like this:</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample2.aspx)]

<span data-ttu-id="eaa3b-115">İlişkili CSS sınıfı bölmesinin iyi arka plan rengi tanımlayın ve ayrıca sabit genişlikli bölmesinin ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="eaa3b-115">In the associated CSS class for the panel, define a nice background color and also set a fixed width for the panel:</span></span>

[!code-css[Main](picking-one-animation-out-of-a-list-cs/samples/sample3.css)]

<span data-ttu-id="eaa3b-116">Ardından, ekleyin `AnimationExtender` sayfasına sağlayan bir `ID`, `TargetControlID` özniteliği ve zorunlu`runat="server":`</span><span class="sxs-lookup"><span data-stu-id="eaa3b-116">Then, add the `AnimationExtender` to the page, providing an `ID`, the `TargetControlID` attribute and the obligatory `runat="server":`</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample4.aspx)]

<span data-ttu-id="eaa3b-117">İçinde `<Animations>` düğümü, kullanım `<OnLoad>` sayfa tam yüklenmeden silindikten sonra animasyonları çalıştırmak için.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-117">Within the `<Animations>` node, use `<OnLoad>` to run the animations once the page has been fully loaded.</span></span> <span data-ttu-id="eaa3b-118">Normal bir animasyon birini yerine `<Case>` öğesi oyuna gelir.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-118">Instead of one of the regular animations, the `<Case>` element comes into play.</span></span> <span data-ttu-id="eaa3b-119">Kendi SelectScript özniteliğinin değeri değerlendirilir; dönüş değeri sayısal olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-119">The value of its SelectScript attribute is evaluated; the return value must be numerical.</span></span> <span data-ttu-id="eaa3b-120">Bu sayı, içinde subanimations birini bağlı olarak &lt;durumda&gt; yürütülür.</span><span class="sxs-lookup"><span data-stu-id="eaa3b-120">Depending on this number, one of the subanimations within &lt;Case&gt; is executed.</span></span> <span data-ttu-id="eaa3b-121">SelectScript 2 olarak değerlendirilirse, Denetim Araç Seti içinde üçüncü animasyon örneği için çalışan &lt;durumda&gt; (sayım 0 başlar).</span><span class="sxs-lookup"><span data-stu-id="eaa3b-121">For instance, if SelectScript evaluates to 2, the Control Toolkit runs the third animation within &lt;Case&gt; (counting starts at 0).</span></span>

<span data-ttu-id="eaa3b-122">Aşağıdaki biçimlendirmede üç subanimations tanımlar: genişliği yeniden boyutlandırma, yüksekliği yeniden boyutlandırma ve yavaş çıkış. JavaScript kodu (`Math.floor(3 * Math.random())`) üç animasyon birini çalışabilmesi 0 ve 2 ' arasında bir sayı seçer:</span><span class="sxs-lookup"><span data-stu-id="eaa3b-122">The following markup defines three subanimations: Resizing the width, resizing the height, and fading out. The JavaScript code (`Math.floor(3 * Math.random())`) then picks a number between 0 and 2, so that one of the three animations is run:</span></span>

[!code-aspx[Main](picking-one-animation-out-of-a-list-cs/samples/sample5.aspx)]


<span data-ttu-id="eaa3b-123">[![Olası üç animasyon birini: paneli daha geniş alır](picking-one-animation-out-of-a-list-cs/_static/image2.png)](picking-one-animation-out-of-a-list-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="eaa3b-123">[![One of the possible three animations: The panel gets wider](picking-one-animation-out-of-a-list-cs/_static/image2.png)](picking-one-animation-out-of-a-list-cs/_static/image1.png)</span></span>

<span data-ttu-id="eaa3b-124">Olası üç animasyon birini: paneli daha geniş alır ([tam boyutlu görüntüyü görüntülemek için tıklatın](picking-one-animation-out-of-a-list-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="eaa3b-124">One of the possible three animations: The panel gets wider ([Click to view full-size image](picking-one-animation-out-of-a-list-cs/_static/image3.png))</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="eaa3b-125">[Önceki](animation-depending-on-a-condition-cs.md)
[sonraki](animating-in-response-to-user-interaction-cs.md)</span><span class="sxs-lookup"><span data-stu-id="eaa3b-125">[Previous](animation-depending-on-a-condition-cs.md)
[Next](animating-in-response-to-user-interaction-cs.md)</span></span>
