---
uid: web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-with-validation-controls-vb
title: TextBoxWatermark doğrulama denetimleri (VB) ile kullanma | Microsoft Docs
author: wenz
description: Böylece bir metin kutusu içinde görüntülenir AJAX Denetim Araç Seti TextBoxWatermark denetiminde bir metin kutusu genişletir. Bir kullanıcı kutunun içine tıkladığında, ı...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: e6c2cb98-f745-4bc8-973a-813879c8a891
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-with-validation-controls-vb
msc.type: authoredcontent
ms.openlocfilehash: 0ca1a4af62af1d65525e59d0b7bc47245dd01476
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="using-textboxwatermark-with-validation-controls-vb"></a><span data-ttu-id="50eb5-104">TextBoxWatermark doğrulama denetimleri (VB) ile kullanma</span><span class="sxs-lookup"><span data-stu-id="50eb5-104">Using TextBoxWatermark With Validation Controls (VB)</span></span>
====================
<span data-ttu-id="50eb5-105">tarafından [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="50eb5-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="50eb5-106">[Kodu indirme](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/TextBoxWatermark2.vb.zip) veya [PDF indirin](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/textboxwatermark2VB.pdf)</span><span class="sxs-lookup"><span data-stu-id="50eb5-106">[Download Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/TextBoxWatermark2.vb.zip) or [Download PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/textboxwatermark2VB.pdf)</span></span>

> <span data-ttu-id="50eb5-107">Böylece bir metin kutusu içinde görüntülenir AJAX Denetim Araç Seti TextBoxWatermark denetiminde bir metin kutusu genişletir.</span><span class="sxs-lookup"><span data-stu-id="50eb5-107">The TextBoxWatermark control in the AJAX Control Toolkit extends a text box so that a text is displayed within the box.</span></span> <span data-ttu-id="50eb5-108">Bir kullanıcı kutunun içine tıkladığında boşaltılır.</span><span class="sxs-lookup"><span data-stu-id="50eb5-108">When a user clicks into the box, it is emptied.</span></span> <span data-ttu-id="50eb5-109">Kullanıcı kutunun metin girmeden ayrılırsa, doldurulmuş metin görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="50eb5-109">If the user leaves the box without entering text, the prefilled text reappears.</span></span> <span data-ttu-id="50eb5-110">Bu aynı sayfada ASP.NET doğrulama denetimleri ile çakışabilir, ancak bu sorunlarının üstesinden.</span><span class="sxs-lookup"><span data-stu-id="50eb5-110">This may collide with ASP.NET Validation Controls on the same page, but these issues may be overcome.</span></span>


## <a name="overview"></a><span data-ttu-id="50eb5-111">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="50eb5-111">Overview</span></span>

<span data-ttu-id="50eb5-112">`TextBoxWatermark` AJAX Denetim Araç Seti denetiminde genişleten bir metin kutusu böylece bir metin kutusu içinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="50eb5-112">The `TextBoxWatermark` control in the AJAX Control Toolkit extends a text box so that a text is displayed within the box.</span></span> <span data-ttu-id="50eb5-113">Bir kullanıcı kutunun içine tıkladığında boşaltılır.</span><span class="sxs-lookup"><span data-stu-id="50eb5-113">When a user clicks into the box, it is emptied.</span></span> <span data-ttu-id="50eb5-114">Kullanıcı kutunun metin girmeden ayrılırsa, doldurulmuş metin görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="50eb5-114">If the user leaves the box without entering text, the prefilled text reappears.</span></span> <span data-ttu-id="50eb5-115">Bu aynı sayfada ASP.NET doğrulama denetimleri ile çakışabilir, ancak bu sorunlarının üstesinden.</span><span class="sxs-lookup"><span data-stu-id="50eb5-115">This may collide with ASP.NET Validation Controls on the same page, but these issues may be overcome.</span></span>

## <a name="steps"></a><span data-ttu-id="50eb5-116">Adımlar</span><span class="sxs-lookup"><span data-stu-id="50eb5-116">Steps</span></span>

<span data-ttu-id="50eb5-117">Örnek temel kurulumu aşağıdaki gibidir: bir `TextBox` denetim Filigran kullanarak bir `TextBoxWatermarkExtender` denetim.</span><span class="sxs-lookup"><span data-stu-id="50eb5-117">The basic setup of the sample is the following: a `TextBox` control is watermarked using a `TextBoxWatermarkExtender` control.</span></span> <span data-ttu-id="50eb5-118">Bir düğme geri gönderimin tetikler ve daha sonra sayfada doğrulama denetimleri tetiklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="50eb5-118">A button triggers a postback and will later be used to trigger the validation controls on the page.</span></span> <span data-ttu-id="50eb5-119">Ayrıca, bir `ScriptManager` denetim, ASP.NET AJAX'ı başlatmak için gereklidir:</span><span class="sxs-lookup"><span data-stu-id="50eb5-119">Also, a `ScriptManager` control is required to initialize ASP.NET AJAX:</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample1.aspx)]

<span data-ttu-id="50eb5-120">Şimdi ekleyin bir `RequiredFieldValidator` olup olmadığını metin alanı form gönderildiğinde denetleyen denetimi.</span><span class="sxs-lookup"><span data-stu-id="50eb5-120">Now add a `RequiredFieldValidator` control that checks whether there is text in the field when the form is submitted.</span></span> <span data-ttu-id="50eb5-121">`InitialValue` Doğrulayıcının özelliğini ayarlayın, kullanılan aynı değere `TextBoxWatermarkExtender` denetimi: form gönderildiğinde değiştirilmemiş bir metin kutusu içindeki filigran değeri değeri:</span><span class="sxs-lookup"><span data-stu-id="50eb5-121">The `InitialValue` property of the validator must be set to the same value that is used in the `TextBoxWatermarkExtender` control: When the form is submitted, the value of an unchanged textbox is the watermark value within it:</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample2.aspx)]

<span data-ttu-id="50eb5-122">Ancak bir sorun var. Bu yaklaşım: JavaScript istemci devre dışı bırakır, metin alanı filigran metni ile bu nedenle doldurulmuş değil `RequiredFieldValidator` bir hata iletisi tetiklemez.</span><span class="sxs-lookup"><span data-stu-id="50eb5-122">However there is one problem with this approach: If the client disables JavaScript, the text field is not prefilled with the watermark text, therefore the `RequiredFieldValidator` does not trigger an error message.</span></span> <span data-ttu-id="50eb5-123">Bu nedenle, ikinci `RequiredFieldValidator` denetimi gereklidir, boş bir metin kutusu için denetler (atlama `InitialValue` özniteliği).</span><span class="sxs-lookup"><span data-stu-id="50eb5-123">Therefore, a second `RequiredFieldValidator` control is required which checks for an empty text box (omitting the `InitialValue` attribute).</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample3.aspx)]

<span data-ttu-id="50eb5-124">Her iki Doğrulayıcıların kullandığından `Display` = `"Dynamic"`, son kullanıcının hangi iki Doğrulayıcıların tetiklenen görsel görünümünü ayırt edemez; oluştu yalnızca bunlardan birinin bunun yerine, görülüyor.</span><span class="sxs-lookup"><span data-stu-id="50eb5-124">Since both validators use `Display`=`"Dynamic"`, the end user cannot distinguish from the visual appearance which of the two validators was fired; instead, it looks like there was only one of them.</span></span>

<span data-ttu-id="50eb5-125">Son olarak, bir hata iletisi Doğrulayıcı verilen olursa metin alanına çıkış için bazı sunucu tarafı kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="50eb5-125">Finally, add some server-side code to output the text in the field if no validator issued an error message:</span></span>

[!code-aspx[Main](using-textboxwatermark-with-validation-controls-vb/samples/sample4.aspx)]


<span data-ttu-id="50eb5-126">[![Doğrulayıcı alanı metin yok complains](using-textboxwatermark-with-validation-controls-vb/_static/image2.png)](using-textboxwatermark-with-validation-controls-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="50eb5-126">[![The validator complains that there is no text in the field](using-textboxwatermark-with-validation-controls-vb/_static/image2.png)](using-textboxwatermark-with-validation-controls-vb/_static/image1.png)</span></span>

<span data-ttu-id="50eb5-127">Doğrulayıcı alanı metin yok complains ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-textboxwatermark-with-validation-controls-vb/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="50eb5-127">The validator complains that there is no text in the field ([Click to view full-size image](using-textboxwatermark-with-validation-controls-vb/_static/image3.png))</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="50eb5-128">Önceki</span><span class="sxs-lookup"><span data-stu-id="50eb5-128">Previous</span></span>](using-textboxwatermark-in-a-formview-vb.md)
