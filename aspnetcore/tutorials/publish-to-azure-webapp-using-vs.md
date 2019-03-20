---
title: Visual Studio ile Azure'a bir ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: Visual Studio kullanarak Azure App Service'e bir ASP.NET Core uygulaması yayımlama hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: b2b5a155d0dff28e471af449731da787f19d1faf
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208372"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="9144e-103">Visual Studio ile Azure'a bir ASP.NET Core uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="9144e-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="9144e-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs)</span><span class="sxs-lookup"><span data-stu-id="9144e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="9144e-105">Bkz: [Mac için Visual Studio'dan azure'a Yayımla](https://blog.xamarin.com/publish-azure-visual-studio-mac/) macOS üzerinde çalışıyorsanız ve o.</span><span class="sxs-lookup"><span data-stu-id="9144e-105">See [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) if you are working on macOS.</span></span>

<span data-ttu-id="9144e-106">App Service dağıtım sorun gidermek için bkz: <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="9144e-106">To troubleshoot an App Service deployment issue, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="set-up"></a><span data-ttu-id="9144e-107">Ayarlama</span><span class="sxs-lookup"><span data-stu-id="9144e-107">Set up</span></span>

* <span data-ttu-id="9144e-108">Açık bir [ücretsiz Azure hesabı](https://azure.microsoft.com/free/dotnet/) tane yoksa.</span><span class="sxs-lookup"><span data-stu-id="9144e-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="9144e-109">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="9144e-109">Create a web app</span></span>

<span data-ttu-id="9144e-110">Visual Studio Başlangıç sayfasında, seçin **Dosya > Yeni > Proje...**</span><span class="sxs-lookup"><span data-stu-id="9144e-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="9144e-112">Tamamlamak **yeni proje** iletişim:</span><span class="sxs-lookup"><span data-stu-id="9144e-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="9144e-113">Sol bölmede seçin **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="9144e-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="9144e-114">Orta bölmede seçin **ASP.NET Core Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="9144e-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9144e-115">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9144e-115">Select **OK**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="9144e-117">İçinde **yeni ASP.NET Core Web uygulaması** iletişim:</span><span class="sxs-lookup"><span data-stu-id="9144e-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="9144e-118">Seçin **Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="9144e-118">Select **Web Application**.</span></span>
* <span data-ttu-id="9144e-119">Seçin **kimlik doğrulamayı Değiştir**.</span><span class="sxs-lookup"><span data-stu-id="9144e-119">Select **Change Authentication**.</span></span>

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="9144e-121">**Kimlik doğrulamayı Değiştir** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="9144e-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="9144e-122">Seçin **bireysel kullanıcı hesapları**.</span><span class="sxs-lookup"><span data-stu-id="9144e-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="9144e-123">Seçin **Tamam** dönmek için **yeni ASP.NET Core Web uygulaması**, ardından **Tamam** yeniden.</span><span class="sxs-lookup"><span data-stu-id="9144e-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Yeni ASP.NET Core Web kimlik doğrulaması iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="9144e-125">Visual Studio çözüm oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9144e-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="9144e-126">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="9144e-126">Run the app</span></span>

* <span data-ttu-id="9144e-127">Projeyi çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="9144e-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="9144e-128">Test **hakkında** ve **kişi** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="9144e-128">Test the **About** and **Contact** links.</span></span>

![Web uygulamasını localhost üzerinde Microsoft Edge'de açın](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="9144e-130">Bir kullanıcı kaydı</span><span class="sxs-lookup"><span data-stu-id="9144e-130">Register a user</span></span>

* <span data-ttu-id="9144e-131">Seçin **kaydetme** ve yeni bir kullanıcı kaydı.</span><span class="sxs-lookup"><span data-stu-id="9144e-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="9144e-132">Kurgusal bir e-posta adresini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9144e-132">You can use a fictitious email address.</span></span> <span data-ttu-id="9144e-133">Gönderdiğinizde, sayfa şu iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="9144e-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="9144e-134">*"İç sunucu hatası: İstek işlenirken bir veritabanı işlemi başarısız. SQL özel durum: Veritabanı açılamıyor. Uygulama DB bağlamı için mevcut geçişler uygulama bu sorunu çözebilir."*</span><span class="sxs-lookup"><span data-stu-id="9144e-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="9144e-135">Seçin **geçerli geçişleri** ve Sayfa güncelleştirmelerini yenileyin sonra sayfa.</span><span class="sxs-lookup"><span data-stu-id="9144e-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![İç sunucu hatası: İstek işlenirken bir veritabanı işlemi başarısız.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="9144e-139">Yeni kullanıcı kaydetmek için kullanılan e-posta uygulaması görüntüler ve **oturumunuzu** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="9144e-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Web uygulaması, Microsoft Edge'de açın.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="9144e-142">Uygulamayı Azure'a dağıtma</span><span class="sxs-lookup"><span data-stu-id="9144e-142">Deploy the app to Azure</span></span>

<span data-ttu-id="9144e-143">Çözüm Gezgini'nde projeye sağ tıklayıp **Yayımla...** .</span><span class="sxs-lookup"><span data-stu-id="9144e-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Bağlamsal menüyü Yayımla bağlantısının vurgulandığı Aç](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="9144e-145">İçinde **Yayımla** iletişim:</span><span class="sxs-lookup"><span data-stu-id="9144e-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="9144e-146">Seçin **Microsoft Azure App Service'te**.</span><span class="sxs-lookup"><span data-stu-id="9144e-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="9144e-147">Dişli simgesini seçin ve ardından **profili oluştur**.</span><span class="sxs-lookup"><span data-stu-id="9144e-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="9144e-148">Seçin **profili oluşturma**.</span><span class="sxs-lookup"><span data-stu-id="9144e-148">Select **Create Profile**.</span></span>

![Yayımla iletişim kutusu](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="9144e-150">Azure kaynakları oluşturma</span><span class="sxs-lookup"><span data-stu-id="9144e-150">Create Azure resources</span></span>

<span data-ttu-id="9144e-151">**App Service Oluştur** iletişim kutusu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9144e-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="9144e-152">Aboneliğinizi girin.</span><span class="sxs-lookup"><span data-stu-id="9144e-152">Enter your subscription.</span></span>
* <span data-ttu-id="9144e-153">**Uygulama adı**, **kaynak grubu**, ve **App Service planı** giriş alanları doldurulur.</span><span class="sxs-lookup"><span data-stu-id="9144e-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="9144e-154">Bu adlar tutun veya bunları değiştirme.</span><span class="sxs-lookup"><span data-stu-id="9144e-154">You can keep these names or change them.</span></span>

![App Service iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="9144e-156">Seçin **Hizmetleri** yeni bir veritabanı oluşturmak için sekmesinde.</span><span class="sxs-lookup"><span data-stu-id="9144e-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="9144e-157">Yeşili **+** simgesini yeni SQL veritabanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="9144e-157">Select the green **+** icon to create a new SQL Database</span></span>

![Yeni SQL veritabanı](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="9144e-159">Seçin **yeni...**  üzerinde **SQL veritabanını Yapılandır** yeni bir veritabanı oluşturma iletişim kutusu.</span><span class="sxs-lookup"><span data-stu-id="9144e-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Yeni SQL veritabanı ve sunucu](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="9144e-161">**SQL Server'ı Yapılandır** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="9144e-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="9144e-162">Bir yönetici kullanıcı adı ve parola girin ve ardından **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="9144e-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="9144e-163">Varsayılan koruyabilirsiniz **sunucu adı**.</span><span class="sxs-lookup"><span data-stu-id="9144e-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="9144e-164">Yönetici kullanıcı adı olarak "admin" izin verilmiyor.</span><span class="sxs-lookup"><span data-stu-id="9144e-164">"admin" isn't allowed as the administrator user name.</span></span>

![SQL Server iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="9144e-166">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9144e-166">Select **OK**.</span></span>

<span data-ttu-id="9144e-167">Visual Studio döner **App Service Oluştur** iletişim.</span><span class="sxs-lookup"><span data-stu-id="9144e-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="9144e-168">Seçin **Oluştur** üzerinde **App Service Oluştur** iletişim.</span><span class="sxs-lookup"><span data-stu-id="9144e-168">Select **Create** on the **Create App Service** dialog.</span></span>

![SQL veritabanı iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="9144e-170">Visual Studio, Azure üzerinde SQL Server ve Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="9144e-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="9144e-171">Bu adım birkaç dakika sürebilir.</span><span class="sxs-lookup"><span data-stu-id="9144e-171">This step can take a few minutes.</span></span> <span data-ttu-id="9144e-172">Oluşturulan kaynakları hakkında daha fazla bilgi için bkz: [ek kaynaklar](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="9144e-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="9144e-173">Dağıtım tamamlandığında, seçin **ayarları**:</span><span class="sxs-lookup"><span data-stu-id="9144e-173">When deployment completes, select **Settings**:</span></span>

![SQL Server iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="9144e-175">Üzerinde **ayarları** sayfasının **Yayımla** iletişim:</span><span class="sxs-lookup"><span data-stu-id="9144e-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="9144e-176">Genişletin **veritabanları** ve **çalışma zamanında Bu bağlantı dizesini kullan**.</span><span class="sxs-lookup"><span data-stu-id="9144e-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="9144e-177">Genişletin **varlık çerçevesi geçişleriyle** ve **bu geçişi yayımlama Uygula**.</span><span class="sxs-lookup"><span data-stu-id="9144e-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="9144e-178">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="9144e-178">Select **Save**.</span></span> <span data-ttu-id="9144e-179">Visual Studio döner **Yayımla** iletişim.</span><span class="sxs-lookup"><span data-stu-id="9144e-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Yayımla iletişim kutusu: Ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="9144e-181">Tıklayın **yayımlama**.</span><span class="sxs-lookup"><span data-stu-id="9144e-181">Click **Publish**.</span></span> <span data-ttu-id="9144e-182">Visual Studio, uygulamanızı Azure'a yayımlar.</span><span class="sxs-lookup"><span data-stu-id="9144e-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="9144e-183">Dağıtım tamamlandığında, uygulamayı bir tarayıcıda açılır.</span><span class="sxs-lookup"><span data-stu-id="9144e-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="9144e-184">Azure'da uygulamanızı test etme</span><span class="sxs-lookup"><span data-stu-id="9144e-184">Test your app in Azure</span></span>

* <span data-ttu-id="9144e-185">Test **hakkında** ve **kişi** bağlantıları</span><span class="sxs-lookup"><span data-stu-id="9144e-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="9144e-186">Yeni bir kullanıcı kaydı</span><span class="sxs-lookup"><span data-stu-id="9144e-186">Register a new user</span></span>

![Microsoft Edge üzerinde Azure App Service açılan web uygulaması](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="9144e-188">Uygulamayı güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="9144e-188">Update the app</span></span>

* <span data-ttu-id="9144e-189">Düzen *Pages/About.cshtml* Razor sayfasını ve içeriğini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="9144e-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="9144e-190">Örneğin, "Hello ASP.NET Core!" söylemek paragraf değiştirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="9144e-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="9144e-191">Sağ tıklatın ve proje **Yayımla...**  yeniden.</span><span class="sxs-lookup"><span data-stu-id="9144e-191">Right-click on the project and select **Publish...** again.</span></span>

![Bağlamsal menüyü Yayımla bağlantısının vurgulandığı Aç](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="9144e-193">Uygulamayı yayımladıktan sonra yaptığınız değişiklikleri Azure üzerinde kullanılabilir olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="9144e-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Görev tamamlandığında doğrulayın](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="9144e-195">Temizleme</span><span class="sxs-lookup"><span data-stu-id="9144e-195">Clean up</span></span>

<span data-ttu-id="9144e-196">Uygulamayı test etme işlemini tamamladığınızda, Git [Azure portalında](https://portal.azure.com/) ve uygulamayı silin.</span><span class="sxs-lookup"><span data-stu-id="9144e-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="9144e-197">Seçin **kaynak grupları**, ardından oluşturduğunuz kaynak grubunu seçin.</span><span class="sxs-lookup"><span data-stu-id="9144e-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure portalı: Kenar menüsündeki kaynak grupları](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="9144e-199">İçinde **kaynak grupları** sayfasında **Sil**.</span><span class="sxs-lookup"><span data-stu-id="9144e-199">In the **Resource groups** page, select **Delete**.</span></span>

![Azure portalı: Kaynak grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="9144e-201">Kaynak grubu adını girin ve seçin **Sil**.</span><span class="sxs-lookup"><span data-stu-id="9144e-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="9144e-202">Artık uygulamanız ve Bu öğreticide oluşturulan tüm kaynakları Azure'dan silinecektir.</span><span class="sxs-lookup"><span data-stu-id="9144e-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="9144e-203">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="9144e-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="9144e-204">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9144e-204">Additional resources</span></span>

* [<span data-ttu-id="9144e-205">Azure uygulama hizmeti</span><span class="sxs-lookup"><span data-stu-id="9144e-205">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="9144e-206">Azure kaynak grupları</span><span class="sxs-lookup"><span data-stu-id="9144e-206">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="9144e-207">Azure SQL Veritabanı</span><span class="sxs-lookup"><span data-stu-id="9144e-207">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:host-and-deploy/azure-apps/troubleshoot>
