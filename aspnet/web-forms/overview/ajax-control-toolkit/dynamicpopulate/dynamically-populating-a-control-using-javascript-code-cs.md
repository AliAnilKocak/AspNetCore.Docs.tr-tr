---
uid: web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-using-javascript-code-cs
title: Dinamik olarak JavaScript kodu (C#) kullanarak denetim doldurma | Microsoft Docs
author: wenz
description: "ASP.NET AJAX Denetim Araç Seti DynamicPopulate denetiminde bir web hizmeti (veya sayfa yöntemi) çağırır ve sonuçta elde edilen değerin t hedef denetime doldurur..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: cc4c2def-e88c-4456-ae8b-a6ae0ff8cc2d
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dynamicpopulate/dynamically-populating-a-control-using-javascript-code-cs
msc.type: authoredcontent
ms.openlocfilehash: 5b3b23e16f2e4dd26f50eb3e07f35d078dd19a19
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="dynamically-populating-a-control-using-javascript-code-c"></a><span data-ttu-id="6733c-103">JavaScript kodu (C#) kullanarak denetim dinamik olarak doldurma</span><span class="sxs-lookup"><span data-stu-id="6733c-103">Dynamically Populating a Control Using JavaScript Code (C#)</span></span>
====================
<span data-ttu-id="6733c-104">tarafından [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="6733c-104">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="6733c-105">[Kodu indirme](http://download.microsoft.com/download/d/8/f/d8f2f6f9-1b7c-46ad-9252-e1fc81bdea3e/dynamicpopulate1.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dynamicpopulate1CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="6733c-105">[Download Code](http://download.microsoft.com/download/d/8/f/d8f2f6f9-1b7c-46ad-9252-e1fc81bdea3e/dynamicpopulate1.cs.zip) or [Download PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dynamicpopulate1CS.pdf)</span></span>

> <span data-ttu-id="6733c-106">ASP.NET AJAX Denetim Araç Seti DynamicPopulate denetiminde bir web hizmeti (veya sayfa yöntemi) çağırır ve çıkan değeri bir sayfa yenileme olmadan sayfasında hedef denetime doldurur.</span><span class="sxs-lookup"><span data-stu-id="6733c-106">The DynamicPopulate control in the ASP.NET AJAX Control Toolkit calls a web service (or page method) and fills the resulting value into a target control on the page, without a page refresh.</span></span> <span data-ttu-id="6733c-107">Özel istemci tarafı JavaScript kodu kullanarak popülasyon tetiklemek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="6733c-107">It is also possible to trigger the population using custom client-side JavaScript code.</span></span>


## <a name="overview"></a><span data-ttu-id="6733c-108">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="6733c-108">Overview</span></span>

<span data-ttu-id="6733c-109">`DynamicPopulate` ASP.NET AJAX Denetim Araç Seti denetiminde bir web hizmeti (veya sayfa yöntemi) çağırır ve sayfa yenileme olmadan sayfasında hedef denetime sonuç değeri doldurur.</span><span class="sxs-lookup"><span data-stu-id="6733c-109">The `DynamicPopulate` control in the ASP.NET AJAX Control Toolkit calls a web service (or page method) and fills the resulting value into a target control on the page, without a page refresh.</span></span> <span data-ttu-id="6733c-110">Özel istemci tarafı JavaScript kodu kullanarak popülasyon tetiklemek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="6733c-110">It is also possible to trigger the population using custom client-side JavaScript code.</span></span>

## <a name="steps"></a><span data-ttu-id="6733c-111">Adımlar</span><span class="sxs-lookup"><span data-stu-id="6733c-111">Steps</span></span>

<span data-ttu-id="6733c-112">Öncelikle, ASP.NET Web hizmeti çağrılacak yöntemin uygulayan gereksinim `DynamicPopulateExtender` denetim.</span><span class="sxs-lookup"><span data-stu-id="6733c-112">First of all, you need an ASP.NET Web Service which implements the method to be called by the `DynamicPopulateExtender` control.</span></span> <span data-ttu-id="6733c-113">Web hizmeti yöntemi uygulayan `getDate()` adlı dize türünde bir bağımsız değişken bekler `contextKey`, bu yana `DynamicPopulate` denetim bağlam bilgilerini bir parçasını her web hizmeti çağrısı ile gönderir.</span><span class="sxs-lookup"><span data-stu-id="6733c-113">The web service implements the method `getDate()` that expects one argument of type string, called `contextKey`, since the `DynamicPopulate` control sends one piece of context information with each web service call.</span></span> <span data-ttu-id="6733c-114">Kod aşağıdaki gibidir (dosya `DynamicPopulate.cs.asmx`) üç biçimlerden birinde geçerli tarihi alır:</span><span class="sxs-lookup"><span data-stu-id="6733c-114">Here is the code (file `DynamicPopulate.cs.asmx`) which retrieves the current date in one of three formats:</span></span>

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-cs/samples/sample1.aspx)]

<span data-ttu-id="6733c-115">Sonraki adım, yeni bir ASP.NET sitesi oluşturmak ve ASP.NET AJAX ScriptManager denetimi ile başlatın:</span><span class="sxs-lookup"><span data-stu-id="6733c-115">In the next step, create a new ASP.NET site and start with the ASP.NET AJAX ScriptManager control:</span></span>

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-cs/samples/sample2.aspx)]

<span data-ttu-id="6733c-116">Ardından, bir etiket denetimi ekleyin (örneği için aynı ada sahip bir HTML denetimini kullanarak veya `<asp:Label />` web denetimi), daha sonra gösterir web hizmeti çağrısı sonucu.</span><span class="sxs-lookup"><span data-stu-id="6733c-116">Then, add a label control (for instance using the HTML control of the same name, or the `<asp:Label />` web control) which will later show the result of the web service call.</span></span>

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-cs/samples/sample3.aspx)]

<span data-ttu-id="6733c-117">Ardından, içeren bir `DynamicPopulateExtender` denetlemek ve web hizmeti bilgileri, hedef denetimi, ancak özel JavaScript kullanarak bu yapılır daha sonra popülasyon tetikler denetim adını girin!</span><span class="sxs-lookup"><span data-stu-id="6733c-117">Next, include a `DynamicPopulateExtender` control and provide web service information, target control, but not the name of the control which triggers the population this will be done later on, using custom JavaScript!</span></span>

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-cs/samples/sample4.aspx)]

<span data-ttu-id="6733c-118">Şimdi için JavaScript bölümü.</span><span class="sxs-lookup"><span data-stu-id="6733c-118">Now to the JavaScript part.</span></span> <span data-ttu-id="6733c-119">`$find()` ASP.NET AJAX kitaplığı tarafından tanımlanan işlevi, ASP.NET AJAX Denetim Araç Seti sunucu tarafı nesnelerin başvuru gibi döndürür `DynamicPopulateExtender`.</span><span class="sxs-lookup"><span data-stu-id="6733c-119">The `$find()` function, defined by the ASP.NET AJAX library, returns a reference to server-side objects of the ASP.NET AJAX Control Toolkit such as `DynamicPopulateExtender`.</span></span> <span data-ttu-id="6733c-120">Geçerli dosyasındaki `$find("dpe")` bir başvuru döndürür `DynamicPopulateExtender` sayfasında denetimi.</span><span class="sxs-lookup"><span data-stu-id="6733c-120">In the current file, `$find("dpe")` returns a reference to the one `DynamicPopulateExtender` control in the page.</span></span> <span data-ttu-id="6733c-121">Adlı bir yöntemi gösterir `populate()` dinamik doldurma işlemini tetikler.</span><span class="sxs-lookup"><span data-stu-id="6733c-121">It exposes a method called `populate()` which triggers the dynamic population process.</span></span> <span data-ttu-id="6733c-122">`populate()` Yöntemi bir bağımsız değişken gerektirir: bağımsız değişken olarak hizmet verecektir içerik anahtarı `getDate()` web yöntemi.</span><span class="sxs-lookup"><span data-stu-id="6733c-122">The `populate()` method requires one argument: the context key which will serve as argument to the `getDate()` web method.</span></span> <span data-ttu-id="6733c-123">Bu nedenle örneğin `$find("dpe").populate("format1")` ay gün yıl biçiminde geçerli tarihi etiketle doldurmak.</span><span class="sxs-lookup"><span data-stu-id="6733c-123">So for instance, `$find("dpe").populate("format1")` would populate the label with the current date in month-day-year format.</span></span>

<span data-ttu-id="6733c-124">Örnek biraz daha esnek hale getirmek için kullanıcı artık çeşitli tarih biçimleri arasında tercih edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6733c-124">In order to make the sample a bit more flexible, the user may now choose between several date formats.</span></span> <span data-ttu-id="6733c-125">Bunların her biri için bir radyo düğmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6733c-125">For each one of them, a radio button is displayed.</span></span> <span data-ttu-id="6733c-126">Bir kez kullanıcı tıklama radyo düğmesine JavaScript kodu seçili tarih biçimiyle etiketi dinamik olarak doldurur.</span><span class="sxs-lookup"><span data-stu-id="6733c-126">Once the user clicks on a radio button, JavaScript code dynamically populates the label with the selected date format.</span></span> <span data-ttu-id="6733c-127">Bu radyo düğmeleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="6733c-127">Here are those radio buttons:</span></span>

[!code-aspx[Main](dynamically-populating-a-control-using-javascript-code-cs/samples/sample5.aspx)]

<span data-ttu-id="6733c-128">Radyo düğmesi, JavaScript ifadesi bağlamında unutmayın `this.value` tam olarak aynı bilgiler için geçerli düğmesini değerine başvuruyor `getDate()` yöntemi ile çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="6733c-128">Note that within the context of a radio button, the JavaScript expression `this.value` refers to the value of the current button, which happens to be exactly the same information the `getDate()` method can work with.</span></span>


<span data-ttu-id="6733c-129">[![Düğme tıklama sunucusundan belirtilen biçimde tarihi alır.](dynamically-populating-a-control-using-javascript-code-cs/_static/image2.png)](dynamically-populating-a-control-using-javascript-code-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="6733c-129">[![A click on the button retrieves the date from the server, in the format specified](dynamically-populating-a-control-using-javascript-code-cs/_static/image2.png)](dynamically-populating-a-control-using-javascript-code-cs/_static/image1.png)</span></span>

<span data-ttu-id="6733c-130">Düğme tıklama sunucusundan belirtilen biçimde tarihi alır ([tam boyutlu görüntüyü görüntülemek için tıklatın](dynamically-populating-a-control-using-javascript-code-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="6733c-130">A click on the button retrieves the date from the server, in the format specified ([Click to view full-size image](dynamically-populating-a-control-using-javascript-code-cs/_static/image3.png))</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="6733c-131">[Önceki](dynamically-populating-a-control-cs.md)
[sonraki](using-dynamicpopulate-with-a-user-control-and-javascript-cs.md)</span><span class="sxs-lookup"><span data-stu-id="6733c-131">[Previous](dynamically-populating-a-control-cs.md)
[Next](using-dynamicpopulate-with-a-user-control-and-javascript-cs.md)</span></span>
