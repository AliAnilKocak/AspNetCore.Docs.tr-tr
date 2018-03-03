---
title: "Visual Studio kullanarak Azure ASP.NET Core uygulama yayımlama"
author: rick-anderson
description: "Visual Studio kullanarak Azure App Service için ASP.NET Core uygulama yayımlama öğrenin."
manager: wpickett
ms.author: riande
ms.date: 12/16/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 0c2905575751c9880e02d8581642a1628bea5a49
ms.sourcegitcommit: 7ac15eaae20b6d70e65f3650af050a7880115cbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/02/2018
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a><span data-ttu-id="bf306-103">Visual Studio kullanarak Azure App Service için ASP.NET Core web uygulama yayımlama</span><span class="sxs-lookup"><span data-stu-id="bf306-103">Publish an ASP.NET Core web app to Azure App Service using Visual Studio</span></span>

<span data-ttu-id="bf306-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), ve [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="bf306-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), and [Rachel Appel](https://twitter.com/rachelappel)</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="bf306-105">Bkz: [Mac için Visual Studio'dan Azure Yayımla](https://blog.xamarin.com/publish-azure-visual-studio-mac/) Mac'te çalışıyorsanız</span><span class="sxs-lookup"><span data-stu-id="bf306-105">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on a Mac.</span></span>

## <a name="set-up"></a><span data-ttu-id="bf306-106">Ayarlama</span><span class="sxs-lookup"><span data-stu-id="bf306-106">Set up</span></span>

* <span data-ttu-id="bf306-107">Açık bir [ücretsiz Azure hesabı](https://aka.ms/K5y5yh) , yoksa.</span><span class="sxs-lookup"><span data-stu-id="bf306-107">Open a [free Azure account](https://aka.ms/K5y5yh) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="bf306-108">Bir web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="bf306-108">Create a web app</span></span>

<span data-ttu-id="bf306-109">Visual Studio Başlangıç sayfasında, seçin **Dosya > Yeni > Proje...**</span><span class="sxs-lookup"><span data-stu-id="bf306-109">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="bf306-111">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="bf306-111">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="bf306-112">Sol bölmede seçin **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="bf306-112">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="bf306-113">Orta bölmede seçin **ASP.NET çekirdek Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="bf306-113">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bf306-114">Seçin **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="bf306-114">Select **OK**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="bf306-116">İçinde **çekirdek yeni bir ASP.NET Web uygulaması** iletişim:</span><span class="sxs-lookup"><span data-stu-id="bf306-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="bf306-117">Seçin **Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="bf306-117">Select **Web Application**.</span></span>
* <span data-ttu-id="bf306-118">Seçin **değiştirmek kimlik doğrulama**.</span><span class="sxs-lookup"><span data-stu-id="bf306-118">Select **Change Authentication**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="bf306-120">**Kimlik doğrulamayı Değiştir** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bf306-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="bf306-121">Seçin **bireysel kullanıcı hesapları**.</span><span class="sxs-lookup"><span data-stu-id="bf306-121">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="bf306-122">Seçin **Tamam** geri dönmek için **çekirdek yeni bir ASP.NET Web uygulaması**seçeneğini belirleyip **Tamam** yeniden.</span><span class="sxs-lookup"><span data-stu-id="bf306-122">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Yeni ASP.NET çekirdek Web kimlik doğrulaması iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="bf306-124">Visual Studio çözümü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="bf306-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="bf306-125">Uygulama Çalıştırma</span><span class="sxs-lookup"><span data-stu-id="bf306-125">Run the app</span></span>

* <span data-ttu-id="bf306-126">Projeyi çalıştırmak için CTRL + F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="bf306-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="bf306-127">Test **hakkında** ve **kişi** bağlantılar.</span><span class="sxs-lookup"><span data-stu-id="bf306-127">Test the **About** and **Contact** links.</span></span>

![Web uygulamasını localhost üzerinde Microsoft Edge'de açın](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="bf306-129">Bir kullanıcı kaydı</span><span class="sxs-lookup"><span data-stu-id="bf306-129">Register a user</span></span>

* <span data-ttu-id="bf306-130">Seçin **kaydetmek** ve yeni bir kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="bf306-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="bf306-131">Kurgusal bir e-posta adresini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bf306-131">You can use a fictitious email address.</span></span> <span data-ttu-id="bf306-132">Gönderdiğinizde, aşağıdaki hata sayfasını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="bf306-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="bf306-133">*"Dahili Sunucu hatası: veritabanı işlemi başarısız oldu istek işlenirken. SQL özel durumu: veritabanı açılamıyor. Uygulama DB bağlamı için varolan geçişler uygulama bu sorun çözülebilir."*</span><span class="sxs-lookup"><span data-stu-id="bf306-133">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="bf306-134">Seçin **uygulamak geçişler** ve Sayfa güncelleştirmelerini Yenile sonra sayfa.</span><span class="sxs-lookup"><span data-stu-id="bf306-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![İç sunucu hatası: İstek işlenirken bir veritabanı işlemi başarısız oldu.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="bf306-138">Yeni kullanıcı kaydetmek için kullanılan e-posta uygulaması görüntüler ve **oturumunuzu** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="bf306-138">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Web uygulaması Microsoft Edge'de açın.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="bf306-141">Uygulamayı Azure'a dağıtma</span><span class="sxs-lookup"><span data-stu-id="bf306-141">Deploy the app to Azure</span></span>

<span data-ttu-id="bf306-142">Çözüm Gezgini'nde projeye sağ tıklayın ve **Yayımla...** .</span><span class="sxs-lookup"><span data-stu-id="bf306-142">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Bağlamsal menü vurgulanmış Yayımla bağlantısıyla Aç](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="bf306-144">İçinde **Yayımla** iletişim:</span><span class="sxs-lookup"><span data-stu-id="bf306-144">In the **Publish** dialog:</span></span>

* <span data-ttu-id="bf306-145">Seçin **Microsoft Azure uygulama hizmeti**.</span><span class="sxs-lookup"><span data-stu-id="bf306-145">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="bf306-146">Dişli simgesini seçin ve ardından **profili oluştur**.</span><span class="sxs-lookup"><span data-stu-id="bf306-146">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="bf306-147">Seçin **profili oluşturma**.</span><span class="sxs-lookup"><span data-stu-id="bf306-147">Select **Create Profile**.</span></span>

![Yayımlama iletişim kutusu](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="bf306-149">Azure kaynakları oluşturun</span><span class="sxs-lookup"><span data-stu-id="bf306-149">Create Azure resources</span></span>

<span data-ttu-id="bf306-150">**App Service Oluştur** iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="bf306-150">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="bf306-151">Aboneliğinizi girin.</span><span class="sxs-lookup"><span data-stu-id="bf306-151">Enter your subscription.</span></span>
* <span data-ttu-id="bf306-152">**App Name**, **kaynak grubu**, ve **App Service planı** giriş alanları doldurulur.</span><span class="sxs-lookup"><span data-stu-id="bf306-152">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="bf306-153">Bu adları tutun veya onları değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bf306-153">You can keep these names or change them.</span></span>

![App Service iletişim](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="bf306-155">Seçin **Hizmetleri** sekmesinde yeni bir veritabanı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bf306-155">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="bf306-156">Yeşil seçin  **+**  yeni bir SQL veritabanı oluşturmak için simgesi</span><span class="sxs-lookup"><span data-stu-id="bf306-156">Select the green **+** icon to create a new SQL Database</span></span>

![Yeni SQL veritabanı](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="bf306-158">Seçin **yeni...**  üzerinde **SQL veritabanını yapılandırma** yeni bir veritabanı oluşturmak için iletişim kutusu.</span><span class="sxs-lookup"><span data-stu-id="bf306-158">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Yeni SQL veritabanı ve sunucu](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="bf306-160">**SQL Server'ı Yapılandır** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bf306-160">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="bf306-161">Bir yönetici kullanıcı adı ve parola girin ve ardından **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="bf306-161">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="bf306-162">Varsayılan tutabilirsiniz **sunucu adı**.</span><span class="sxs-lookup"><span data-stu-id="bf306-162">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="bf306-163">"Yönetici" Yönetici kullanıcı adı olarak izin verilmiyor.</span><span class="sxs-lookup"><span data-stu-id="bf306-163">"admin" isn't allowed as the administrator user name.</span></span>

![SQL Server iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="bf306-165">Seçin **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="bf306-165">Select **OK**.</span></span>

<span data-ttu-id="bf306-166">Visual Studio döner **App Service Oluştur** iletişim.</span><span class="sxs-lookup"><span data-stu-id="bf306-166">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="bf306-167">Seçin **oluşturma** üzerinde **App Service Oluştur** iletişim.</span><span class="sxs-lookup"><span data-stu-id="bf306-167">Select **Create** on the **Create App Service** dialog.</span></span>

![SQL veritabanı iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="bf306-169">Visual Studio, Azure üzerinde SQL Server ve Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="bf306-169">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="bf306-170">Bu adım birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="bf306-170">This step can take a few minutes.</span></span> <span data-ttu-id="bf306-171">Oluşturulan kaynakları hakkında daha fazla bilgi için bkz: [cihazıyla kaynakları](#additonal-resources).</span><span class="sxs-lookup"><span data-stu-id="bf306-171">For information on the resources created, see [Additonal resources](#additonal-resources).</span></span>

<span data-ttu-id="bf306-172">Dağıtım tamamlandığında seçin **ayarları**:</span><span class="sxs-lookup"><span data-stu-id="bf306-172">When deployment completes, select **Settings**:</span></span>

![SQL Server iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="bf306-174">Üzerinde **ayarları** sayfasında **Yayımla** iletişim:</span><span class="sxs-lookup"><span data-stu-id="bf306-174">On the **Settings** page of the **Publish** dialog:</span></span>

  * <span data-ttu-id="bf306-175">Genişletme **veritabanları** ve denetleme **çalışma zamanında Bu bağlantı dizesini kullan**.</span><span class="sxs-lookup"><span data-stu-id="bf306-175">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
  * <span data-ttu-id="bf306-176">Genişletme **Entity Framework geçişler** ve denetleme **bu geçiş yayımlama Uygula**.</span><span class="sxs-lookup"><span data-stu-id="bf306-176">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="bf306-177">Seçin **kaydetmek**.</span><span class="sxs-lookup"><span data-stu-id="bf306-177">Select **Save**.</span></span> <span data-ttu-id="bf306-178">Visual Studio döner **Yayımla** iletişim.</span><span class="sxs-lookup"><span data-stu-id="bf306-178">Visual Studio returns to the **Publish** dialog.</span></span> 

![Yayımlama iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="bf306-180">Tıklatın **yayımlama**.</span><span class="sxs-lookup"><span data-stu-id="bf306-180">Click **Publish**.</span></span> <span data-ttu-id="bf306-181">Visual Studio publishs uygulamanızı azure'a.</span><span class="sxs-lookup"><span data-stu-id="bf306-181">Visual Studio publishs your app to Azure.</span></span> <span data-ttu-id="bf306-182">Dağıtım tamamlandığında, uygulama bir tarayıcıda açılır.</span><span class="sxs-lookup"><span data-stu-id="bf306-182">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="bf306-183">Azure'da uygulamanızı test etme</span><span class="sxs-lookup"><span data-stu-id="bf306-183">Test your app in Azure</span></span>

* <span data-ttu-id="bf306-184">Test **hakkında** ve **kişi** bağlantılar</span><span class="sxs-lookup"><span data-stu-id="bf306-184">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="bf306-185">Yeni bir kullanıcı kaydı</span><span class="sxs-lookup"><span data-stu-id="bf306-185">Register a new user</span></span>

![Web uygulamasını Azure App Service'te Microsoft Edge açılır](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="bf306-187">Uygulamayı güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="bf306-187">Update the app</span></span>

* <span data-ttu-id="bf306-188">Düzen *Pages/About.cshtml* Razor sayfasında ve içeriğini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="bf306-188">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="bf306-189">Örneğin, "Merhaba ASP.NET Core!" söylemek için paragraf değiştirebilirsiniz: [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]</span><span class="sxs-lookup"><span data-stu-id="bf306-189">For example, you can modify the paragraph to say "Hello ASP.NET Core!": [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]</span></span>

* <span data-ttu-id="bf306-190">Sağ tıklatın ve proje **Yayımla...**  yeniden.</span><span class="sxs-lookup"><span data-stu-id="bf306-190">Right-click on the project and select **Publish...** again.</span></span>

![Bağlamsal menü vurgulanmış Yayımla bağlantısıyla Aç](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="bf306-192">Uygulama yayımlandıktan sonra yaptığınız değişiklikleri Azure üzerinde kullanılabilir olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="bf306-192">After the app is published, verify the changes you made are available on Azure.</span></span>

![Görev tamamlandığında doğrulayın](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="bf306-194">Temizleme</span><span class="sxs-lookup"><span data-stu-id="bf306-194">Clean up</span></span>

<span data-ttu-id="bf306-195">Uygulamayı test etme işlemini tamamladığınızda, Git [Azure portal](https://portal.azure.com/) ve uygulamayı silin.</span><span class="sxs-lookup"><span data-stu-id="bf306-195">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="bf306-196">Seçin **kaynak grupları**, oluşturduğunuz kaynak grubunu seçin.</span><span class="sxs-lookup"><span data-stu-id="bf306-196">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: Kaynak gruplarını kenar menüsü](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="bf306-198">İçinde **kaynak grupları** sayfasında, **silmek**.</span><span class="sxs-lookup"><span data-stu-id="bf306-198">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: Kaynak grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="bf306-200">Kaynak grubunun adını girin ve **silmek**.</span><span class="sxs-lookup"><span data-stu-id="bf306-200">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="bf306-201">Artık uygulamanızı ve Bu öğreticide oluşturduğunuz diğer tüm kaynaklar Azure'dan silinir.</span><span class="sxs-lookup"><span data-stu-id="bf306-201">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="bf306-202">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="bf306-202">Next steps</span></span>

* [<span data-ttu-id="bf306-203">Visual Studio ve Git ile Azure için sürekli dağıtım</span><span class="sxs-lookup"><span data-stu-id="bf306-203">Continuous Deployment to Azure with Visual Studio and Git</span></span>](xref:host-and-deploy/azure-apps/azure-continuous-deployment)

## <a name="additonal-resources"></a><span data-ttu-id="bf306-204">Diğer kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bf306-204">Additonal resources</span></span>

* [<span data-ttu-id="bf306-205">Azure uygulama hizmeti</span><span class="sxs-lookup"><span data-stu-id="bf306-205">Azure App Service</span></span>](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="bf306-206">Azure kaynak grupları</span><span class="sxs-lookup"><span data-stu-id="bf306-206">Azure resource groups</span></span>](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="bf306-207">Azure SQL veritabanı</span><span class="sxs-lookup"><span data-stu-id="bf306-207">Azure SQL Database</span></span>](https://docs.microsoft.com/azure/sql-database/)
