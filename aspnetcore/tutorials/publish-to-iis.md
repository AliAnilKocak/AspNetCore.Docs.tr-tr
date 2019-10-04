---
title: IIS 'de ASP.NET Core uygulaması yayımlama
author: guardrex
description: ASP.NET Core uygulamasının bir IIS sunucusunda nasıl barındırılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 820527cc15f883c906d2fdf1c073d443a5b3b40e
ms.sourcegitcommit: d8b12cc1716ee329d7bd2300e201b61e15d506ac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71942879"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="b5614-103">IIS 'de ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="b5614-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="b5614-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b5614-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b5614-105">Bu öğreticide bir IIS sunucusunda ASP.NET Core uygulamasının nasıl barındırılacağını gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b5614-105">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="b5614-106">Bu öğreticide aşağıdaki konular ele alınmaktadır:</span><span class="sxs-lookup"><span data-stu-id="b5614-106">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b5614-107">.NET Core barındırma paketi 'ni Windows Server 'a yükler.</span><span class="sxs-lookup"><span data-stu-id="b5614-107">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="b5614-108">IIS Yöneticisi 'nde bir IIS sitesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b5614-108">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="b5614-109">ASP.NET Core uygulamasını dağıtın.</span><span class="sxs-lookup"><span data-stu-id="b5614-109">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5614-110">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="b5614-110">Prerequisites</span></span>

* <span data-ttu-id="b5614-111">Geliştirme makinesinde yüklü [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="b5614-111">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="b5614-112">**Web sunucusu (IIS)** sunucu rolüyle yapılandırılmış Windows Server.</span><span class="sxs-lookup"><span data-stu-id="b5614-112">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="b5614-113">Sunucunuz Web sitelerini IIS ile barındırmak üzere yapılandırılmamışsa, <xref:host-and-deploy/iis/index#iis-configuration> makalenin *IIS yapılandırması* bölümündeki yönergeleri izleyin ve ardından Bu öğreticiye geri dönün.</span><span class="sxs-lookup"><span data-stu-id="b5614-113">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="b5614-114">**IIS yapılandırması ve Web sitesi güvenliği, bu öğretici kapsamında olmayan kavramları içerir.**</span><span class="sxs-lookup"><span data-stu-id="b5614-114">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="b5614-115">IIS 'de üretim uygulamalarını barındırmadan önce, [MICROSOFT IIS BELGELERINDEKI](https://www.iis.net/) IIS KıLAVUZUNA ve [IIS ile barındırma hakkında ASP.NET Core makalesine](xref:host-and-deploy/iis/index) başvurun.</span><span class="sxs-lookup"><span data-stu-id="b5614-115">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="b5614-116">Bu öğreticide kapsanmayan IIS barındırması için önemli senaryolar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b5614-116">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="b5614-117">ASP.NET Core veri koruması için bir kayıt defteri kovanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="b5614-117">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="b5614-118">Uygulama havuzunun Access Control listesi (ACL) yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b5614-118">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="b5614-119">IIS dağıtım kavramlarına odaklanmak için bu öğretici, IIS 'de HTTPS güvenliği olmayan bir uygulama dağıtır.</span><span class="sxs-lookup"><span data-stu-id="b5614-119">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="b5614-120">HTTPS protokolü için etkinleştirilmiş bir uygulamayı barındırma hakkında daha fazla bilgi için, bu makalenin [ek kaynaklar](#additional-resources) bölümündeki güvenlik konularına bakın.</span><span class="sxs-lookup"><span data-stu-id="b5614-120">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="b5614-121">ASP.NET Core uygulamalar barındırılmasına yönelik daha fazla rehberlik <xref:host-and-deploy/iis/index> makalesinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b5614-121">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="b5614-122">Paket barındırma .NET Core'u yükleme</span><span class="sxs-lookup"><span data-stu-id="b5614-122">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="b5614-123">*.NET Core barındırma paketi* 'ni IIS sunucusuna yükler.</span><span class="sxs-lookup"><span data-stu-id="b5614-123">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="b5614-124">.NET Core çalışma zamanı, .NET Core kitaplığı paketi yükler ve [ASP.NET Core Modülü](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="b5614-124">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="b5614-125">Modül IIS çalıştırılacak uygulamaları ASP.NET Core sağlar.</span><span class="sxs-lookup"><span data-stu-id="b5614-125">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="b5614-126">Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:</span><span class="sxs-lookup"><span data-stu-id="b5614-126">Download the installer using the following link:</span></span>

[<span data-ttu-id="b5614-127">Geçerli .NET Core barındırma Paket Yükleyici (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="b5614-127">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="b5614-128">Yükleyiciyi IIS sunucusunda çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b5614-128">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="b5614-129">Sunucuyu yeniden başlatın ya da **net stop idi** ' i ve ardından bir komut kabuğu 'ndan **net start w3svc** ' i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b5614-129">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="b5614-130">IIS sitesi oluştur</span><span class="sxs-lookup"><span data-stu-id="b5614-130">Create the IIS site</span></span>

1. <span data-ttu-id="b5614-131">IIS sunucusunda, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b5614-131">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="b5614-132">Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır.</span><span class="sxs-lookup"><span data-stu-id="b5614-132">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="b5614-133">IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın.</span><span class="sxs-lookup"><span data-stu-id="b5614-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="b5614-134">Sağ **siteleri** klasör.</span><span class="sxs-lookup"><span data-stu-id="b5614-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="b5614-135">Seçin **Web sitesi Ekle** bağlam menüsünde.</span><span class="sxs-lookup"><span data-stu-id="b5614-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="b5614-136">Bir **site adı** belirtin ve **fiziksel yolu** , oluşturduğunuz uygulamanın dağıtım klasörüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b5614-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="b5614-137">**Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b5614-137">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="b5614-138">ASP.NET Core Razor Pages uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="b5614-138">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="b5614-139">Razor Pages uygulaması oluşturmak için öğreticiyiizleyin.<xref:getting-started></span><span class="sxs-lookup"><span data-stu-id="b5614-139">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="b5614-140">Uygulamayı yayımlama ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="b5614-140">Publish and deploy the app</span></span>

<span data-ttu-id="b5614-141">*Uygulama yayımlama* , bir sunucu tarafından barındırılabilecek derlenmiş bir uygulama oluşturmak anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="b5614-141">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="b5614-142">*Uygulama dağıtma* , yayımlanan uygulamayı bir barındırma sistemine taşımak anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="b5614-142">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="b5614-143">Yayımla adımı [.NET Core SDK](/dotnet/core/sdk)tarafından işlenir, ancak dağıtım adımı çeşitli yaklaşımlar tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="b5614-143">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="b5614-144">Bu öğretici, şu konumda *klasör* dağıtım yaklaşımını benimsemektedir:</span><span class="sxs-lookup"><span data-stu-id="b5614-144">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="b5614-145">Uygulama bir klasöre yayımlanır.</span><span class="sxs-lookup"><span data-stu-id="b5614-145">The app is published to a folder.</span></span>
* <span data-ttu-id="b5614-146">Klasörün içeriği IIS sitesinin klasörüne taşınır (IIS Yöneticisi 'nde sitenin **fiziksel yolu** ).</span><span class="sxs-lookup"><span data-stu-id="b5614-146">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b5614-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5614-147">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b5614-148">**Çözüm Gezgini** projede projeye sağ tıklayın ve **Yayımla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="b5614-148">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="b5614-149">**Bir yayımlama hedefi seç** iletişim kutusunda, **klasörü** Yayımla seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="b5614-149">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="b5614-150">**Klasör veya dosya paylaşma** yolunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b5614-150">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="b5614-151">Geliştirme makinesinde bir ağ paylaşımında bulunan IIS sitesi için bir klasör oluşturduysanız, paylaşımın yolunu belirtin.</span><span class="sxs-lookup"><span data-stu-id="b5614-151">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="b5614-152">Geçerli kullanıcının paylaşıma yayımlamak için yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b5614-152">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="b5614-153">IIS sunucusunda IIS site klasörüne doğrudan dağıtım yapadıysanız, kaldırılabilir medyada bir klasöre yayımlayın ve yayımlanan uygulamayı sunucuda, sitenin **fiziksel yolu** olan sunucudaki IIS site klasörüne fiziksel olarak taşıyın.</span><span class="sxs-lookup"><span data-stu-id="b5614-153">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="b5614-154">*Bin/Release/{Target Framework}/Publish* klasörünün IÇERIĞINI sunucusundaki IIS site klasörüne taşıyın, bu site, sitenin IIS Yöneticisi 'ndeki **fiziksel yoludur** .</span><span class="sxs-lookup"><span data-stu-id="b5614-154">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b5614-155">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b5614-155">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="b5614-156">Bir komut kabuğunda, uygulamayı sürüm yapılandırması 'nda [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutuyla yayımlayın:</span><span class="sxs-lookup"><span data-stu-id="b5614-156">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="b5614-157">*Bin/Release/{Target Framework}/Publish* klasörünün IÇERIĞINI sunucusundaki IIS site klasörüne taşıyın, bu site, sitenin IIS Yöneticisi 'ndeki **fiziksel yoludur** .</span><span class="sxs-lookup"><span data-stu-id="b5614-157">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b5614-158">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b5614-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b5614-159">**Çözümdeki** projeye sağ tıklayın ve**Yayımla klasörünü** **Yayımla ' yı seçin.**  > </span><span class="sxs-lookup"><span data-stu-id="b5614-159">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="b5614-160">**Klasör seçin** yolunu ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b5614-160">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="b5614-161">Geliştirme makinesinde bir ağ paylaşımında bulunan IIS sitesi için bir klasör oluşturduysanız, paylaşımın yolunu belirtin.</span><span class="sxs-lookup"><span data-stu-id="b5614-161">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="b5614-162">Geçerli kullanıcının paylaşıma yayımlamak için yazma erişimi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b5614-162">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="b5614-163">IIS sunucusunda IIS site klasörüne doğrudan dağıtım yapadıysanız, kaldırılabilir medyada bir klasöre yayımlayın ve yayımlanan uygulamayı sunucuda, sitenin **fiziksel yolu** olan sunucudaki IIS site klasörüne fiziksel olarak taşıyın.</span><span class="sxs-lookup"><span data-stu-id="b5614-163">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="b5614-164">*Bin/Release/{Target Framework}/Publish* klasörünün IÇERIĞINI sunucusundaki IIS site klasörüne taşıyın, bu site, sitenin IIS Yöneticisi 'ndeki **fiziksel yoludur** .</span><span class="sxs-lookup"><span data-stu-id="b5614-164">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="b5614-165">Web sitesine Gözat</span><span class="sxs-lookup"><span data-stu-id="b5614-165">Browse the website</span></span>

<span data-ttu-id="b5614-166">Uygulamanın ilk isteği aldıktan sonra tarayıcıda erişilebilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b5614-166">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="b5614-167">Site için IIS Yöneticisi 'nde oluşturduğunuz uç nokta bağlamasındaki uygulamaya bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b5614-167">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b5614-168">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="b5614-168">Next steps</span></span>

<span data-ttu-id="b5614-169">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="b5614-169">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b5614-170">.NET Core barındırma paketi 'ni Windows Server 'a yükler.</span><span class="sxs-lookup"><span data-stu-id="b5614-170">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="b5614-171">IIS Yöneticisi 'nde bir IIS sitesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b5614-171">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="b5614-172">ASP.NET Core uygulamasını dağıtın.</span><span class="sxs-lookup"><span data-stu-id="b5614-172">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="b5614-173">IIS 'de ASP.NET Core uygulamaları barındırma hakkında daha fazla bilgi için bkz. IIS genel bakış makalesi:</span><span class="sxs-lookup"><span data-stu-id="b5614-173">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="b5614-174">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b5614-174">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="b5614-175">ASP.NET Core belge kümesi makaleleri</span><span class="sxs-lookup"><span data-stu-id="b5614-175">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="b5614-176">ASP.NET Core Uygulama dağıtımıyla ilgili makaleler</span><span class="sxs-lookup"><span data-stu-id="b5614-176">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="b5614-177">Mac için Visual Studio kullanarak bir Web uygulamasını bir klasöre yayımlama</span><span class="sxs-lookup"><span data-stu-id="b5614-177">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="b5614-178">IIS HTTPS yapılandırması makaleleri</span><span class="sxs-lookup"><span data-stu-id="b5614-178">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="b5614-179">IIS Yöneticisi 'nde SSL 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b5614-179">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="b5614-180">IIS 'de SSL ayarlama</span><span class="sxs-lookup"><span data-stu-id="b5614-180">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="b5614-181">IIS ve Windows Server makaleleri</span><span class="sxs-lookup"><span data-stu-id="b5614-181">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="b5614-182">Resmi Microsoft IIS sitesi</span><span class="sxs-lookup"><span data-stu-id="b5614-182">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="b5614-183">Windows Server Teknik İçerik Kitaplığı</span><span class="sxs-lookup"><span data-stu-id="b5614-183">Windows Server technical content library</span></span>](/windows-server/windows-server)
