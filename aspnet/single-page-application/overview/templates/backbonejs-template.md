---
uid: single-page-application/overview/templates/backbonejs-template
title: Omurga şablon | Microsoft Docs
author: madskristensen
description: Backbone.js SPA şablonu
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/04/2013
ms.topic: article
ms.assetid: 00aca413-f067-4108-9bd1-cf21e64a2646
ms.technology: ''
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/templates/backbonejs-template
msc.type: authoredcontent
ms.openlocfilehash: 3b8eabd3cefcb96dc40bbf6cc6e3ee81accb0d7c
ms.sourcegitcommit: 6784510cfb589308c3875ccb5113eb31031766b4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/08/2018
ms.locfileid: "26566310"
---
<a name="backbone-template"></a><span data-ttu-id="398e2-103">Omurga şablonu</span><span class="sxs-lookup"><span data-stu-id="398e2-103">Backbone Template</span></span>
====================
<span data-ttu-id="398e2-104">tarafından [Kristensen Mads](https://github.com/madskristensen)</span><span class="sxs-lookup"><span data-stu-id="398e2-104">by [Mads Kristensen](https://github.com/madskristensen)</span></span>

> <span data-ttu-id="398e2-105">Omurga SPA şablonu Kazi Manzur Rashid tarafından yazıldı</span><span class="sxs-lookup"><span data-stu-id="398e2-105">The Backbone SPA Template was written by Kazi Manzur Rashid</span></span>
> 
> [<span data-ttu-id="398e2-106">Backbone.js SPA şablonunu yükle</span><span class="sxs-lookup"><span data-stu-id="398e2-106">Download the Backbone.js SPA Template</span></span>](https://go.microsoft.com/fwlink/?LinkId=293631)


<span data-ttu-id="398e2-107">Backbone.js SPA şablon hızlı bir şekilde kullanarak etkileşimli istemci tarafı web uygulamaları oluşturmaya başlamanıza yardımcı olmak için tasarlanmıştır [Backbone.js.](http://backbonejs.org/)</span><span class="sxs-lookup"><span data-stu-id="398e2-107">The Backbone.js SPA template is designed to get you started quickly building interactive client-side web apps using [Backbone.js.](http://backbonejs.org/)</span></span>

<span data-ttu-id="398e2-108">Şablonu, bir ASP.NET MVC Backbone.js uygulama geliştirmek için ilk bir çatı sağlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-108">The template provides an initial skeleton for developing a Backbone.js application in ASP.NET MVC.</span></span> <span data-ttu-id="398e2-109">Kutudan çıktığında kullanıcı kaydolma, oturum açma, parola sıfırlama ve temel e-posta şablonları ile kullanıcı onayı dahil olmak üzere temel kullanıcı oturum açma işlevselliği sağlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-109">Out of the box it provides basic user login functionality, including user sign-up, sign-in, password reset, and user confirmation with basic email templates.</span></span>

<span data-ttu-id="398e2-110">Gereksinimleri:</span><span class="sxs-lookup"><span data-stu-id="398e2-110">Requirements:</span></span>

- [<span data-ttu-id="398e2-111">ASP.NET ve Web Araçları 2012.2 güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="398e2-111">ASP.NET and Web Tools 2012.2 update</span></span>](https://go.microsoft.com/fwlink/?LinkId=282650)

## <a name="create-a-backbone-template-project"></a><span data-ttu-id="398e2-112">Bir omurga şablonu projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="398e2-112">Create a Backbone Template Project</span></span>

<span data-ttu-id="398e2-113">İndirin ve şablonu yukarıdaki karşıdan yükleme düğmesini tıklatarak yükleyin.</span><span class="sxs-lookup"><span data-stu-id="398e2-113">Download and install the template by clicking the Download button above.</span></span> <span data-ttu-id="398e2-114">Şablonu bir Visual Studio Uzantısı (VSIX) dosyası olarak paketlenir.</span><span class="sxs-lookup"><span data-stu-id="398e2-114">The template is packaged as a Visual Studio Extension (VSIX) file.</span></span> <span data-ttu-id="398e2-115">Visual Studio yeniden başlatmanız gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="398e2-115">You might need to restart Visual Studio.</span></span>

<span data-ttu-id="398e2-116">İçinde **şablonları** bölmesinde, **yüklü şablonlar** ve genişletin **Visual C#** düğümü.</span><span class="sxs-lookup"><span data-stu-id="398e2-116">In the **Templates** pane, select **Installed Templates** and expand the **Visual C#** node.</span></span> <span data-ttu-id="398e2-117">Altında **Visual C#** seçin **Web**.</span><span class="sxs-lookup"><span data-stu-id="398e2-117">Under **Visual C#**, select **Web**.</span></span> <span data-ttu-id="398e2-118">Proje şablonları listesinde seçin **ASP.NET MVC 4 Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="398e2-118">In the list of project templates, select **ASP.NET MVC 4 Web Application**.</span></span> <span data-ttu-id="398e2-119">Proje adı ve'ı tıklatın **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="398e2-119">Name the project and click **OK**.</span></span>

![](backbonejs-template/_static/image1.png)

<span data-ttu-id="398e2-120">İçinde **yeni proje** Backbone.js SPA Proje Sihirbazı, seçin.</span><span class="sxs-lookup"><span data-stu-id="398e2-120">In the **New Project** wizard, select Backbone.js SPA Project.</span></span>

![](backbonejs-template/_static/image2.png)

<span data-ttu-id="398e2-121">Derleme ve hata ayıklama olmadan uygulamayı çalıştırmak için CTRL-F5 tuşuna basın veya hata ayıklama ile çalıştırmak için F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="398e2-121">Press Ctrl-F5 to build and run the application without debugging, or press F5 to run with debugging.</span></span>

![](backbonejs-template/_static/image3.png)

<span data-ttu-id="398e2-122">"Hesabım" tıklatarak ayarlama oturum açma sayfası açar:</span><span class="sxs-lookup"><span data-stu-id="398e2-122">Clicking "My Account" brings up the login page:</span></span>

![](backbonejs-template/_static/image4.png)

## <a name="walkthrough-client-code"></a><span data-ttu-id="398e2-123">İzlenecek yol: İstemci kodu</span><span class="sxs-lookup"><span data-stu-id="398e2-123">Walkthrough: Client Code</span></span>

<span data-ttu-id="398e2-124">Şimdi istemci tarafı ile başlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-124">Let's starts with the client side.</span></span> <span data-ttu-id="398e2-125">İstemci uygulama betikler ~/Scripts/application klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="398e2-125">The client application scripts are located in the ~/Scripts/application folder.</span></span> <span data-ttu-id="398e2-126">Uygulama yazılmış [TypeScript](http://www.typescriptlang.org/) (.ts dosyaları) (.js dosyaları) JavaScript ile derlenen.</span><span class="sxs-lookup"><span data-stu-id="398e2-126">The application is written in [TypeScript](http://www.typescriptlang.org/) (.ts files) which are compiled into JavaScript (.js files).</span></span>

<span data-ttu-id="398e2-127">**Uygulama**</span><span class="sxs-lookup"><span data-stu-id="398e2-127">**Application**</span></span>

<span data-ttu-id="398e2-128">`Application` Application.TS içinde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="398e2-128">`Application` is defined in application.ts.</span></span> <span data-ttu-id="398e2-129">Bu nesne, uygulama başlatır ve kök ad alanı olarak davranır.</span><span class="sxs-lookup"><span data-stu-id="398e2-129">This object initializes the application and acts as the root namespace.</span></span> <span data-ttu-id="398e2-130">Kullanıcının oturum açtığı gibi uygulama arasında paylaşılacak yapılandırma ve durum bilgilerini tutar.</span><span class="sxs-lookup"><span data-stu-id="398e2-130">It maintains configuration and state information that is shared across the application, such as whether the user is signed in.</span></span>

<span data-ttu-id="398e2-131">`application.start` Yöntemi kalıcı görünümler oluşturur ve kullanıcı oturum açma gibi uygulama düzeyindeki olayları için olay işleyicileri iliştirir.</span><span class="sxs-lookup"><span data-stu-id="398e2-131">The `application.start` method creates the modal views and attaches event handlers for application-level events, such as user sign-in.</span></span> <span data-ttu-id="398e2-132">Ardından, varsayılan yönlendirici oluşturur ve herhangi bir istemci-tarafı URL belirtilen olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="398e2-132">Next, it creates the default router and checks whether any client-side URL is specified.</span></span> <span data-ttu-id="398e2-133">Varsayılan URL'ye yeniden yönlendirilen değil, varsa (#! /).</span><span class="sxs-lookup"><span data-stu-id="398e2-133">If not, it redirects to the default url (#!/).</span></span>

<span data-ttu-id="398e2-134">**Olaylar**</span><span class="sxs-lookup"><span data-stu-id="398e2-134">**Events**</span></span>

<span data-ttu-id="398e2-135">Olaylar, her zaman geniş geliştirme bileşenleri bağlı olduğunda önemlidir.</span><span class="sxs-lookup"><span data-stu-id="398e2-135">Events are always important when developing loosely coupled components.</span></span> <span data-ttu-id="398e2-136">Uygulamalar genellikle birden çok yanıt olarak bir kullanıcı eylemi işlemleri.</span><span class="sxs-lookup"><span data-stu-id="398e2-136">Applications often perform multiple operations in response to a user action.</span></span> <span data-ttu-id="398e2-137">Omurga modeli, koleksiyon ve görünüm gibi bileşenlerle yerleşik olayları sağlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-137">Backbone provides built-in events with components such as Model, Collection, and View.</span></span> <span data-ttu-id="398e2-138">Bu bileşenler arasında arası bağımlılıklar oluşturmak yerine, şablonu "pub/alt" modeli kullanır: `events` events.ts içinde tanımlanan nesne, olay hub'ı yayımlama ve uygulama olaylara abone olma görür.</span><span class="sxs-lookup"><span data-stu-id="398e2-138">Instead of creating inter-dependencies among these components, the template uses a "pub/sub" model: The `events` object, defined in events.ts, acts as an event hub for publishing and subscribing to application events.</span></span> <span data-ttu-id="398e2-139">`events` Tek bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="398e2-139">The `events` object is a singleton.</span></span> <span data-ttu-id="398e2-140">Aşağıdaki kod, bir olaya abone olmak ve olayı tetiklemek gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="398e2-140">The following code shows how to subscribe to an event and then trigger the event:</span></span>

[!code-csharp[Main](backbonejs-template/samples/sample1.cs)]

<span data-ttu-id="398e2-141">**Yönlendirici**</span><span class="sxs-lookup"><span data-stu-id="398e2-141">**Router**</span></span>

<span data-ttu-id="398e2-142">Backbone.js içinde bir yönlendirici istemci-tarafı sayfaları Yönlendirme ve eylemleri ve olayları bağlanmak için kullanılan yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-142">In Backbone.js, a router provides methods for routing client-side pages and connecting them to actions and events.</span></span> <span data-ttu-id="398e2-143">Şablonu router.ts içinde tek bir yönlendirici tanımlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-143">The template defines a single router, in router.ts.</span></span> <span data-ttu-id="398e2-144">Yönlendirici activable görünümler oluşturur ve görünüm geçiş yaparken durumunu korur.</span><span class="sxs-lookup"><span data-stu-id="398e2-144">The router creates the activable views and maintains the state when switching views.</span></span> <span data-ttu-id="398e2-145">(Activable görünümleri sonraki bölümde açıklanmaktadır.) Başlangıçta, iki kukla görünümler, projenin içerdiğinden ev ve hakkında.</span><span class="sxs-lookup"><span data-stu-id="398e2-145">(Activable views are described in the next section.) Initially, the project has two dummy views, Home and About.</span></span> <span data-ttu-id="398e2-146">Ayrıca, yol olmayan biliniyorsa görüntülenen bir NotFound görünüme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="398e2-146">It also has a NotFound view, which is displayed if the route is not known.</span></span>

<span data-ttu-id="398e2-147">**Görünümler**</span><span class="sxs-lookup"><span data-stu-id="398e2-147">**Views**</span></span>

<span data-ttu-id="398e2-148">Görünümleri ~/Scripts/application/görünümlerde tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="398e2-148">The views are defined in ~/Scripts/application/views.</span></span> <span data-ttu-id="398e2-149">Görünümler, activable görünümleri ve kalıcı iletişim görünümleri iki tür vardır.</span><span class="sxs-lookup"><span data-stu-id="398e2-149">There are two kinds of views, activable views and modal dialog views.</span></span> <span data-ttu-id="398e2-150">Activable görünümleri yönlendirici tarafından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="398e2-150">Activable views are invoked by the router.</span></span> <span data-ttu-id="398e2-151">Activable bir görünümü gösterilir, diğer tüm activable görünümleri etkin olur.</span><span class="sxs-lookup"><span data-stu-id="398e2-151">When an activable view is shown, all other activable views become inactive.</span></span> <span data-ttu-id="398e2-152">Activable bir görünüm oluşturmak için görünümü ile genişletmek `Activable` nesnesi:</span><span class="sxs-lookup"><span data-stu-id="398e2-152">To create an activable view, extend the view with the `Activable` object:</span></span>

[!code-javascript[Main](backbonejs-template/samples/sample2.js)]

<span data-ttu-id="398e2-153">İle genişletme `Activable` iki yeni yöntemleri görünümüne ekler `activate` ve `deactivate`.</span><span class="sxs-lookup"><span data-stu-id="398e2-153">Extending with `Activable` adds two new methods to the view, `activate` and `deactivate`.</span></span> <span data-ttu-id="398e2-154">Yönlendirici etkinleştirmek ve devre dışı görünümü için bu yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="398e2-154">The router calls these methods to activate and deactive the view.</span></span>

<span data-ttu-id="398e2-155">Kalıcı görünümleri olarak gerçekleştirilen [Twitter Bootstrap](http://twitter.github.com/bootstrap/) kalıcı iletişim kutuları.</span><span class="sxs-lookup"><span data-stu-id="398e2-155">Modal views are implemented as [Twitter Bootstrap](http://twitter.github.com/bootstrap/) modal dialogs.</span></span> <span data-ttu-id="398e2-156">`Membership` Ve `Profile` kalıcı görünümleri görünümlerdir.</span><span class="sxs-lookup"><span data-stu-id="398e2-156">The `Membership` and `Profile` views are modal views.</span></span> <span data-ttu-id="398e2-157">Model görünümlerini herhangi bir uygulama olayları tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="398e2-157">Model views can be invoked by any application events.</span></span> <span data-ttu-id="398e2-158">Örneğin, `Navigation` "Hesabım" bağlantısını tıklatarak Görünüm, gösterir ya da `Membership` görünüm veya `Profile` kullanıcının oturum açtığı bağlı olarak görüntüle.</span><span class="sxs-lookup"><span data-stu-id="398e2-158">For example, in the `Navigation` view, clicking the "My Account" link shows either the `Membership` view or the `Profile` view, depending on whether the user is logged in.</span></span> <span data-ttu-id="398e2-159">`Navigation` Ekler tıklatın sahip herhangi bir alt öğe olay işleyicilerine `data-command` özniteliği.</span><span class="sxs-lookup"><span data-stu-id="398e2-159">The `Navigation` attaches click event handlers to any child elements that have the `data-command` attribute.</span></span> <span data-ttu-id="398e2-160">HTML biçimlendirmesi şöyledir:</span><span class="sxs-lookup"><span data-stu-id="398e2-160">Here is the HTML markup:</span></span>

[!code-html[Main](backbonejs-template/samples/sample3.html)]

<span data-ttu-id="398e2-161">Olayları kanca navigation.ts kod aşağıdadır:</span><span class="sxs-lookup"><span data-stu-id="398e2-161">Here is the code in navigation.ts to hook up the events:</span></span>

[!code-csharp[Main](backbonejs-template/samples/sample4.cs)]

<span data-ttu-id="398e2-162">**Modelleri**</span><span class="sxs-lookup"><span data-stu-id="398e2-162">**Models**</span></span>

<span data-ttu-id="398e2-163">Modelleri ~/Scripts/application/modellerinde tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="398e2-163">The models are defined in ~/Scripts/application/models.</span></span> <span data-ttu-id="398e2-164">Tüm modelleri üç temel şey vardır: varsayılan öznitelikler, doğrulama kuralları ve bir sunucu tarafı bitiş noktası.</span><span class="sxs-lookup"><span data-stu-id="398e2-164">The models all have three basic things: default attributes, validation rules, and a server-side end point.</span></span> <span data-ttu-id="398e2-165">Aşağıda, genel bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="398e2-165">Here is a typical example:</span></span>

[!code-javascript[Main](backbonejs-template/samples/sample5.js)]

<span data-ttu-id="398e2-166">**Eklentiler**</span><span class="sxs-lookup"><span data-stu-id="398e2-166">**Plug-ins**</span></span>

<span data-ttu-id="398e2-167">~/Scripts/application/lib klasörü birkaç kullanışlı jQuery eklentileri içerir. Form.ts dosyası biçiminde verilerle çalışmak için bir eklenti tanımlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-167">The ~/Scripts/application/lib folder contains a few handy jQuery plug-ins. The form.ts file defines a plug-in for working with form data.</span></span> <span data-ttu-id="398e2-168">Genelde serileştirmek veya form verileri seri durumdan ve herhangi bir model doğrulama hatasını göstermek gerekir.</span><span class="sxs-lookup"><span data-stu-id="398e2-168">Often you need to serialize or deserialize form data and show any model validation errors.</span></span> <span data-ttu-id="398e2-169">Eklenti form.ts gibi yöntemlerine sahiptir `serializeFields`, `deserializeFields`, ve `showFieldErrors`.</span><span class="sxs-lookup"><span data-stu-id="398e2-169">The form.ts plug-in has methods such as `serializeFields`, `deserializeFields`, and `showFieldErrors`.</span></span> <span data-ttu-id="398e2-170">Aşağıdaki örnek, bir model için bir form serileştirmek gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="398e2-170">The following example shows how to serialize a form to a model.</span></span>

[!code-javascript[Main](backbonejs-template/samples/sample6.js)]

<span data-ttu-id="398e2-171">Eklenti flashbar.ts kullanıcıya geri bildirim iletileri çeşitli sağlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-171">The flashbar.ts plug-in gives various kinds of feedback messages to the user.</span></span> <span data-ttu-id="398e2-172">Yöntemler `$.showSuccessbar`, `$.showErrorbar` ve `$.showInfobar`.</span><span class="sxs-lookup"><span data-stu-id="398e2-172">The methods are `$.showSuccessbar`, `$.showErrorbar` and `$.showInfobar`.</span></span> <span data-ttu-id="398e2-173">Arka planda sorunsuz şekilde animasyonlu iletileri göstermek için Twitter Bootstrap uyarıları kullanır.</span><span class="sxs-lookup"><span data-stu-id="398e2-173">Behind the scenes, it uses Twitter Bootstrap alerts to show nicely animated messages.</span></span>

<span data-ttu-id="398e2-174">Eklenti confirm.ts tarayıcının değiştirir API biraz farklı olmasına rağmen iletişim kutusunda, onaylayın:</span><span class="sxs-lookup"><span data-stu-id="398e2-174">The confirm.ts plug-in replaces the browser's confirm dialog, although the API is somewhat different:</span></span>

[!code-javascript[Main](backbonejs-template/samples/sample7.js)]

## <a name="walkthrough-server-code"></a><span data-ttu-id="398e2-175">İzlenecek yol: Sunucu kodu</span><span class="sxs-lookup"><span data-stu-id="398e2-175">Walkthrough: Server Code</span></span>

<span data-ttu-id="398e2-176">Artık sunucu tarafında bakalım.</span><span class="sxs-lookup"><span data-stu-id="398e2-176">Now let's look at the server side.</span></span>

<span data-ttu-id="398e2-177">**Denetleyiciler**</span><span class="sxs-lookup"><span data-stu-id="398e2-177">**Controllers**</span></span>

<span data-ttu-id="398e2-178">Bir tek sayfalı uygulama sunucusu kullanıcı arabiriminde yalnızca küçük bir rol oynar.</span><span class="sxs-lookup"><span data-stu-id="398e2-178">In a single page application, the server plays only a small role in the user interface.</span></span> <span data-ttu-id="398e2-179">Genellikle, sunucunun başlangıç sayfası oluşturur ve ardından gönderir ve JSON verilerini alır.</span><span class="sxs-lookup"><span data-stu-id="398e2-179">Typically, the server renders the initial page and then sends and receives JSON data.</span></span>

<span data-ttu-id="398e2-180">İki MVC denetleyicileri şablonunu sahiptir: `HomeController` başlangıç sayfasını işler ve `SupportsController` yeni kullanıcı hesapları onaylayın ve parola sıfırlama için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="398e2-180">The template has two MVC controllers: `HomeController` renders the initial page, and `SupportsController` is used to confirm new user accounts and reset passwords.</span></span> <span data-ttu-id="398e2-181">Şablondaki tüm diğer denetleyicileri JSON veri gönderip ASP.NET Web API denetleyicilerinin etkilenir.</span><span class="sxs-lookup"><span data-stu-id="398e2-181">All other controllers in the template are ASP.NET Web API controllers, which send and receive JSON data.</span></span> <span data-ttu-id="398e2-182">Varsayılan olarak, denetleyicileri yeni kullanmak `WebSecurity` kullanıcıyla ilgili görevleri gerçekleştirmek için sınıf.</span><span class="sxs-lookup"><span data-stu-id="398e2-182">By default, the controllers use the new `WebSecurity` class to perform user-related tasks.</span></span> <span data-ttu-id="398e2-183">Ancak, aynı zamanda bu görevler için temsilcileri geçirmenize olanak sağlayan isteğe bağlı oluşturucular sahiptirler.</span><span class="sxs-lookup"><span data-stu-id="398e2-183">However, they also have optional constructors that let you pass in delegates for these tasks.</span></span> <span data-ttu-id="398e2-184">Bu işlem, testi kolaylaştırır ve değiştirmenizi sağlar `WebSecurity` IOC kapsayıcısını kullanarak başka bir şey ile.</span><span class="sxs-lookup"><span data-stu-id="398e2-184">This makes testing easier, and lets you replace `WebSecurity` with something else, by using an IoC Container.</span></span> <span data-ttu-id="398e2-185">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="398e2-185">Here is an example:</span></span>

[!code-csharp[Main](backbonejs-template/samples/sample8.cs)]

## <a name="views"></a><span data-ttu-id="398e2-186">Görünümler</span><span class="sxs-lookup"><span data-stu-id="398e2-186">Views</span></span>

<span data-ttu-id="398e2-187">Görünümleri modüler olacak şekilde tasarlanmıştır: bir sayfanın her bölümünü kendi özel görünüme sahiptir.</span><span class="sxs-lookup"><span data-stu-id="398e2-187">The views are designed to be modular: Each section of a page has its own dedicated view.</span></span> <span data-ttu-id="398e2-188">Bir tek sayfa uygulaması karşılık gelen bir denetleyici yok görünümleri de içerecek şekilde yaygındır.</span><span class="sxs-lookup"><span data-stu-id="398e2-188">In a single page application, it is common to include views that do not have any corresponding controller.</span></span> <span data-ttu-id="398e2-189">Bir görünüm çağırarak içerebilir `@Html.Partial('myView')`, ancak bu yorucu alır.</span><span class="sxs-lookup"><span data-stu-id="398e2-189">You can include a view by calling `@Html.Partial('myView')`, but this gets tedious.</span></span> <span data-ttu-id="398e2-190">Bu işlemi kolaylaştırmak için şablon bir yardımcı yöntem tanımlar `IncludeClientViews`, tüm belirtilen klasördeki görünümlerinin işleyen:</span><span class="sxs-lookup"><span data-stu-id="398e2-190">To make this easier, the template defines a helper method, `IncludeClientViews`, that renders all of the views in a specified folder:</span></span>

[!code-cshtml[Main](backbonejs-template/samples/sample9.cshtml)]

<span data-ttu-id="398e2-191">Klasör adı belirtilmezse, varsayılan klasör adı "ClientViews" olur.</span><span class="sxs-lookup"><span data-stu-id="398e2-191">If the folder name is not specified, the default folder name is "ClientViews".</span></span> <span data-ttu-id="398e2-192">İstemci görünümünüzü kısmi görünümleri de kullanıyorsa, kısa çizgi karakteri ile kısmi görünüm adını (örneğin, `_SignUp`).</span><span class="sxs-lookup"><span data-stu-id="398e2-192">If your client view also uses partial views, name the partial view with an underscore character (for example, `_SignUp`).</span></span> <span data-ttu-id="398e2-193">`IncludeClientViews` Yöntemi adı alt çizgi ile başlayan tüm görünümleri dışlar.</span><span class="sxs-lookup"><span data-stu-id="398e2-193">The `IncludeClientViews` method excludes any views whose name starts with an underscore.</span></span> <span data-ttu-id="398e2-194">Kısmi görünüm istemci görünüme dahil etmek için arama `Html.ClientView('SignUp')` yerine `Html.Partial('_SignUp')`.</span><span class="sxs-lookup"><span data-stu-id="398e2-194">To include a partial view in the client view, call `Html.ClientView('SignUp')` instead of `Html.Partial('_SignUp')`.</span></span>

<span data-ttu-id="398e2-195">**E-posta gönderme**</span><span class="sxs-lookup"><span data-stu-id="398e2-195">**Sending Email**</span></span>

<span data-ttu-id="398e2-196">E-posta göndermek için şablonun kullanan [posta](http://aboutcode.net/postal).</span><span class="sxs-lookup"><span data-stu-id="398e2-196">To send email, the template uses [Postal](http://aboutcode.net/postal).</span></span> <span data-ttu-id="398e2-197">Ancak, posta kodu ile geri kalanından soyutlanır `IMailer` , başka bir uygulama ile kolayca yerini alabilecek şekilde arabirim.</span><span class="sxs-lookup"><span data-stu-id="398e2-197">However, Postal is abstracted from the rest of the code with the `IMailer` interface, so you can easily replace it with another implementation.</span></span> <span data-ttu-id="398e2-198">E-posta şablonlarını görünümler/e-postalar klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="398e2-198">The email templates are located in the Views/Emails folder.</span></span> <span data-ttu-id="398e2-199">Gönderenin e-posta adresi web.config dosyasında belirtilen `sender.email` , anahtar **appSettings** bölümü.</span><span class="sxs-lookup"><span data-stu-id="398e2-199">The sender's email address is specified in the web.config file, in the `sender.email` key of the **appSettings** section.</span></span> <span data-ttu-id="398e2-200">Ayrıca, ne zaman `debug="true"` web.config dosyasında, uygulama geliştirme hızlandırmak için kullanıcı e-posta onayı gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="398e2-200">Also, when `debug="true"` in web.config, the application does not require user email confirmation, to speed up development.</span></span>

## <a name="github"></a><span data-ttu-id="398e2-201">GitHub</span><span class="sxs-lookup"><span data-stu-id="398e2-201">GitHub</span></span>

<span data-ttu-id="398e2-202">Üzerinde Backbone.js SPA şablon da bulabilirsiniz [GitHub](https://github.com/kazimanzurrashid/AspNetMvcBackboneJsSpa).</span><span class="sxs-lookup"><span data-stu-id="398e2-202">You can also find the Backbone.js SPA template on [GitHub](https://github.com/kazimanzurrashid/AspNetMvcBackboneJsSpa).</span></span>
