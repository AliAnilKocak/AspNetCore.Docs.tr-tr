---
title: "Bir ASP.NET Core uygulamada açık yeniden yönlendirme saldırılarını önleme | Microsoft Docs"
author: ardalis
description: "Açık yeniden yönlendirme saldırılarına karşı bir ASP.NET Core uygulama önlemek nasıl gösterir"
ms.author: riande
manager: wpickett
ms.date: 07/07/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/preventing-open-redirects
ms.openlocfilehash: e57ae429e9af54ade74485361ba591cb75c16752
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/19/2018
---
# <a name="preventing-open-redirect-attacks-in-an-aspnet-core-app"></a><span data-ttu-id="32c6a-103">Bir ASP.NET Core uygulamada açık yeniden yönlendirme saldırılarını önleme</span><span class="sxs-lookup"><span data-stu-id="32c6a-103">Preventing Open Redirect Attacks in an ASP.NET Core app</span></span>

<span data-ttu-id="32c6a-104">İstek sorgu dizesi veya form verileri gibi aracılığıyla belirtilen bir URL'ye yönlendiren bir web uygulaması olası kullanıcıları bir dış, kötü amaçlı URL'sine yeniden yönlendirmek için değiştirilmiş.</span><span class="sxs-lookup"><span data-stu-id="32c6a-104">A web app that redirects to a URL that is specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="32c6a-105">Bu oynama açık yeniden yönlendirme saldırısı olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="32c6a-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="32c6a-106">Uygulama mantığınızın bir belirtilen URL'ye yeniden yönlendirilen olduğunda yeniden yönlendirme URL'sini oynanmadığını doğrulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="32c6a-107">ASP.NET Core uygulamaları açık (açık olarak da bilinen yeniden yönlendirme) yeniden yönlendirme saldırılarına karşı korumaya yardımcı olmak için yerleşik bir işleve sahiptir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="32c6a-108">Açık yeniden yönlendirme saldırının nedir?</span><span class="sxs-lookup"><span data-stu-id="32c6a-108">What is an open redirect attack?</span></span>

<span data-ttu-id="32c6a-109">Kimlik doğrulaması gerektiren kaynaklara erişirken web uygulamaları sık kullanıcılar bir oturum açma sayfasına yönlendir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="32c6a-110">Yeniden yönlendirme typlically içeren bir `returnUrl` sorgu dizesi parametresi böylece bunlar başarıyla oturum açtıktan sonra kullanıcı ilk olarak istenen URL'ye yeniden döndürülebilir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-110">The redirection typlically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="32c6a-111">Kullanıcı kimlik doğrulaması yaptıktan sonra cihazın ilk olarak istedikleri URL yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-111">After the user authenticates, they are redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="32c6a-112">Hedef URL isteğinin sorgu dizesinde belirtildiğinden, kötü niyetli bir kullanıcı ile querystring değiştirmesine.</span><span class="sxs-lookup"><span data-stu-id="32c6a-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="32c6a-113">Değiştirilen bir sorgu dizesi, kullanıcı bir dış, kötü amaçlı sitesine yönlendirmek site izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="32c6a-114">Bu teknik açık bir yeniden yönlendirme (veya yeniden yönlendirme) saldırısı olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="32c6a-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="32c6a-115">Örnek saldırının</span><span class="sxs-lookup"><span data-stu-id="32c6a-115">An example attack</span></span>

<span data-ttu-id="32c6a-116">Kötü niyetli bir kullanıcı, bir kullanıcının kimlik bilgileri veya uygulamanızdan hassas bilgileri kötü amaçlı kullanıcı erişmesine izin vermek için amaçlanan bir saldırı geliştirebilir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-116">A malicious user could develop an attack intended to allow the malicious user access to a user's credentials or sensitive information on your app.</span></span> <span data-ttu-id="32c6a-117">Saldırı başlatmak için kullanıcının, sitenin oturum açma sayfasına bir bağlantı ile'ı bunlar ikna bir `returnUrl` URL'ye eklenen sorgu dizesi değeri.</span><span class="sxs-lookup"><span data-stu-id="32c6a-117">To begin the attack, they convince the user to click a link to your site's login page, with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="32c6a-118">Örneğin, [NerdDinner.com](http://nerddinner.com) örnek uygulama (ASP.NET MVC için yazılan) bu tür bir oturum açma sayfasına burada içerir: ``http://nerddinner.com/Account/LogOn?returnUrl=/Home/About``.</span><span class="sxs-lookup"><span data-stu-id="32c6a-118">For example, the [NerdDinner.com](http://nerddinner.com) sample application (written for ASP.NET MVC) includes such a login page here: ``http://nerddinner.com/Account/LogOn?returnUrl=/Home/About``.</span></span> <span data-ttu-id="32c6a-119">Saldırı sonra şu adımları izler:</span><span class="sxs-lookup"><span data-stu-id="32c6a-119">The attack then follows these steps:</span></span>

1. <span data-ttu-id="32c6a-120">Kullanıcı, bir bağlantı ``http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn`` (Not, ikinci URL'dir nerddi**n**er, değil nerddi**nn**er).</span><span class="sxs-lookup"><span data-stu-id="32c6a-120">User clicks a link to ``http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn`` (note, second URL is nerddi**n**er, not nerddi**nn**er).</span></span>
2. <span data-ttu-id="32c6a-121">Kullanıcı başarıyla oturum.</span><span class="sxs-lookup"><span data-stu-id="32c6a-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="32c6a-122">Kullanıcı için (site tarafından) yönlendirilir ``http://nerddiner.com/Account/LogOn`` (gerçek sitesine benzeyen kötü amaçlı site).</span><span class="sxs-lookup"><span data-stu-id="32c6a-122">The user is redirected (by the site) to ``http://nerddiner.com/Account/LogOn`` (malicious site that looks like real site).</span></span>
4. <span data-ttu-id="32c6a-123">Kullanıcı yeniden oturum (kimlik bilgilerini site kötü amaçlı vermiş) ve gerçek sitenin yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="32c6a-124">Kullanıcı büyük olasılıkla, ilk oturum açma girişimi başarısız oldu düşünüyorsanız ve bunların ikincisi başarılı oldu.</span><span class="sxs-lookup"><span data-stu-id="32c6a-124">The user will likely believe their first attempt to log in failed, and their second one was successful.</span></span> <span data-ttu-id="32c6a-125">Büyük olasılıkla farkında kalmaları kimlik bilgilerini tehlikeye.</span><span class="sxs-lookup"><span data-stu-id="32c6a-125">They'll most likely remain unaware their credentials have been compromised.</span></span>

![Açık yeniden yönlendirme saldırısına işlemi](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="32c6a-127">Oturum açma sayfaları ek olarak bazı siteler yeniden yönlendirme sayfaları veya uç noktaları sağlar.</span><span class="sxs-lookup"><span data-stu-id="32c6a-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="32c6a-128">Uygulamanızı sahip açık bir yeniden yönlendirme içeren bir sayfa düşünün ``/Home/Redirect``.</span><span class="sxs-lookup"><span data-stu-id="32c6a-128">Imagine your app has a page with an open redirect, ``/Home/Redirect``.</span></span> <span data-ttu-id="32c6a-129">Bir saldırgan, örneğin, giden e-posta iletisinde bir bağlantı oluşturabilir ``[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login``.</span><span class="sxs-lookup"><span data-stu-id="32c6a-129">An attacker could create, for example, a link in an email that goes to ``[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login``.</span></span> <span data-ttu-id="32c6a-130">Tipik bir kullanıcıyı URL'de bakın ve site adınızı ile başlayan bakın.</span><span class="sxs-lookup"><span data-stu-id="32c6a-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="32c6a-131">Güvenen, bunlar bağlantıya tıklayarak.</span><span class="sxs-lookup"><span data-stu-id="32c6a-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="32c6a-132">Açık yeniden yönlendirme sonra kullanıcı dilediğiniz aynı görünür, kimlik avı siteye gönderir ve kullanıcı büyük olasılıkla misiniz ne düşünüyorsanız için oturum açma adıdır, sitenizi.</span><span class="sxs-lookup"><span data-stu-id="32c6a-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="32c6a-133">Açık yeniden yönlendirme saldırılarına karşı koruma</span><span class="sxs-lookup"><span data-stu-id="32c6a-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="32c6a-134">Web uygulamaları geliştirirken, tüm kullanıcı tarafından sağlanan verileri güvenilmez olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="32c6a-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="32c6a-135">URL içeriğine göre kullanıcı yönlendiren işlevselliği uygulamanız varsa, bu tür yeniden yönlendirmeleri yalnızca yerel olarak, uygulamanızın içinde (veya bilinen bir URL, sorgu dizesinde sağlanan olmayan herhangi bir URL adresi) yapıldığını emin olun.</span><span class="sxs-lookup"><span data-stu-id="32c6a-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="32c6a-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="32c6a-136">LocalRedirect</span></span>

<span data-ttu-id="32c6a-137">Kullanım ``LocalRedirect`` temel yardımcı yöntemini `Controller` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="32c6a-137">Use the ``LocalRedirect`` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="32c6a-138">``LocalRedirect``yerel olmayan URL'si belirtilmişse, bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="32c6a-138">``LocalRedirect`` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="32c6a-139">Aksi takdirde, olduğu gibi davranır ``Redirect`` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="32c6a-139">Otherwise, it behaves just like the ``Redirect`` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="32c6a-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="32c6a-140">IsLocalUrl</span></span>

<span data-ttu-id="32c6a-141">Kullanım [IsLocalUrl](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iurlhelper#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) yöntemi yönlendirmeden önce URL'leri test etmek için:</span><span class="sxs-lookup"><span data-stu-id="32c6a-141">Use the [IsLocalUrl](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.iurlhelper#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="32c6a-142">Aşağıdaki örnek, bir URL yeniden yönlendirme önce yerel olup olmadığını denetlemek gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="32c6a-143">`IsLocalUrl` Yöntemi, kötü amaçlı bir siteye yanlışlıkla yönlendirildi kullanıcıları korur.</span><span class="sxs-lookup"><span data-stu-id="32c6a-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="32c6a-144">Yerel olmayan URL yerel bir URL beklenirken bir durumda sağlandığında sağlanan URL ayrıntılarını oturum açabilir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="32c6a-145">Yeniden yönlendirme günlüğü URL'leri yeniden yönlendirme saldırılarına tanılamalarına yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="32c6a-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
