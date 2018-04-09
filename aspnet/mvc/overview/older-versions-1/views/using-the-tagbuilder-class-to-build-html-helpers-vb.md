---
uid: mvc/overview/older-versions-1/views/using-the-tagbuilder-class-to-build-html-helpers-vb
title: HTML Yardımcıları (VB) oluşturmak için TagBuilder sınıfını kullanma | Microsoft Docs
author: StephenWalther
description: Stephen Walther yararlı yardımcı program sınıfı TagBuilder sınıfı adlı ASP.NET MVC çerçevesi arasında tanıtır. TagBuilder sınıfına kolayca kullanabilirsiniz...
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/02/2009
ms.topic: article
ms.assetid: ec26f264-d0ea-4031-9943-825505a3ac4b
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/views/using-the-tagbuilder-class-to-build-html-helpers-vb
msc.type: authoredcontent
ms.openlocfilehash: 2b72e08dff646f66252f210543230186cab6e641
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="using-the-tagbuilder-class-to-build-html-helpers-vb"></a><span data-ttu-id="d00eb-104">HTML Yardımcıları (VB) oluşturmak için TagBuilder sınıfını kullanma</span><span class="sxs-lookup"><span data-stu-id="d00eb-104">Using the TagBuilder Class to Build HTML Helpers (VB)</span></span>
====================
<span data-ttu-id="d00eb-105">tarafından [Stephen Walther](https://github.com/StephenWalther)</span><span class="sxs-lookup"><span data-stu-id="d00eb-105">by [Stephen Walther](https://github.com/StephenWalther)</span></span>

> <span data-ttu-id="d00eb-106">Stephen Walther yararlı yardımcı program sınıfı TagBuilder sınıfı adlı ASP.NET MVC çerçevesi arasında tanıtır.</span><span class="sxs-lookup"><span data-stu-id="d00eb-106">Stephen Walther introduces you to a useful utility class in the ASP.NET MVC framework named the TagBuilder class.</span></span> <span data-ttu-id="d00eb-107">TagBuilder sınıfı kolayca HTML etiketleri oluşturmak için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d00eb-107">You can use the TagBuilder class to easily build HTML tags.</span></span>


<span data-ttu-id="d00eb-108">ASP.NET MVC çerçevesi HTML Yardımcıları oluştururken kullanabileceğiniz TagBuilder sınıfı adlı bir yararlı yardımcı program sınıfı içerir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-108">The ASP.NET MVC framework includes a useful utility class named the TagBuilder class that you can use when building HTML helpers.</span></span> <span data-ttu-id="d00eb-109">Sınıfın adını da anlaşılacağı gibi TagBuilder sınıfı, HTML etiketleri kolayca oluşturmanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d00eb-109">The TagBuilder class, as the name of the class suggests, enables you to easily build HTML tags.</span></span> <span data-ttu-id="d00eb-110">Kısa Bu öğreticide, TagBuilder sınıfı bir bakış sağlanır ve basit bir HTML Yardımcısı oluşturmaya HTML işleyen olduğunda bu sınıfının nasıl kullanılacağını öğrenin &lt;img&gt; etiketler.</span><span class="sxs-lookup"><span data-stu-id="d00eb-110">In this brief tutorial, you are provided with an overview of the TagBuilder class and you learn how to use this class when building a simple HTML helper that renders HTML &lt;img&gt; tags.</span></span>

## <a name="overview-of-the-tagbuilder-class"></a><span data-ttu-id="d00eb-111">TagBuilder sınıfı genel bakış</span><span class="sxs-lookup"><span data-stu-id="d00eb-111">Overview of the TagBuilder Class</span></span>

<span data-ttu-id="d00eb-112">TagBuilder sınıfı System.Web.Mvc ad alanında yer alır.</span><span class="sxs-lookup"><span data-stu-id="d00eb-112">The TagBuilder class is contained in the System.Web.Mvc namespace.</span></span> <span data-ttu-id="d00eb-113">Beş yöntemi vardır:</span><span class="sxs-lookup"><span data-stu-id="d00eb-113">It has five methods:</span></span>

- <span data-ttu-id="d00eb-114">AddCssClass() – yeni bir eklemenize olanak sağlayan *class = ""* özniteliği için bir etiket.</span><span class="sxs-lookup"><span data-stu-id="d00eb-114">AddCssClass() – Enables you to add a new *class=""* attribute to a tag.</span></span>
- <span data-ttu-id="d00eb-115">GenerateId() – bir etiketi için ID özniteliği eklemenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d00eb-115">GenerateId() – Enables you to add an id attribute to a tag.</span></span> <span data-ttu-id="d00eb-116">Bu yöntem otomatik olarak Kimliği'nde noktaları değiştiren (varsayılan olarak, alt çizgi tarafından nokta değiştirilir)</span><span class="sxs-lookup"><span data-stu-id="d00eb-116">This method automatically replaces periods in the id (by default, periods are replaced by underscores)</span></span>
- <span data-ttu-id="d00eb-117">MergeAttribute() – bir etiket için öznitelikler eklemenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d00eb-117">MergeAttribute() – Enables you to add attributes to a tag.</span></span> <span data-ttu-id="d00eb-118">Bu yöntemin birden çok aşırı vardır.</span><span class="sxs-lookup"><span data-stu-id="d00eb-118">There are multiple overloads of this method.</span></span>
- <span data-ttu-id="d00eb-119">SetInnerText() – etiketinin iç metni ayarlamanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d00eb-119">SetInnerText() – Enables you to set the inner text of the tag.</span></span> <span data-ttu-id="d00eb-120">HTML kodlama otomatik olarak iç metindir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-120">The inner text is HTML encode automatically.</span></span>
- <span data-ttu-id="d00eb-121">ToString() – etiket oluşturmak etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-121">ToString() – Enables you to render the tag.</span></span> <span data-ttu-id="d00eb-122">Normal bir etiketi, bir başlangıç etiketi, bir bitiş etiketi veya kendi kendine kapanan etiketi oluşturmak isteyip istemediğinizi belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d00eb-122">You can specify whether you want to create a normal tag, a start tag, an end tag, or a self-closing tag.</span></span>
  

<span data-ttu-id="d00eb-123">TagBuilder sınıfı dört önemli özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d00eb-123">The TagBuilder class has four important properties:</span></span>

- <span data-ttu-id="d00eb-124">Öznitelikleri – tüm etiket özniteliklerini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d00eb-124">Attributes – Represents all of the attributes of the tag.</span></span>
- <span data-ttu-id="d00eb-125">IdAttributeDotReplacement – nokta (varsayılan değer altçizgidir) değiştirmek için GenerateId() yöntemi tarafından kullanılan karakteri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d00eb-125">IdAttributeDotReplacement – Represents the character used by the GenerateId() method to replace periods (the default is an underscore).</span></span>
- <span data-ttu-id="d00eb-126">InnerHtml – etiketinin iç içeriğini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d00eb-126">InnerHTML – Represents the inner contents of the tag.</span></span> <span data-ttu-id="d00eb-127">Bu özellik bir dize atama *yok* HTML kodlama dizesi.</span><span class="sxs-lookup"><span data-stu-id="d00eb-127">Assigning a string to this property *does not* HTML encode the string.</span></span>
- <span data-ttu-id="d00eb-128">TagName – etiketin adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d00eb-128">TagName – Represents the name of the tag.</span></span>

<span data-ttu-id="d00eb-129">Bu yöntemlere ve özelliklere tüm temel yöntemleri ve bir HTML etiketi oluşturmak için gereken özelliklerini verin.</span><span class="sxs-lookup"><span data-stu-id="d00eb-129">These methods and properties give you all of the basic methods and properties that you need to build up an HTML tag.</span></span> <span data-ttu-id="d00eb-130">Gerçekten TagBuilder sınıfı kullanmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d00eb-130">You don't really need to use the TagBuilder class.</span></span> <span data-ttu-id="d00eb-131">Bunun yerine bir StringBuilder sınıfını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d00eb-131">You could use a StringBuilder class instead.</span></span> <span data-ttu-id="d00eb-132">Ancak, TagBuilder sınıfı yaşamınızı biraz daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-132">However, the TagBuilder class makes your life a little easier.</span></span>

## <a name="creating-an-image-html-helper"></a><span data-ttu-id="d00eb-133">Bir görüntü HTML Yardımcısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="d00eb-133">Creating an Image HTML Helper</span></span>

<span data-ttu-id="d00eb-134">TagBuilder sınıfının bir örneğini oluştururken TagBuilder oluşturucuya oluşturmak istediğiniz etiketin adını geçirin.</span><span class="sxs-lookup"><span data-stu-id="d00eb-134">When you create an instance of the TagBuilder class, you pass the name of the tag that you want to build to the TagBuilder constructor.</span></span> <span data-ttu-id="d00eb-135">Ardından, etiket özniteliklerini değiştirmek için AddCssClass ve MergeAttribute() yöntemleri gibi yöntemleri çağırabilir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-135">Next, you can call methods like the AddCssClass and MergeAttribute() methods to modify the attributes of the tag.</span></span> <span data-ttu-id="d00eb-136">Son olarak, etiket oluşturmak için ToString() yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="d00eb-136">Finally, you call the ToString() method to render the tag.</span></span>

<span data-ttu-id="d00eb-137">Örneğin, 1 listeleyen bir görüntü HTML Yardımcısı içerir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-137">For example, Listing 1 contains an Image HTML helper.</span></span> <span data-ttu-id="d00eb-138">Image helper dahili bir HTML temsil eden bir TagBuilder ile uygulanan &lt;img&gt; etiketi.</span><span class="sxs-lookup"><span data-stu-id="d00eb-138">The Image helper is implemented internally with a TagBuilder that represents an HTML &lt;img&gt; tag.</span></span>

<span data-ttu-id="d00eb-139">**1 – Helpers\ImageHelper.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="d00eb-139">**Listing 1 – Helpers\ImageHelper.vb**</span></span>

[!code-vb[Main](using-the-tagbuilder-class-to-build-html-helpers-vb/samples/sample1.vb)]

<span data-ttu-id="d00eb-140">Listeleme 1 modülünde Image() adlı iki aşırı yüklenmiş yöntemler içerir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-140">The module in Listing 1 contains two overloaded methods named Image().</span></span> <span data-ttu-id="d00eb-141">Image() yöntemini çağırdığınızda, veya bir dizi HTML özniteliklerini temsil eden bir nesne geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d00eb-141">When you call the Image() method, you can pass an object which represents a set of HTML attributes or not.</span></span>

<span data-ttu-id="d00eb-142">TagBuilder.MergeAttribute() yöntemi TagBuilder src özniteliğini gibi tek tek öznitelikler eklemek için nasıl kullanıldığını dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="d00eb-142">Notice how the TagBuilder.MergeAttribute() method is used to add individual attributes such as the src attribute to the TagBuilder.</span></span> <span data-ttu-id="d00eb-143">Ayrıca, fark nasıl TagBuilder.MergeAttributes() yöntemi TagBuilder özniteliklerin bir koleksiyonu eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d00eb-143">Notice, furthermore, how the TagBuilder.MergeAttributes() method is used to add a collection of attributes to the TagBuilder.</span></span> <span data-ttu-id="d00eb-144">Bir sözlük MergeAttributes() yöntemi kabul&lt;dize, nesne&gt; parametresi.</span><span class="sxs-lookup"><span data-stu-id="d00eb-144">The MergeAttributes() method accepts a Dictionary&lt;string,object&gt; parameter.</span></span> <span data-ttu-id="d00eb-145">RouteValueDictionary sınıf öznitelikleri koleksiyon bir sözlük olarak temsil eden nesneyi dönüştürmek için kullanılan&lt;dize, nesne&gt;.</span><span class="sxs-lookup"><span data-stu-id="d00eb-145">The RouteValueDictionary class is used to convert the Object representing the collection of attributes into a Dictionary&lt;string,object&gt;.</span></span>

<span data-ttu-id="d00eb-146">Image helper oluşturduktan sonra ASP.NET MVC görünümlerinizde gibi diğer standart HTML Yardımcıları hiçbirini yardımcıyı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d00eb-146">After you create the Image helper, you can use the helper in your ASP.NET MVC views just like any of the other standard HTML helpers.</span></span> <span data-ttu-id="d00eb-147">Xbox aynı görüntüsü iki kez görüntülemek için Image helper listeleme 2 görünümünde kullanır (bkz: Şekil 1).</span><span class="sxs-lookup"><span data-stu-id="d00eb-147">The view in Listing 2 uses the Image helper to display the same image of an Xbox twice (see Figure 1).</span></span> <span data-ttu-id="d00eb-148">Image() Yardımcısı, hem ile hem de bir HTML öznitelik koleksiyonu olmadan adı verilir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-148">The Image() helper is called both with and without an HTML attribute collection.</span></span>

<span data-ttu-id="d00eb-149">**Listing 2 – Home\Index.aspx**</span><span class="sxs-lookup"><span data-stu-id="d00eb-149">**Listing 2 – Home\Index.aspx**</span></span>

[!code-aspx[Main](using-the-tagbuilder-class-to-build-html-helpers-vb/samples/sample2.aspx)]


<span data-ttu-id="d00eb-150">[![Yeni Proje iletişim kutusu](using-the-tagbuilder-class-to-build-html-helpers-vb/_static/image1.jpg)](using-the-tagbuilder-class-to-build-html-helpers-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="d00eb-150">[![The New Project dialog box](using-the-tagbuilder-class-to-build-html-helpers-vb/_static/image1.jpg)](using-the-tagbuilder-class-to-build-html-helpers-vb/_static/image1.png)</span></span>

<span data-ttu-id="d00eb-151">**Şekil 01**: yansıma Yardımcısını kullanarak ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-the-tagbuilder-class-to-build-html-helpers-vb/_static/image2.png))</span><span class="sxs-lookup"><span data-stu-id="d00eb-151">**Figure 01**: Using the Image helper([Click to view full-size image](using-the-tagbuilder-class-to-build-html-helpers-vb/_static/image2.png))</span></span>


<span data-ttu-id="d00eb-152">Bildirim Index.aspx görünüm üstündeki Image helper ilişkili ad alanı içeri aktarmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="d00eb-152">Notice that you must import the namespace associated with the Image helper at the top of the Index.aspx view.</span></span> <span data-ttu-id="d00eb-153">Yardımcı aşağıdaki yönergesi alınır:</span><span class="sxs-lookup"><span data-stu-id="d00eb-153">The helper is imported with the following directive:</span></span>

[!code-aspx[Main](using-the-tagbuilder-class-to-build-html-helpers-vb/samples/sample3.aspx)]

<span data-ttu-id="d00eb-154">Bir Visual Basic uygulamasında varsayılan ad alanını uygulamanın adı ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="d00eb-154">In a Visual Basic application, the default namespace is the same as the name of the application.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d00eb-155">[Önceki](creating-custom-html-helpers-vb.md)
> [sonraki](creating-page-layouts-with-view-master-pages-vb.md)</span><span class="sxs-lookup"><span data-stu-id="d00eb-155">[Previous](creating-custom-html-helpers-vb.md)
[Next](creating-page-layouts-with-view-master-pages-vb.md)</span></span>
