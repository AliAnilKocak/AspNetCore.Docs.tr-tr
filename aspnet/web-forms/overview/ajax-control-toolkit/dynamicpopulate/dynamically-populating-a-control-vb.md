---
uid: web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-vb
title: Dinamik olarak denetimini (VB) doldurma | Microsoft Docs
author: wenz
description: ASP.NET AJAX Denetim Araç Seti DynamicPopulate denetiminde bir web hizmeti (veya sayfa yöntemi) çağırır ve sonuçta elde edilen değerin t hedef denetime doldurur...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 27305347-7b5d-4519-97b7-197a357e7f6e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-vb
msc.type: authoredcontent
ms.openlocfilehash: e2031a80be71a406e632955583d83920dd0f3ef7
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="dynamically-populating-a-control-vb"></a><span data-ttu-id="3810a-103">Bir denetim (VB) dinamik olarak doldurma</span><span class="sxs-lookup"><span data-stu-id="3810a-103">Dynamically Populating a Control (VB)</span></span>
====================
<span data-ttu-id="3810a-104">tarafından [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="3810a-104">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="3810a-105">[Kodu indirme](http://download.microsoft.com/download/d/8/f/d8f2f6f9-1b7c-46ad-9252-e1fc81bdea3e/dynamicpopulate0.vb.zip) veya [PDF indirin](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dynamicpopulate0VB.pdf)</span><span class="sxs-lookup"><span data-stu-id="3810a-105">[Download Code](http://download.microsoft.com/download/d/8/f/d8f2f6f9-1b7c-46ad-9252-e1fc81bdea3e/dynamicpopulate0.vb.zip) or [Download PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dynamicpopulate0VB.pdf)</span></span>

> <span data-ttu-id="3810a-106">ASP.NET AJAX Denetim Araç Seti DynamicPopulate denetiminde bir web hizmeti (veya sayfa yöntemi) çağırır ve çıkan değeri bir sayfa yenileme olmadan sayfasında hedef denetime doldurur.</span><span class="sxs-lookup"><span data-stu-id="3810a-106">The DynamicPopulate control in the ASP.NET AJAX Control Toolkit calls a web service (or page method) and fills the resulting value into a target control on the page, without a page refresh.</span></span>


## <a name="overview"></a><span data-ttu-id="3810a-107">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="3810a-107">Overview</span></span>

<span data-ttu-id="3810a-108">`DynamicPopulate` ASP.NET AJAX Denetim Araç Seti denetiminde bir web hizmeti (veya sayfa yöntemi) çağırır ve sayfa yenileme olmadan sayfasında hedef denetime sonuç değeri doldurur.</span><span class="sxs-lookup"><span data-stu-id="3810a-108">The `DynamicPopulate` control in the ASP.NET AJAX Control Toolkit calls a web service (or page method) and fills the resulting value into a target control on the page, without a page refresh.</span></span> <span data-ttu-id="3810a-109">Bu öğreticide bunu nasıl ayarlanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3810a-109">This tutorial shows how to set this up.</span></span>

## <a name="steps"></a><span data-ttu-id="3810a-110">Adımlar</span><span class="sxs-lookup"><span data-stu-id="3810a-110">Steps</span></span>

<span data-ttu-id="3810a-111">Öncelikle, ASP.NET Web hizmeti çağrılacak yöntemin uygulayan gereksinim `DynamicPopulate`.</span><span class="sxs-lookup"><span data-stu-id="3810a-111">First of all, you need an ASP.NET Web Service which implements the method to be called by `DynamicPopulate`.</span></span> <span data-ttu-id="3810a-112">Web hizmeti sınıfı gerektirir `ScriptService` içinde tanımlanan özniteliği `Microsoft.Web.Script.Services`; Aksi takdirde ASP.NET AJAX sırayla gerekli web hizmeti için istemci tarafı JavaScript proxy oluşturulamıyor `DynamicPopulate`.</span><span class="sxs-lookup"><span data-stu-id="3810a-112">The web service class requires the `ScriptService` attribute which is defined within `Microsoft.Web.Script.Services`; otherwise ASP.NET AJAX cannot create the client-side JavaScript proxy for the web service which in turn is required by `DynamicPopulate`.</span></span>

<span data-ttu-id="3810a-113">Web yöntemi adı verilen dize türünde bir bağımsız değişken bekler gerekir `contextKey`, bu yana `DynamicPopulate` denetim bağlam bilgilerini bir parçasını her web hizmeti çağrısı ile gönderir.</span><span class="sxs-lookup"><span data-stu-id="3810a-113">The web method must expect one argument of type string, called `contextKey`, since the `DynamicPopulate` control sends one piece of context information with each web service call.</span></span> <span data-ttu-id="3810a-114">Aşağıdaki web hizmeti tarafından temsil edilen bir biçiminde geçerli tarihi döndürür `contextKey` bağımsız değişkeni:</span><span class="sxs-lookup"><span data-stu-id="3810a-114">The following web service returns the current date in a format represented by the `contextKey` argument:</span></span>

[!code-aspx[Main](dynamically-populating-a-control-vb/samples/sample1.aspx)]

<span data-ttu-id="3810a-115">Web hizmeti olarak kaydedilir `DynamicPopulate.vb.asmx`.</span><span class="sxs-lookup"><span data-stu-id="3810a-115">The web service is then saved as `DynamicPopulate.vb.asmx`.</span></span> <span data-ttu-id="3810a-116">Alternatif olarak, uygulamanız `getDate()` yöntemi ile gerçek ASP.NET sayfa içinde sayfa yöntemi olarak `DynamicPopulate` denetim.</span><span class="sxs-lookup"><span data-stu-id="3810a-116">Alternatively, you could implement the `getDate()` method as a page method within the actual ASP.NET page with the `DynamicPopulate` control.</span></span>

<span data-ttu-id="3810a-117">Sonraki adımda, yeni bir ASP.NET dosyası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3810a-117">In the next step, create a new ASP.NET file.</span></span> <span data-ttu-id="3810a-118">İlk adım dahil etmek için her zaman olduğu gibi `ScriptManager` geçerli sayfada ASP.NET AJAX kitaplığı yüklenemiyor ve Denetim Araç Seti çalışmasını sağlamak için:</span><span class="sxs-lookup"><span data-stu-id="3810a-118">As always, the first step is to include the `ScriptManager` in the current page to load the ASP.NET AJAX library and to make the Control Toolkit work:</span></span>

[!code-aspx[Main](dynamically-populating-a-control-vb/samples/sample2.aspx)]

<span data-ttu-id="3810a-119">Ardından, bir etiket denetimi ekleyin (örneği için aynı ada sahip bir HTML denetimini kullanarak veya &lt; `asp:Label`  / &gt; web denetimi), daha sonra gösterir web hizmeti çağrısı sonucu.</span><span class="sxs-lookup"><span data-stu-id="3810a-119">Then, add a label control (for instance using the HTML control of the same name, or the &lt;`asp:Label` /&gt; web control) which will later show the result of the web service call.</span></span>

[!code-aspx[Main](dynamically-populating-a-control-vb/samples/sample3.aspx)]

<span data-ttu-id="3810a-120">Bir HTML düğmesi (olarak sunucuya geri gönderimin gerektirmeyen bu yana bir HTML denetimini), ardından dinamik popülasyon tetiklemek için kullanılacak:</span><span class="sxs-lookup"><span data-stu-id="3810a-120">An HTML button (as an HTML control, since we do not require a postback to the server) will then be used to trigger the dynamic population:</span></span>

[!code-aspx[Main](dynamically-populating-a-control-vb/samples/sample4.aspx)]

<span data-ttu-id="3810a-121">Son olarak, ihtiyacımız `DynamicPopulateExtender` kablo işlemleri için denetim.</span><span class="sxs-lookup"><span data-stu-id="3810a-121">Finally, we need the `DynamicPopulateExtender` control to wire things up.</span></span> <span data-ttu-id="3810a-122">Aşağıdaki öznitelikler ayarlayın (belirgin olanlar dışında `ID` ve `runat` = `"server"`):</span><span class="sxs-lookup"><span data-stu-id="3810a-122">The following attributes will be set (apart from the obvious ones, `ID` and `runat`=`"server"`):</span></span>

- <span data-ttu-id="3810a-123">`TargetControlID` web hizmeti çağrısından sonucu bulunacağı yer</span><span class="sxs-lookup"><span data-stu-id="3810a-123">`TargetControlID` where to put the result from the web service call</span></span>
- <span data-ttu-id="3810a-124">`ServicePath` web hizmeti yoluna (sayfa yöntemi kullanmak istiyorsanız, atla)</span><span class="sxs-lookup"><span data-stu-id="3810a-124">`ServicePath` path to the web service (omit if you want to use a page method)</span></span>
- <span data-ttu-id="3810a-125">`ServiceMethod` web yöntemi veya sayfa yöntemi adı</span><span class="sxs-lookup"><span data-stu-id="3810a-125">`ServiceMethod` name of the web method or page method</span></span>
- <span data-ttu-id="3810a-126">`ContextKey` web hizmetine gönderilmesini bağlam bilgileri</span><span class="sxs-lookup"><span data-stu-id="3810a-126">`ContextKey` context information to be sent to the web service</span></span>
- <span data-ttu-id="3810a-127">`PopulateTriggerControlID` web hizmeti çağrısı tetikler öğesi</span><span class="sxs-lookup"><span data-stu-id="3810a-127">`PopulateTriggerControlID` element which triggers the web service call</span></span>
- <span data-ttu-id="3810a-128">`ClearContentsDuringUpdate` web hizmeti çağrısı sırasında hedef öğe boş verilip</span><span class="sxs-lookup"><span data-stu-id="3810a-128">`ClearContentsDuringUpdate` whether to empty the target element during the web service call</span></span>

<span data-ttu-id="3810a-129">Gördüğünüz gibi bazı bilgiler denetimi gerektiriyor, ancak her şeyi yerine koyma oldukça düz ilet.</span><span class="sxs-lookup"><span data-stu-id="3810a-129">As you can see, the control requires some information but putting everything into place is quite straight-forward.</span></span> <span data-ttu-id="3810a-130">İşaretleme için işte `DynamicPopulateExtender` Geçerli senaryoda denetimi:</span><span class="sxs-lookup"><span data-stu-id="3810a-130">Here is the markup for the `DynamicPopulateExtender` control in the current scenario:</span></span>

[!code-aspx[Main](dynamically-populating-a-control-vb/samples/sample5.aspx)]

<span data-ttu-id="3810a-131">ASP.NET sayfasını tarayıcıda çalışmasına ve düğmeyi tıklatın; ay gün yıl biçiminde geçerli tarihi alır.</span><span class="sxs-lookup"><span data-stu-id="3810a-131">Run the ASP.NET page in the browser and click on the button; you will receive the current date in month-day-year format.</span></span>


<span data-ttu-id="3810a-132">[![Düğme tıklama tarihi sunucudan alır.](dynamically-populating-a-control-vb/_static/image2.png)](dynamically-populating-a-control-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="3810a-132">[![A click on the button retrieves the date from the server](dynamically-populating-a-control-vb/_static/image2.png)](dynamically-populating-a-control-vb/_static/image1.png)</span></span>

<span data-ttu-id="3810a-133">Düğme tıklama sunucudan tarihi alır ([tam boyutlu görüntüyü görüntülemek için tıklatın](dynamically-populating-a-control-vb/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="3810a-133">A click on the button retrieves the date from the server ([Click to view full-size image](dynamically-populating-a-control-vb/_static/image3.png))</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3810a-134">[Önceki](using-dynamicpopulate-with-a-user-control-and-javascript-cs.md)
> [sonraki](dynamically-populating-a-control-using-javascript-code-vb.md)</span><span class="sxs-lookup"><span data-stu-id="3810a-134">[Previous](using-dynamicpopulate-with-a-user-control-and-javascript-cs.md)
[Next](dynamically-populating-a-control-using-javascript-code-vb.md)</span></span>
