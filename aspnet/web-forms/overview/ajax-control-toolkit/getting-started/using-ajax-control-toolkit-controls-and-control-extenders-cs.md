---
uid: web-forms/overview/ajax-control-toolkit/getting-started/using-ajax-control-toolkit-controls-and-control-extenders-cs
title: "AJAX Denetim Araç Seti denetimleri ve denetim Extender'larının (C#) kullanarak | Microsoft Docs"
author: microsoft
description: "AJAX Denetim Araç Seti denetimleri ve Extender'larının, ASP.NET sayfaları eklemeyi öğrenin."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: c1e6b51c-3bc3-4bf7-9916-9991197af3dd
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/getting-started/using-ajax-control-toolkit-controls-and-control-extenders-cs
msc.type: authoredcontent
ms.openlocfilehash: 42a210ac41e83e2379aa64979f42ce66c843f878
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="using-ajax-control-toolkit-controls-and-control-extenders-c"></a><span data-ttu-id="f5217-103">AJAX Denetim Araç Seti denetimleri ve denetim Extender'larının (C#) kullanarak</span><span class="sxs-lookup"><span data-stu-id="f5217-103">Using AJAX Control Toolkit Controls and Control Extenders (C#)</span></span>
====================
<span data-ttu-id="f5217-104">tarafından [Microsoft](https://github.com/microsoft)</span><span class="sxs-lookup"><span data-stu-id="f5217-104">by [Microsoft](https://github.com/microsoft)</span></span>

> <span data-ttu-id="f5217-105">AJAX Denetim Araç Seti denetimleri ve Extender'larının, ASP.NET sayfaları eklemeyi öğrenin.</span><span class="sxs-lookup"><span data-stu-id="f5217-105">Learn how to add AJAX Control Toolkit controls and extenders to your ASP.NET pages.</span></span>


<span data-ttu-id="f5217-106">AJAX Denetim Araç Seti denetimler ve denetim Extender'larının kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="f5217-106">The AJAX Control Toolkit contains a set of controls and control extenders.</span></span> <span data-ttu-id="f5217-107">Kısa Bu öğreticide, bir ASP.NET sayfasına denetimler ve denetim Extender'larının ekleme öğrenin.</span><span class="sxs-lookup"><span data-stu-id="f5217-107">In this brief tutorial, you learn how to add both controls and control extenders to an ASP.NET page.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="f5217-108">AJAX Denetim Araç Seti yükleme ve AJAX Denetim Araç Seti Visual Studio/Visual Web Developer araç kutusuna ekleme hakkında yönergeler için bkz öğretici [AJAX Denetim Araç Seti ile çalışmaya başlama](get-started-with-the-ajax-control-toolkit-cs.md).</span><span class="sxs-lookup"><span data-stu-id="f5217-108">For instructions on installing the AJAX Control Toolkit and adding the AJAX Control Toolkit to the Visual Studio/Visual Web Developer toolbox, see the tutorial [Get Started with the AJAX Control Toolkit](get-started-with-the-ajax-control-toolkit-cs.md).</span></span>


## <a name="using-ajax-control-toolkit-controls"></a><span data-ttu-id="f5217-109">AJAX Denetim Araç Seti denetimlerini kullanma</span><span class="sxs-lookup"><span data-stu-id="f5217-109">Using AJAX Control Toolkit Controls</span></span>

<span data-ttu-id="f5217-110">AJAX Denetim Araç Seti denetimi yalnızca normal ASP.NET denetim gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="f5217-110">An AJAX Control Toolkit control works just like a normal ASP.NET control.</span></span> <span data-ttu-id="f5217-111">Bir ASP.NET sayfasının Kutusu'ndan denetimi sürükleyin.</span><span class="sxs-lookup"><span data-stu-id="f5217-111">You can drag the control from the toolbox onto an ASP.NET page.</span></span> <span data-ttu-id="f5217-112">Tasarım görünümü veya kaynak görünümü sayfası denetimi ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f5217-112">You can add the control to the page in either Design view or Source view.</span></span>

<span data-ttu-id="f5217-113">AJAX Denetim Araç Seti denetimlerden kullanırken özel gereksinim yoktur.</span><span class="sxs-lookup"><span data-stu-id="f5217-113">There is one special requirement when using the controls from the AJAX Control Toolkit.</span></span> <span data-ttu-id="f5217-114">Sayfada bir ScriptManager denetimi içermesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="f5217-114">The page must contain a ScriptManager control.</span></span> <span data-ttu-id="f5217-115">ScriptManager denetimi tüm AJAX Denetim Araç Seti denetimleri tarafından gerekli gerekli JavaScript dahil etmek için sorumludur.</span><span class="sxs-lookup"><span data-stu-id="f5217-115">The ScriptManager control is responsible for including all of the necessary JavaScript required by the AJAX Control Toolkit controls.</span></span>

<span data-ttu-id="f5217-116">Örneğin, AJAX Denetim Araç Seti sekmesi düzenleyici denetimi adında bir denetimi içerir.</span><span class="sxs-lookup"><span data-stu-id="f5217-116">For example, the AJAX Control Toolkit tab includes a control named the Editor control.</span></span> <span data-ttu-id="f5217-117">Bu denetim zengin HTML düzenleyicisi gösterir.</span><span class="sxs-lookup"><span data-stu-id="f5217-117">This control displays a rich HTML editor.</span></span> <span data-ttu-id="f5217-118">Bir düzenleyici denetimi eklemek için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="f5217-118">Follow these steps to add the Editor control to a page:</span></span>

1. <span data-ttu-id="f5217-119">ShowEditor.aspx adlı yeni bir ASP.NET sayfası oluşturun</span><span class="sxs-lookup"><span data-stu-id="f5217-119">Create a new ASP.NET page named ShowEditor.aspx</span></span>
2. <span data-ttu-id="f5217-120">Araç kutusunda AJAX uzantılar sekmesi from beneath ScriptManager denetimi seçin ve denetimin sayfaya sürükleyin.</span><span class="sxs-lookup"><span data-stu-id="f5217-120">Select the ScriptManager control from beneath the AJAX Extensions tab in the toolbox and drag the control onto the page.</span></span>
3. <span data-ttu-id="f5217-121">Araç kutusunda düzenleyici denetimi from beneath AJAX Denetim Araç Seti sekmesini seçin ve denetimin sayfaya sürükleyin (bkz: Şekil 1).</span><span class="sxs-lookup"><span data-stu-id="f5217-121">Select the Editor control from beneath the AJAX Control Toolkit tab in the toolbox and drag the control onto the page (see Figure 1).</span></span> <span data-ttu-id="f5217-122">Tasarımcı Şekil 2'gibi görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="f5217-122">The Designer should look like Figure 2.</span></span>
4. <span data-ttu-id="f5217-123">Menü seçeneğini belirleyerek web sitesini çalıştırmak **hata ayıklama, hata ayıklamayı Başlat** veya F5 tuşuna basmak.</span><span class="sxs-lookup"><span data-stu-id="f5217-123">Run the web site by selecting the menu option **Debug, Start Debugging** or hitting the F5 key.</span></span>
5. <span data-ttu-id="f5217-124">Şekil 3'te sayfasını görmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f5217-124">You should see the page in Figure 3.</span></span>


<span data-ttu-id="f5217-125">[![HTML düzenleyicisi denetim seçme](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image1.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="f5217-125">[![Selecting the HTML Editor control](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image1.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image1.png)</span></span>

<span data-ttu-id="f5217-126">**Şekil 01**: HTML düzenleyicisi denetim seçme ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="f5217-126">**Figure 01**: Selecting the HTML Editor control([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image2.png))</span></span>


<span data-ttu-id="f5217-127">[![ScriptManager ve düzenleme denetimi ile Visual Studio Tasarımcısı](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image2.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image3.png)</span><span class="sxs-lookup"><span data-stu-id="f5217-127">[![Visual Studio Designer with ScriptManager and Edit control](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image2.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image3.png)</span></span>

<span data-ttu-id="f5217-128">**Şekil 02**: ScriptManager ve düzenleme denetimi ile Visual Studio Tasarımcısı ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image4.png))</span><span class="sxs-lookup"><span data-stu-id="f5217-128">**Figure 02**: Visual Studio Designer with ScriptManager and Edit control([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image4.png))</span></span>


<span data-ttu-id="f5217-129">[![DisplayEditor.aspx sayfası](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image3.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image5.png)</span><span class="sxs-lookup"><span data-stu-id="f5217-129">[![The DisplayEditor.aspx page](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image3.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image5.png)</span></span>

<span data-ttu-id="f5217-130">**Şekil 03**: DisplayEditor.aspx sayfa ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image6.png))</span><span class="sxs-lookup"><span data-stu-id="f5217-130">**Figure 03**: The DisplayEditor.aspx page([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image6.png))</span></span>


## <a name="using-ajax-control-toolkit-control-extenders"></a><span data-ttu-id="f5217-131">AJAX Denetim Araç Seti denetim Extender'larının kullanma</span><span class="sxs-lookup"><span data-stu-id="f5217-131">Using AJAX Control Toolkit Control Extenders</span></span>

<span data-ttu-id="f5217-132">AJAX Denetim Araç Seti ayrıca Denetim Extender'larının içerir.</span><span class="sxs-lookup"><span data-stu-id="f5217-132">The AJAX Control Toolkit also contains control extenders.</span></span> <span data-ttu-id="f5217-133">Adından da anlaşılacağı gibi denetim genişletici varolan bir denetim işlevselliğini genişletir.</span><span class="sxs-lookup"><span data-stu-id="f5217-133">As its name suggests, a control extender extends the functionality of an existing control.</span></span> <span data-ttu-id="f5217-134">Örneğin, standart ASP.NET düğme denetimi ConfirmButton denetim genişletici genişletir.</span><span class="sxs-lookup"><span data-stu-id="f5217-134">For example, the ConfirmButton control extender extends the standard ASP.NET Button control.</span></span> <span data-ttu-id="f5217-135">Genişletici düğme denetim s davranışı değiştirir, böylece düğmesini tıklattığınızda bir onay iletişim kutusu görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f5217-135">The extender changes the Button control�s behavior so that the Button displays a confirmation dialog when you click it.</span></span>

<span data-ttu-id="f5217-136">Bir AJAX Denetim Araç Seti denetimi gibi bir denetim genişletici bir ScriptManager denetimi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f5217-136">A control extender, just like an AJAX Control Toolkit control, requires a ScriptManager control.</span></span> <span data-ttu-id="f5217-137">Denetim Extender'larının sayfasında kullanmaya başlamadan önce bir sayfaya bir ScriptManager denetimi eklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f5217-137">You must add a ScriptManager control to a page before you start using control extenders in the page.</span></span>

<span data-ttu-id="f5217-138">ConfirmButton denetim genişletici kullanmak için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="f5217-138">Follow these steps to use the ConfirmButton control extender:</span></span>

1. <span data-ttu-id="f5217-139">ShowConfirmButton.aspx adlı yeni bir ASP.NET sayfası oluşturun</span><span class="sxs-lookup"><span data-stu-id="f5217-139">Create a new ASP.NET page named ShowConfirmButton.aspx</span></span>
2. <span data-ttu-id="f5217-140">Bir ScriptManager denetimi AJAX uzantılar sekmesi from beneath sayfaya denetimi sürükleyerek sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f5217-140">Add a ScriptManager control to the page by dragging the control onto the page from beneath the AJAX Extensions tab.</span></span>
3. <span data-ttu-id="f5217-141">Standart bir düğme denetimi, araç çubuğundaki Tasarımcı yüzeyine Standart sekmesini from beneath düğmesi sürükleyerek sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f5217-141">Add a standard Button control to the page by dragging the Button from beneath the Standard tab in the toolbox onto the Designer surface.</span></span>
4. <span data-ttu-id="f5217-142">Tıklatın **eklemek genişletici** görev seçeneği (Şekil 4'e bakın).</span><span class="sxs-lookup"><span data-stu-id="f5217-142">Click the **Add Extender** task option (see Figure 4).</span></span>
5. <span data-ttu-id="f5217-143">Genişletici Seç iletişim kutusunda ConfirmButtonExtender seçin (bkz. Şekil 5) ve Tamam düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f5217-143">In the Choose Extender dialog, select ConfirmButtonExtender (see Figure 5) and click the OK button.</span></span>
6. <span data-ttu-id="f5217-144">Düğme denetimi Tasarımcısı'nda seçin ve Button1 Genişleticileri genişletin\_ConfirmButtonExtender düğüm Özellikleri penceresinde (bkz. Şekil 6).</span><span class="sxs-lookup"><span data-stu-id="f5217-144">Select the Button control in the Designer and expand the Extenders, Button1\_ConfirmButtonExtender node in the Properties window (see Figure 6).</span></span> <span data-ttu-id="f5217-145">Değer atamak *gerçekten?* ConfirmText özelliğine.</span><span class="sxs-lookup"><span data-stu-id="f5217-145">Assign the value *�Really?�* to the ConfirmText property.</span></span>
7. <span data-ttu-id="f5217-146">Menü seçeneğini seçerek sayfayı çalıştırın **hata ayıklama, hata ayıklamayı Başlat** veya F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="f5217-146">Run the page by selecting the menu option **Debug, Start Debugging** or hit the F5 key.</span></span>


<span data-ttu-id="f5217-147">[![Genişletici eklemek görev seçeneği](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image4.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image7.png)</span><span class="sxs-lookup"><span data-stu-id="f5217-147">[![The Add Extender task option](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image4.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image7.png)</span></span>

<span data-ttu-id="f5217-148">**Şekil 04**: ekleme genişletici görev seçeneği ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image8.png))</span><span class="sxs-lookup"><span data-stu-id="f5217-148">**Figure 04**: The Add Extender task option([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image8.png))</span></span>


<span data-ttu-id="f5217-149">[![ConfirmButton denetim genişletici seçme](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image5.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image9.png)</span><span class="sxs-lookup"><span data-stu-id="f5217-149">[![Selecting the ConfirmButton control extender](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image5.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image9.png)</span></span>

<span data-ttu-id="f5217-150">**Şekil 05**: ConfirmButton denetim genişletici seçme ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image10.png))</span><span class="sxs-lookup"><span data-stu-id="f5217-150">**Figure 05**: Selecting the ConfirmButton control extender([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image10.png))</span></span>


<span data-ttu-id="f5217-151">[![ConfirmButton özelliği ayarlama](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image6.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image11.png)</span><span class="sxs-lookup"><span data-stu-id="f5217-151">[![Setting a ConfirmButton property](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image6.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image11.png)</span></span>

<span data-ttu-id="f5217-152">**Şekil 06**: ConfirmButton özelliği ayarı ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image12.png))</span><span class="sxs-lookup"><span data-stu-id="f5217-152">**Figure 06**: Setting a ConfirmButton property([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image12.png))</span></span>


<span data-ttu-id="f5217-153">Sayfa açıldığında bir düğme görmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f5217-153">When the page opens, you should see a button.</span></span> <span data-ttu-id="f5217-154">Düğmeye tıkladığınızda, Şekil 7'de onay iletişim kutusu alın.</span><span class="sxs-lookup"><span data-stu-id="f5217-154">When you click the button, you get the confirmation dialog in Figure 7.</span></span>


<span data-ttu-id="f5217-155">[![Onay iletişim kutusu görüntüleme](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image7.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image13.png)</span><span class="sxs-lookup"><span data-stu-id="f5217-155">[![Displaying the confirmation dialog](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image7.jpg)](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image13.png)</span></span>

<span data-ttu-id="f5217-156">**Şekil 07**: onay iletişim kutusu görüntüleme ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image14.png))</span><span class="sxs-lookup"><span data-stu-id="f5217-156">**Figure 07**: Displaying the confirmation dialog([Click to view full-size image](using-ajax-control-toolkit-controls-and-control-extenders-cs/_static/image14.png))</span></span>


<span data-ttu-id="f5217-157">Normalde bir denetim genişletici bir sayfaya sürüklediğiniz değil, dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f5217-157">Notice that you normally do not drag a control extender onto a page.</span></span> <span data-ttu-id="f5217-158">Bunun yerine, kullandığınız **eklemek genişletici** görev genişletici bir sayfasına eklemiş bir denetim eklemek için seçeneği.</span><span class="sxs-lookup"><span data-stu-id="f5217-158">Instead, you use the **Add Extender** task option to add an extender to a control that you have already added to a page.</span></span> <span data-ttu-id="f5217-159">Ayrıca, Denetim genişletilen denetimi için özellik sayfası açarak genişletici özelliklerini ayarlamak dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f5217-159">Notice, furthermore, that you set control extender properties by opening the property sheet for the control being extended.</span></span>

<span data-ttu-id="f5217-160">Tek bir ASP.NET denetim birden çok denetim Extender'larının tarafından genişletilebilir.</span><span class="sxs-lookup"><span data-stu-id="f5217-160">A single ASP.NET control can be extended by multiple control extenders.</span></span> <span data-ttu-id="f5217-161">Özellik sayfasını genişletilen denetimi için tüm denetim Extender'larının denetimle ilişkili listeler.</span><span class="sxs-lookup"><span data-stu-id="f5217-161">The property sheet for the control being extended will list all of the control extenders associated with the control.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="f5217-162">[Önceki](get-started-with-the-ajax-control-toolkit-cs.md)
[sonraki](creating-a-custom-ajax-control-toolkit-control-extender-cs.md)</span><span class="sxs-lookup"><span data-stu-id="f5217-162">[Previous](get-started-with-the-ajax-control-toolkit-cs.md)
[Next](creating-a-custom-ajax-control-toolkit-control-extender-cs.md)</span></span>
