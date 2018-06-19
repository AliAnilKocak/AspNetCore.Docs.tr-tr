---
uid: web-forms/overview/ajax-control-toolkit/animation/triggering-an-animation-in-another-control-cs
title: Başka bir denetim (C#) animasyonda tetikleme | Microsoft Docs
author: wenz
description: ASP.NET AJAX Denetim Araç Seti animasyon denetiminde bir denetimi ancak animasyonları için bir denetim eklemek için tam bir çerçeve değil. Genellikle, başlatma bir...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: e5d99c2b-d8ee-413c-80d5-c120cffb0a4c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/triggering-an-animation-in-another-control-cs
msc.type: authoredcontent
ms.openlocfilehash: e94046ca70607e37c1b5ef57d5cedef67a236b94
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30868119"
---
<a name="triggering-an-animation-in-another-control-c"></a><span data-ttu-id="2c3fb-104">Başka bir denetim (C#) animasyonda tetikleme</span><span class="sxs-lookup"><span data-stu-id="2c3fb-104">Triggering an Animation in another Control (C#)</span></span>
====================
<span data-ttu-id="2c3fb-105">tarafından [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="2c3fb-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="2c3fb-106">[Kodu indirme](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation8.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation8CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="2c3fb-106">[Download Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation8.cs.zip) or [Download PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation8CS.pdf)</span></span>

> <span data-ttu-id="2c3fb-107">ASP.NET AJAX Denetim Araç Seti animasyon denetiminde bir denetimi ancak animasyonları için bir denetim eklemek için tam bir çerçeve değil.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-107">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="2c3fb-108">Bir animasyon başlatarak, aynı denetimi ile kullanıcı etkileşimi tarafından genellikle tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-108">Generally, launching an animation is triggered by user interaction with the same control.</span></span> <span data-ttu-id="2c3fb-109">Ancak ayrıca bir denetim ve sonra da animasyon başka bir denetim etkileşime mümkün.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-109">It is however also possible to interact with one control and then animation another control.</span></span>


## <a name="overview"></a><span data-ttu-id="2c3fb-110">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="2c3fb-110">Overview</span></span>

<span data-ttu-id="2c3fb-111">ASP.NET AJAX Denetim Araç Seti animasyon denetiminde bir denetimi ancak animasyonları için bir denetim eklemek için tam bir çerçeve değil.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-111">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="2c3fb-112">Bir animasyon başlatarak, aynı denetimi ile kullanıcı etkileşimi tarafından genellikle tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-112">Generally, launching an animation is triggered by user interaction with the same control.</span></span> <span data-ttu-id="2c3fb-113">Ancak ayrıca bir denetim ve sonra da animasyon başka bir denetim etkileşime mümkün.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-113">It is however also possible to interact with one control and then animation another control.</span></span>

## <a name="steps"></a><span data-ttu-id="2c3fb-114">Adımlar</span><span class="sxs-lookup"><span data-stu-id="2c3fb-114">Steps</span></span>

<span data-ttu-id="2c3fb-115">İlk olarak dahil `ScriptManager` sayfasında; daha sonra ASP.NET AJAX kitaplığı, Denetim Araç Seti kullanmayı mümkün hale getirme yüklenir:</span><span class="sxs-lookup"><span data-stu-id="2c3fb-115">First of all, include the `ScriptManager` in the page; then, the ASP.NET AJAX library is loaded, making it possible to use the Control Toolkit:</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample1.aspx)]

<span data-ttu-id="2c3fb-116">Animasyonun bir panel şöyle metin uygulanır:</span><span class="sxs-lookup"><span data-stu-id="2c3fb-116">The animation will be applied to a panel of text which looks like this:</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample2.aspx)]

<span data-ttu-id="2c3fb-117">İlişkili CSS sınıfı bölmesinin iyi arka plan rengi tanımlayın ve ayrıca sabit genişlikli bölmesinin ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="2c3fb-117">In the associated CSS class for the panel, define a nice background color and also set a fixed width for the panel:</span></span>

[!code-css[Main](triggering-an-animation-in-another-control-cs/samples/sample3.css)]

<span data-ttu-id="2c3fb-118">Bölmenin animasyon başlamak için bir HTML düğmesi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-118">In order to start animating the panel, an HTML button is used.</span></span> <span data-ttu-id="2c3fb-119">Unutmayın `<input type="button" />` üzerinden favoured `<asp:Button />` kullanıcı o düğmesine tıkladığında biz geri gönderimin istemiyorsanız bu yana.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-119">Note that `<input type="button" />` is favoured over `<asp:Button />` since we do not want a postback when the user clicks on that button.</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample4.aspx)]

<span data-ttu-id="2c3fb-120">Ardından, ekleyin `AnimationExtender` sayfasına sağlayan bir `ID`, `TargetControlID` özniteliği ve zorunlu `runat="server"`.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-120">Then, add the `AnimationExtender` to the page, providing an `ID`, the `TargetControlID` attribute and the obligatory `runat="server"`.</span></span> <span data-ttu-id="2c3fb-121">Ayarlamak önemlidir `TargetControlID` (animasyonu tetikleme öğe) düğmeye kimliği (Hareketli öğesi) paneli Kimliğini uygulanmaz</span><span class="sxs-lookup"><span data-stu-id="2c3fb-121">It is important to set `TargetControlID` to the ID of the button (the element triggering the animation), not to the ID of the panel (the element being animated)</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample5.aspx)]

<span data-ttu-id="2c3fb-122">İçinde `<Animations>` düğümü, normal olarak yer animasyonları.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-122">Within the `<Animations>` node, place animations as usual.</span></span> <span data-ttu-id="2c3fb-123">Bölmenin değişiklik yapmanız için düğmeyi değil ayarlamanız `AnimationTarget` her animasyon öğesinin içinde özniteliğinin `AnimationExtender`.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-123">In order to make them change the panel, not the button, set the `AnimationTarget` attribute for every animation element within `AnimationExtender`.</span></span> <span data-ttu-id="2c3fb-124">Değeri `AnimationTarget` Masası Elbette kimliğidir.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-124">The value for `AnimationTarget` is the ID of the panel, of course.</span></span> <span data-ttu-id="2c3fb-125">Bu şekilde animasyonları paneliyle tetikleme düğmesiyle değil gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-125">That way, the animations happen with the panel, not with the triggering button.</span></span> <span data-ttu-id="2c3fb-126">Burada `AnimationExtender` biçimlendirme bu senaryo için:</span><span class="sxs-lookup"><span data-stu-id="2c3fb-126">Here is the `AnimationExtender` markup for this scenario:</span></span>

[!code-aspx[Main](triggering-an-animation-in-another-control-cs/samples/sample6.aspx)]

<span data-ttu-id="2c3fb-127">Tek tek animasyonları göründüğü özel sıra unutmayın.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-127">Note the special order in which the individual animations appear.</span></span> <span data-ttu-id="2c3fb-128">İlk olarak animasyon çalışır sonra düğmesi devre dışı.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-128">First of all, the button gets deactivated once the animation runs.</span></span> <span data-ttu-id="2c3fb-129">Olduğundan hiçbir `AnimationTarget` özniteliğini `<EnableAction>` öğesi, bu animasyon kaynak denetimine uygulanır: düğme.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-129">Since there is no `AnimationTarget` attribute in the `<EnableAction>` element, this animation is applied to the originating control: the button.</span></span> <span data-ttu-id="2c3fb-130">Sonraki iki animasyon adımları parallelly gerçekleştirilebilmesi (`<Parallel>` öğesi).</span><span class="sxs-lookup"><span data-stu-id="2c3fb-130">The next two animation steps shall be carried out parallelly (`<Parallel>` element).</span></span> <span data-ttu-id="2c3fb-131">Hem de bunların `AnimationTarget` öznitelikleri ayarlamak `"Panel1"`, böylece düğmesi paneli animasyon ekleme.</span><span class="sxs-lookup"><span data-stu-id="2c3fb-131">Both have their `AnimationTarget` attributes set to `"Panel1"`, thus animating the panel, not the button.</span></span>


<span data-ttu-id="2c3fb-132">[![Fare tıklatma düğmesine paneli animasyonu başlatır](triggering-an-animation-in-another-control-cs/_static/image2.png)](triggering-an-animation-in-another-control-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="2c3fb-132">[![A mouse click on the button starts the panel animation](triggering-an-animation-in-another-control-cs/_static/image2.png)](triggering-an-animation-in-another-control-cs/_static/image1.png)</span></span>

<span data-ttu-id="2c3fb-133">Fare tıklatma düğmesine paneli animasyonu başlatır ([tam boyutlu görüntüyü görüntülemek için tıklatın](triggering-an-animation-in-another-control-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="2c3fb-133">A mouse click on the button starts the panel animation ([Click to view full-size image](triggering-an-animation-in-another-control-cs/_static/image3.png))</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2c3fb-134">[Önceki](disabling-actions-during-animation-cs.md)
> [sonraki](modifying-animations-from-the-server-side-cs.md)</span><span class="sxs-lookup"><span data-stu-id="2c3fb-134">[Previous](disabling-actions-during-animation-cs.md)
[Next](modifying-animations-from-the-server-side-cs.md)</span></span>
