---
uid: web-pages/overview/ui-layouts-and-themes/twitter-helper
title: "Twitter Yardımcısı ASP.NET Web sayfaları ile | Microsoft Docs"
author: tfitzmac
description: "Bu konu ve uygulama Twitter Yardımcısı, WebMatrix 3 projenize eklemek nasıl gösterir. Twitter Yardımcısı kodunu içerir ve yardımcı çağrısının nasıl gerçekleştireceğini..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/07/2014
ms.topic: article
ms.assetid: c1a1244e-b9c8-42e6-a00b-8456a4ec027c
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/twitter-helper
msc.type: authoredcontent
ms.openlocfilehash: 07d9c4d485c42b78a42c54c9740af5f67cb44763
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="twitter-helper-with-aspnet-web-pages"></a><span data-ttu-id="78def-104">ASP.NET Web sayfaları ile twitter Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="78def-104">Twitter Helper with ASP.NET Web Pages</span></span>
====================
<span data-ttu-id="78def-105">tarafından [zel FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="78def-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="78def-106">Bu konu ve uygulama Twitter Yardımcısı, WebMatrix 3 projenize eklemek nasıl gösterir.</span><span class="sxs-lookup"><span data-stu-id="78def-106">This topic and application show how to add a Twitter Helper to your WebMatrix 3 project.</span></span> <span data-ttu-id="78def-107">Twitter Yardımcısı kodunu içerir ve nasıl yardımcı yöntemler çağrılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="78def-107">It contains the Twitter Helper code and shows how to call the helper methods.</span></span>
> 
> <span data-ttu-id="78def-108">Twitter.cshtml dosyası için bu kodu tarafından geliştirilmiştir **Tian Pan** Microsoft.</span><span class="sxs-lookup"><span data-stu-id="78def-108">This code for the Twitter.cshtml file was developed by **Tian Pan** of Microsoft.</span></span>
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="78def-109">Öğreticide kullanılan yazılım sürümleri</span><span class="sxs-lookup"><span data-stu-id="78def-109">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="78def-110">ASP.NET Web sayfaları (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="78def-110">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="78def-111">Bu öğreticide, ASP.NET Web Pages 2 ile de çalışır.</span><span class="sxs-lookup"><span data-stu-id="78def-111">This tutorial also works with ASP.NET Web Pages 2.</span></span>


## <a name="introduction"></a><span data-ttu-id="78def-112">Giriş</span><span class="sxs-lookup"><span data-stu-id="78def-112">Introduction</span></span>

<span data-ttu-id="78def-113">Bu konuda, bir Twitter Yardımcısı uygulamanıza ekleyin ve yardımcı yöntemleri çağırmak için Razor sözdizimini kullanan gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="78def-113">This topic demonstrates how to add a Twitter Helper to your application and use Razor syntax to call the helper methods.</span></span> <span data-ttu-id="78def-114">Twitter Yardımcısı Twitter düğmeleri ve pencere öğeleri, uygulamanızda eklemenizi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="78def-114">The Twitter Helper makes it easy to incorporate Twitter buttons and widgets in your application.</span></span> <span data-ttu-id="78def-115">Bir Twitter pencere, bir kullanıcının zaman çizelgesi veya bir hashtag için arama sonuçlarını gibi kullanmak için önce oluşturmanız gerekir [Twitter'da pencere öğesi](https://twitter.com/settings/widgets).</span><span class="sxs-lookup"><span data-stu-id="78def-115">To use a Twitter widget, such as a user's timeline or the search results for a hashtag, you must first create the [widget on Twitter](https://twitter.com/settings/widgets).</span></span> <span data-ttu-id="78def-116">Pencere öğesi oluşturduktan sonra bir pencere öğesi kimliği alırsınız. Bu pencere öğesi kimliği pencere öğesi Göster yardımcı yöntemler çağırırken parametre olarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="78def-116">After creating your widget, you will receive a widget id. You pass this widget id as a parameter when calling the helper methods that show widget.</span></span>

<span data-ttu-id="78def-117">Bu konu, Twitter API'si 1.1 sürümünü yazıldı.</span><span class="sxs-lookup"><span data-stu-id="78def-117">This topic was written for version 1.1 of the Twitter API.</span></span> <span data-ttu-id="78def-118">Projenize doğrudan Twitter Yardımcısı kodu ekleyerek, Twitter API'si değişirse yardımcı kodu güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="78def-118">By directly adding the Twitter Helper code to your project, you can update the helper code if the Twitter API changes.</span></span>

<span data-ttu-id="78def-119">WebMatrix yükleme hakkında daha fazla bilgi için bkz: [Tanıtımı ASP.NET Web sayfaları Başlarken 2 -](../getting-started/introducing-aspnet-web-pages-2/getting-started.md).</span><span class="sxs-lookup"><span data-stu-id="78def-119">For information about installing WebMatrix, see [Introducing ASP.NET Web Pages 2 - Getting Started](../getting-started/introducing-aspnet-web-pages-2/getting-started.md).</span></span>

## <a name="add-twitter-helper-to-your-project"></a><span data-ttu-id="78def-120">Twitter Yardımcısı projenize ekleyin</span><span class="sxs-lookup"><span data-stu-id="78def-120">Add Twitter Helper to your project</span></span>

<span data-ttu-id="78def-121">Twitter Yardımcısı eklemek için ilk olarak, adında bir klasör ekleyin **uygulama\_kod** projenize.</span><span class="sxs-lookup"><span data-stu-id="78def-121">To add the Twitter Helper, first, add a folder named **App\_Code** to your project.</span></span> <span data-ttu-id="78def-122">Ardından, adlı bir dosya oluşturun **Twitter.cshtml**.</span><span class="sxs-lookup"><span data-stu-id="78def-122">Then, create a file named **Twitter.cshtml**.</span></span>

![App_Code klasörü](twitter-helper/_static/image1.png)

<span data-ttu-id="78def-124">Twitter.cshtml varsayılan kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="78def-124">Replace the default code in Twitter.cshtml with the following code.</span></span>

[!code-cshtml[Main](twitter-helper/samples/sample1.cshtml)]

## <a name="call-twitter-methods-from-your-web-pages"></a><span data-ttu-id="78def-125">Web sayfalarından twitter yöntemlerini çağırın</span><span class="sxs-lookup"><span data-stu-id="78def-125">Call Twitter methods from your web pages</span></span>

<span data-ttu-id="78def-126">Aşağıdaki örnek projenizde sayfasından Twitter Yardımcısı yöntemleri kullanmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="78def-126">The following example shows how to use the Twitter Helper methods from a page in your project.</span></span> <span data-ttu-id="78def-127">Projenizde, gereksinimlerinize uygun değerlerle parametre değerlerini değiştirmeniz isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="78def-127">In your project, you will want to replace the parameter values with values that are relevant to your needs.</span></span> <span data-ttu-id="78def-128">Yöntemleri nasıl çalıştığını keşfetmek için sağlanan pencere kimlikleri kullanabilirsiniz, ancak kendi pencere öğelerinizi projeniz için oluşturmak istersiniz.</span><span class="sxs-lookup"><span data-stu-id="78def-128">You can use the provided widget ids to explore how the methods work, but you will want to generate your own widgets for your project.</span></span>

<span data-ttu-id="78def-129">Aşağıda gösterilen parametreler hepsi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="78def-129">Not all of the parameters shown below are required.</span></span> <span data-ttu-id="78def-130">İsteğe bağlı parametreler düğmesini veya pencere öğesi nasıl görüntüleneceğini özelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="78def-130">The optional parameters are used to customize how the button or widget is displayed.</span></span> <span data-ttu-id="78def-131">Örneğin, izleyin düğmesi izlemek için kullanıcı adı yalnızca gerektiriyor, ancak örnek followers sayısını içerir ve nasıl düğmesi ve dil boyutunu belirtmek nasıl gösterir.</span><span class="sxs-lookup"><span data-stu-id="78def-131">For example, the Follow Button only requires the user name to follow, but the example shows how to include the number of followers, and how specify the size of the button and the language.</span></span>

[!code-html[Main](twitter-helper/samples/sample2.html)]

## <a name="see-the-results"></a><span data-ttu-id="78def-132">Sonuçları görüntüleyin</span><span class="sxs-lookup"><span data-stu-id="78def-132">See the results</span></span>

<span data-ttu-id="78def-133">Yukarıdaki kod aşağıdaki düğmeler ve pencere öğeleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="78def-133">The above code produces the following buttons and widgets.</span></span> <span data-ttu-id="78def-134">Bu düğmelerin ve pencere öğeleri tam işlevsel, ekran görüntüleri değil.</span><span class="sxs-lookup"><span data-stu-id="78def-134">These buttons and widgets are fully-functional, not screenshots.</span></span> <span data-ttu-id="78def-135">Dil parametresi olarak ayarlanmış olduğu için İspanyolca izleyin düğmesi görüntülenir **es**.</span><span class="sxs-lookup"><span data-stu-id="78def-135">The Follow Button is displayed in Spanish because the language parameter was set to **es**.</span></span>

### <a name="follow-button"></a><span data-ttu-id="78def-136">Düğme izleyin</span><span class="sxs-lookup"><span data-stu-id="78def-136">Follow Button</span></span>

<span data-ttu-id="78def-137">[İzleyin @aspnet)](https://twitter.com/aspnet)<script>! işlevi (d, s, kimliği) {var js, fjs d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) =? 'http': 'https'; varsa (! d.getElementById(id)) {js d.createElement(s); = js.id = kimliği; js.src = p + ': / / platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore (js, fjs);}} (belge, 'komut dosyası', 'twitter-wjs');</script></span><span class="sxs-lookup"><span data-stu-id="78def-137">[Follow @aspnet)](https://twitter.com/aspnet)<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script></span></span>

### <a name="tweet-button"></a><span data-ttu-id="78def-138">Tweet düğmesi</span><span class="sxs-lookup"><span data-stu-id="78def-138">Tweet Button</span></span>

<span data-ttu-id="78def-139">[Tweet](https://twitter.com/share)<script>! işlevi (d, s, kimliği) {var js, fjs d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) =? 'http': 'https'; varsa (! d.getElementById(id)) {js d.createElement(s); = js.id = kimliği; js.src = p + ': / / platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore (js, fjs);}} (belge, 'komut dosyası', 'twitter-wjs');</script></span><span class="sxs-lookup"><span data-stu-id="78def-139">[Tweet](https://twitter.com/share)<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script></span></span>

### <a name="user-timeline-profile"></a><span data-ttu-id="78def-140">Kullanıcı zaman çizelgesi (Profil)</span><span class="sxs-lookup"><span data-stu-id="78def-140">User Timeline (Profile)</span></span>

<span data-ttu-id="78def-141">[Tarafından tweet'leri @aspnet ](https://twitter.com/aspnet) <script>! işlevi (d, s, kimliği) {var js, fjs d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) =? 'http': 'https'; varsa (! d.getElementById(id)) {js d.createElement(s); = js.id = kimliği; js.src = p + ": / / platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore (js, fjs);}} (belge, "komut dosyası", "twitter-wjs");</script></span><span class="sxs-lookup"><span data-stu-id="78def-141">[Tweets by @aspnet](https://twitter.com/aspnet)<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script></span></span>

### <a name="favorites"></a><span data-ttu-id="78def-142">Sık Kullanılanlar</span><span class="sxs-lookup"><span data-stu-id="78def-142">Favorites</span></span>

<span data-ttu-id="78def-143">[Sık kullanılan Tweet'leri tarafından @Microsoft ](https://twitter.com/Microsoft/favorites) <script>! işlevi (d, s, kimliği) {var js, fjs d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) =? 'http': 'https'; varsa (! d.getElementById(id)) {js d.createElement(s); = js.id = kimliği; js.src = p + ": / / platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore (js, fjs);}} (belge, "komut dosyası", "twitter-wjs");</script></span><span class="sxs-lookup"><span data-stu-id="78def-143">[Favorite Tweets by @Microsoft](https://twitter.com/Microsoft/favorites)<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script></span></span>

### <a name="list"></a><span data-ttu-id="78def-144">List</span><span class="sxs-lookup"><span data-stu-id="78def-144">List</span></span>

<span data-ttu-id="78def-145">[Gelen tweet'leri @Microsoft/MS \_tüketici\_bantları](https://twitter.com/microsoft/ms-consumer-brands/)<script>! işlevi (d, s, kimliği) {var js, fjs d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) =? 'http': 'https'; varsa (! d.getElementById(id)) {js d.createElement(s); = js.id = kimliği; js.src = p + ": / / platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore (js, fjs);}} (belge, "komut dosyası", "twitter-wjs");</script></span><span class="sxs-lookup"><span data-stu-id="78def-145">[Tweets from @Microsoft/MS\_Consumer\_Bands](https://twitter.com/microsoft/ms-consumer-brands/)<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script></span></span>

### <a name="search"></a><span data-ttu-id="78def-146">Ara</span><span class="sxs-lookup"><span data-stu-id="78def-146">Search</span></span>

<span data-ttu-id="78def-147">[İlgili tweet'leri &quot;#asp.net&quot;](https://twitter.com/search?q=%23asp.net)<script>! işlevi (d, s, kimliği) {var js, fjs d.getElementsByTagName(s) [0], p = /^http:/.test(d.location) =? 'http': 'https'; varsa (! d.getElementById(id)) {js d.createElement(s); = js.id = kimliği; js.src = p + ": / / platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore (js, fjs);}} (belge, "komut dosyası", "twitter-wjs");</script></span><span class="sxs-lookup"><span data-stu-id="78def-147">[Tweets about &quot;#asp.net&quot;](https://twitter.com/search?q=%23asp.net)<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script></span></span>
