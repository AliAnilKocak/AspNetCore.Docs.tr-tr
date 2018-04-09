---
uid: web-forms/overview/ajax-control-toolkit/popup/handling-postbacks-from-a-popup-control-without-an-updatepanel-cs
title: Geri göndermeler bir UpdatePanel (C#) olmadan açılan kontrolü işleme | Microsoft Docs
author: wenz
description: AJAX Denetim araç setindeki PopupControl genişletici herhangi bir denetimi etkinleştirildiğinde popup tetiklemek için kolay bir yol sunar. Su içinde geri gönderimin meydana geldiğinde...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 25444121-5a72-4dac-8e50-ad2b7ac667af
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/popup/handling-postbacks-from-a-popup-control-without-an-updatepanel-cs
msc.type: authoredcontent
ms.openlocfilehash: 59ffa05945289de6e01e2c21dd5a0f82ca1fa374
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="handling-postbacks-from-a-popup-control-without-an-updatepanel-c"></a><span data-ttu-id="cf0b1-104">Geri göndermeler bir UpdatePanel (C#) olmadan açılan kontrolü işleme</span><span class="sxs-lookup"><span data-stu-id="cf0b1-104">Handling Postbacks from A Popup Control Without an UpdatePanel (C#)</span></span>
====================
<span data-ttu-id="cf0b1-105">tarafından [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="cf0b1-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="cf0b1-106">[Kodu indirme](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/PopupControl3.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/popupcontrol3CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="cf0b1-106">[Download Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/PopupControl3.cs.zip) or [Download PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/popupcontrol3CS.pdf)</span></span>

> <span data-ttu-id="cf0b1-107">AJAX Denetim araç setindeki PopupControl genişletici herhangi bir denetimi etkinleştirildiğinde popup tetiklemek için kolay bir yol sunar.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-107">The PopupControl extender in the AJAX Control Toolkit offers an easy way to trigger a popup when any other control is activated.</span></span> <span data-ttu-id="cf0b1-108">Bu tür bir panelinde geri gönderimin oluştuğu ve sayfada birkaç paneller yoksa hangi panele tıklattınız belirlemek zordur.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-108">When a postback occurs in such a panel and there are several panels on the page it is hard to determine which panel has been clicked.</span></span>


## <a name="overview"></a><span data-ttu-id="cf0b1-109">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="cf0b1-109">Overview</span></span>

<span data-ttu-id="cf0b1-110">AJAX Denetim araç setindeki PopupControl genişletici herhangi bir denetimi etkinleştirildiğinde popup tetiklemek için kolay bir yol sunar.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-110">The PopupControl extender in the AJAX Control Toolkit offers an easy way to trigger a popup when any other control is activated.</span></span> <span data-ttu-id="cf0b1-111">Bu tür bir panelinde geri gönderimin oluştuğu ve sayfada birkaç paneller yoksa hangi panele tıklattınız belirlemek zordur.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-111">When a postback occurs in such a panel and there are several panels on the page it is hard to determine which panel has been clicked.</span></span>

## <a name="steps"></a><span data-ttu-id="cf0b1-112">Adımlar</span><span class="sxs-lookup"><span data-stu-id="cf0b1-112">Steps</span></span>

<span data-ttu-id="cf0b1-113">Kullanırken bir `PopupControl` geri gönderimin olan, ancak sahip olmayan bir `UpdatePanel` sayfasında Denetim Araç Seti, hangi istemci öğesi sırayla geri gönderme neden açılan başlatmış olabilir belirlemek için bir yol sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-113">When using a `PopupControl` with a postback, but without having an `UpdatePanel` on the page, the Control Toolkit does not offer a way to determine which client element has triggered the popup which in turn caused the postback.</span></span> <span data-ttu-id="cf0b1-114">Ancak küçük eli bu senaryo için geçici bir çözüm sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-114">However a small trick provides a workaround for this scenario.</span></span>

<span data-ttu-id="cf0b1-115">Öncelikle, temel kurulum şöyledir: hangi hem aynı açılan, takvimi tetikleyen iki metin kutusu.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-115">First of all, here is the basic setup: two text boxes which both trigger the same popup, a calendar.</span></span> <span data-ttu-id="cf0b1-116">İki `PopupControlExtenders` metin kutuları ve açılan bir araya getirme.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-116">Two `PopupControlExtenders` bring text boxes and popup together.</span></span>

[!code-aspx[Main](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/samples/sample1.aspx)]

<span data-ttu-id="cf0b1-117">Gizli form alanına eklemek için temel fikirdir &lt; `form` &gt; popup başlattı metin kutusunu tutan öğe:</span><span class="sxs-lookup"><span data-stu-id="cf0b1-117">The basic idea is to add a hidden form field in the &lt;`form`&gt; element that holds the text box which launched the popup:</span></span>

[!code-aspx[Main](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/samples/sample2.aspx)]

<span data-ttu-id="cf0b1-118">Sayfa yüklendiğinde, JavaScript kodu olay işleyicisi iki metin kutusuna ekler.: kullanıcı bir metin kutusunda tıkladığında adını gizli form alanına yazılır:</span><span class="sxs-lookup"><span data-stu-id="cf0b1-118">When the page is loaded, JavaScript code adds an event handler to both text boxes: Whenever the user clicks on a text box, its name is written into the hidden form field:</span></span>

[!code-html[Main](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/samples/sample3.html)]

<span data-ttu-id="cf0b1-119">Sunucu tarafı kodu gizli alanın değerini okumalısınız.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-119">In the server-side code, the value of the hidden field must be read.</span></span> <span data-ttu-id="cf0b1-120">Gizli form alanlarını yönetmek için Önemsiz olduğundan, gizli değeri doğrulamak için bir beyaz liste yaklaşım gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-120">Since hidden form fields are trivial to manipulate, a whitelist approach to validate the hidden value is required.</span></span> <span data-ttu-id="cf0b1-121">Doğru metin kutusunda belirlendikten sonra takvimden tarihi üzerine yazılır.</span><span class="sxs-lookup"><span data-stu-id="cf0b1-121">Once the correct text box has been identified, the date from the calendar is written into it.</span></span>

[!code-aspx[Main](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/samples/sample4.aspx)]


<span data-ttu-id="cf0b1-122">[![Takvim kullanıcı metin kutusuna tıkladığında görüntülenir](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image2.png)](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="cf0b1-122">[![The Calendar appears when the user clicks into the textbox](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image2.png)](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image1.png)</span></span>

<span data-ttu-id="cf0b1-123">Takvim kullanıcı metin kutusuna tıklattığında görüntülenir ([tam boyutlu görüntüyü görüntülemek için tıklatın](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="cf0b1-123">The Calendar appears when the user clicks into the textbox ([Click to view full-size image](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image3.png))</span></span>


<span data-ttu-id="cf0b1-124">[![Bir tarihte tıklatarak metin kutusuna yerleştirir](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image5.png)](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image4.png)</span><span class="sxs-lookup"><span data-stu-id="cf0b1-124">[![Clicking on a date puts it in the textbox](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image5.png)](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image4.png)</span></span>

<span data-ttu-id="cf0b1-125">Bir tarihte tıklatarak koyar, metin kutusuna ([tam boyutlu görüntüyü görüntülemek için tıklatın](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image6.png))</span><span class="sxs-lookup"><span data-stu-id="cf0b1-125">Clicking on a date puts it in the textbox ([Click to view full-size image](handling-postbacks-from-a-popup-control-without-an-updatepanel-cs/_static/image6.png))</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cf0b1-126">[Önceki](handling-postbacks-from-a-popup-control-with-an-updatepanel-cs.md)
> [sonraki](using-multiple-popup-controls-vb.md)</span><span class="sxs-lookup"><span data-stu-id="cf0b1-126">[Previous](handling-postbacks-from-a-popup-control-with-an-updatepanel-cs.md)
[Next](using-multiple-popup-controls-vb.md)</span></span>
