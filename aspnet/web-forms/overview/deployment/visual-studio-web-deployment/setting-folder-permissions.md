---
uid: web-forms/overview/deployment/visual-studio-web-deployment/setting-folder-permissions
title: 'Visual Studio kullanarak ASP.NET Web Dağıtımı: klasör izinlerini ayarlama | Microsoft Docs'
author: tdykstra
description: Bu öğretici seri nasıl dağıtacağınız gösterilir (bir ASP.NET Yayımlama) web uygulamasını Azure App Service Web Apps veya bir üçüncü taraf barındırma sağlayıcısı tarafından usin...
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: 9715a121-fa55-4f1b-a5d2-fb3f6cd8be8f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/setting-folder-permissions
msc.type: authoredcontent
ms.openlocfilehash: 7efe267975835e889950983126088f1b637c28fb
ms.sourcegitcommit: 6784510cfb589308c3875ccb5113eb31031766b4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/08/2018
ms.locfileid: "30890404"
---
<a name="aspnet-web-deployment-using-visual-studio-setting-folder-permissions"></a><span data-ttu-id="263aa-103">Visual Studio kullanarak ASP.NET Web Dağıtımı: klasör izinlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="263aa-103">ASP.NET Web Deployment using Visual Studio: Setting Folder Permissions</span></span>
====================
<span data-ttu-id="263aa-104">Tarafından [zel Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="263aa-104">by [Tom Dykstra](https://github.com/tdykstra)</span></span>

[<span data-ttu-id="263aa-105">Başlangıç projesi indirme</span><span class="sxs-lookup"><span data-stu-id="263aa-105">Download Starter Project</span></span>](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> <span data-ttu-id="263aa-106">Bu öğretici seri nasıl dağıtacağınız gösterilir (bir ASP.NET Yayımlama) web uygulamasını Azure App Service Web Apps veya üçüncü taraf bir barındırma sağlayıcısı, Visual Studio 2012 veya Visual Studio 2010 kullanarak.</span><span class="sxs-lookup"><span data-stu-id="263aa-106">This tutorial series shows you how to deploy (publish) an ASP.NET web application to Azure App Service Web Apps or to a third-party hosting provider, by using Visual Studio 2012 or Visual Studio 2010.</span></span> <span data-ttu-id="263aa-107">Seri hakkında daha fazla bilgi için bkz: [serideki ilk öğreticide](introduction.md).</span><span class="sxs-lookup"><span data-stu-id="263aa-107">For information about the series, see [the first tutorial in the series](introduction.md).</span></span>


## <a name="overview"></a><span data-ttu-id="263aa-108">Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="263aa-108">Overview</span></span>

<span data-ttu-id="263aa-109">Bu öğreticide, klasör izinlerini ayarlama *Elmah* klasörü dağıtılan Web sitesi uygulama günlük dosyaları bu klasörde oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="263aa-109">In this tutorial, you set folder permissions for the *Elmah* folder in the deployed web site so that the application can create log files in that folder.</span></span>

<span data-ttu-id="263aa-110">Bir web uygulaması Visual Studio geliştirme Server (Cassini) veya IIS Express kullanarak Visual Studio'da test ettiğinizde, uygulama kimliği altında çalışır.</span><span class="sxs-lookup"><span data-stu-id="263aa-110">When you test a web application in Visual Studio using the Visual Studio Development Server (Cassini) or IIS Express, the application runs under your identity.</span></span> <span data-ttu-id="263aa-111">Geliştirme bilgisayarınızda büyük olasılıkla Yöneticiyseniz ve herhangi bir klasörde herhangi bir dosyaya herhangi bir şey yapmanızı tam yetkisine sahip.</span><span class="sxs-lookup"><span data-stu-id="263aa-111">You are most likely an administrator on your development computer and have full authority to do anything to any file in any folder.</span></span> <span data-ttu-id="263aa-112">Ancak, bir uygulama IIS altında çalıştığında, site atandığı uygulama havuzu için tanımlanan kimlik altında çalışır.</span><span class="sxs-lookup"><span data-stu-id="263aa-112">But when an application runs under IIS, it runs under the identity defined for the application pool that the site is assigned to.</span></span> <span data-ttu-id="263aa-113">Bu, genellikle sınırlı izinleri olan sistem tarafından tanımlanan bir hesap olur.</span><span class="sxs-lookup"><span data-stu-id="263aa-113">This is typically a system-defined account that has limited permissions.</span></span> <span data-ttu-id="263aa-114">Varsayılan olarak okuma ve Yürütme izinleri, web uygulamanızın dosyalar ve klasörler üzerinde ancak yazma erişimi yok.</span><span class="sxs-lookup"><span data-stu-id="263aa-114">By default it has read and execute permissions on your web application's files and folders, but it doesn't have write access.</span></span>

<span data-ttu-id="263aa-115">Bu, uygulamanızın oluşturduğu veya web uygulamalarında yaygın olan güncelleştirme dosyaları ihtiyacınız varsa bir sorun haline gelir.</span><span class="sxs-lookup"><span data-stu-id="263aa-115">This becomes an issue if your application creates or updates files, which is a common need in web applications.</span></span> <span data-ttu-id="263aa-116">Contoso University uygulamada Elmah XML dosyaları oluşturur *Elmah* hatalarla ilgili ayrıntıları kaydetmek için klasör.</span><span class="sxs-lookup"><span data-stu-id="263aa-116">In the Contoso University application, Elmah creates XML files in the *Elmah* folder in order to save details about errors.</span></span> <span data-ttu-id="263aa-117">Elmah şöyle kullanmayan olsa bile, sitenizi dosyaları karşıya yükleme veya sitenizdeki bir klasöre veri yazma diğer görevleri kullanıcı sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="263aa-117">Even if you don't use something like Elmah, your site might let users upload files or perform other tasks that write data to a folder in your site.</span></span>

<span data-ttu-id="263aa-118">Anımsatıcı: bir hata iletisi alırsınız veya öğreticide ilerlerken bir şey işe yaramazsa, kontrol ettiğinizden emin olun [sorun giderme sayfası](troubleshooting.md).</span><span class="sxs-lookup"><span data-stu-id="263aa-118">Reminder: If you get an error message or something doesn't work as you go through the tutorial, be sure to check the [troubleshooting page](troubleshooting.md).</span></span>

## <a name="test-error-logging-and-reporting"></a><span data-ttu-id="263aa-119">Test hata günlüğünü ve Raporlama</span><span class="sxs-lookup"><span data-stu-id="263aa-119">Test error logging and reporting</span></span>

<span data-ttu-id="263aa-120">(Visual Studio'da test edildiğinde olduğu rağmen) nasıl uygulama doğru IIS'de işe yaramazsa görmek için normalde Elmah tarafından günlüğe ve ayrıntıları görmek için Elmah hata günlüğü'nü açın bir hataya neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="263aa-120">To see how the application doesn't work correctly in IIS (although it did when you tested it in Visual Studio), you can cause an error that would normally be logged by Elmah, and then open the Elmah error log to see the details.</span></span> <span data-ttu-id="263aa-121">Bir XML dosyası oluşturun ve hata ayrıntılarını depolamak Elmah işleyemezse, bir boş hata raporu bakın.</span><span class="sxs-lookup"><span data-stu-id="263aa-121">If Elmah was unable to create an XML file and store the error details, you see an empty error report.</span></span>

<span data-ttu-id="263aa-122">Bir tarayıcı açın ve gidin `http://localhost/ContosoUniversity`, ve ardından gibi geçersiz bir URL isteği *Studentsxxx.aspx*.</span><span class="sxs-lookup"><span data-stu-id="263aa-122">Open a browser and go to `http://localhost/ContosoUniversity`, and then request an invalid URL like *Studentsxxx.aspx*.</span></span> <span data-ttu-id="263aa-123">Sistem tarafından oluşturulan hata sayfası yerine bkz *GenericErrorPage.aspx* olduğundan sayfa `customErrors` ayarı Web.config dosyasında "RemoteOnly" ve IIS yerel olarak çalıştığını:</span><span class="sxs-lookup"><span data-stu-id="263aa-123">You see a system-generated error page instead of the *GenericErrorPage.aspx* page because the `customErrors` setting in the Web.config file is "RemoteOnly" and you are running IIS locally:</span></span>

![HTTP 404 hata sayfası](setting-folder-permissions/_static/image1.png)

<span data-ttu-id="263aa-125">Şimdi Çalıştır *Elmah.axd* hata raporu görmek için.</span><span class="sxs-lookup"><span data-stu-id="263aa-125">Now run *Elmah.axd* to see the error report.</span></span> <span data-ttu-id="263aa-126">Yönetici hesabı kimlik bilgilerinizle oturum sonra (&quot;yönetici&quot; ve &quot;devpwd&quot;), Elmah bir XML dosyası oluşturamadı çünkü boş hata günlüğü sayfasını görmek *Elmah*klasörü:</span><span class="sxs-lookup"><span data-stu-id="263aa-126">After you log in with the administrator account credentials (&quot;admin&quot; and &quot;devpwd&quot;), you see an empty error log page because Elmah was unable to create an XML file in the *Elmah* folder:</span></span>

![Hata günlüğü boş](setting-folder-permissions/_static/image2.png)

## <a name="set-write-permission-on-the-elmah-folder"></a><span data-ttu-id="263aa-128">Elmah klasörü üzerinde yazma izni Ayarla</span><span class="sxs-lookup"><span data-stu-id="263aa-128">Set write permission on the Elmah folder</span></span>

<span data-ttu-id="263aa-129">Dağıtım işlemi otomatik bir parçası yapmak veya klasör izinlerini el ile ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="263aa-129">You can set folder permissions manually or you can make it an automatic part of the deployment process.</span></span> <span data-ttu-id="263aa-130">Yalnızca bu dağıttığınız ilk kez yapmak zorunda olduğundan, el ile yapmak nasıl aşağıdaki adımlar ve otomatik hale getirme karmaşık MSBuild kodu gerektirir.</span><span class="sxs-lookup"><span data-stu-id="263aa-130">Making it automatic requires complex MSBuild code, and since you only have to do this the first time you deploy, the following steps how to do it manually.</span></span> <span data-ttu-id="263aa-131">(Bu dağıtım işleminin parçası yapma hakkında daha fazla bilgi için bkz [Web yayımlama ayarı klasör izinleri](http://sedodream.com/2011/11/08/SettingFolderPermissionsOnWebPublish.aspx) Sayed Hashimi'nın blogunda.)</span><span class="sxs-lookup"><span data-stu-id="263aa-131">(For information about how to make this part of the deployment process, see [Setting Folder Permissions on Web Publish](http://sedodream.com/2011/11/08/SettingFolderPermissionsOnWebPublish.aspx) on Sayed Hashimi's blog.)</span></span>

1. <span data-ttu-id="263aa-132">İçinde **dosya Gezgini**, gitmek *C:\inetpub\wwwroot\ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="263aa-132">In **File Explorer**, navigate to *C:\inetpub\wwwroot\ContosoUniversity*.</span></span> <span data-ttu-id="263aa-133">Sağ *Elmah* klasöründe seçin **özellikleri**ve ardından **güvenlik** sekmesi.</span><span class="sxs-lookup"><span data-stu-id="263aa-133">Right-click the *Elmah* folder, select **Properties**, and then select the **Security** tab.</span></span>
2. <span data-ttu-id="263aa-134">
              **Düzenle**‘ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="263aa-134">Click **Edit**.</span></span>
3. <span data-ttu-id="263aa-135">İçinde **Elmah izinlerini** iletişim kutusunda **DefaultAppPool**ve ardından **yazma** onay kutusuna **izin** sütun.</span><span class="sxs-lookup"><span data-stu-id="263aa-135">In the **Permissions for Elmah** dialog box, select **DefaultAppPool**, and then select the **Write** check box in the **Allow** column.</span></span>

    ![ELMAH klasör izinlerini](setting-folder-permissions/_static/image3.png)

    <span data-ttu-id="263aa-137">(Görmüyorsanız **DefaultAppPool** içinde **grup veya kullanıcı adları** listesi, büyük olasılıkla başka bir yöntem Bu öğreticide belirtilenden IIS ve ASP.NET 4 bilgisayarınızda ayarlamak için kullandığınız.</span><span class="sxs-lookup"><span data-stu-id="263aa-137">(If you don't see **DefaultAppPool** in the **Group or user names** list, you probably used some other method than the one specified in this tutorial to set up IIS and ASP.NET 4 on your computer.</span></span> <span data-ttu-id="263aa-138">Bu durumda, hangi kimlik Contoso University uygulama ve bu kimliğe yazma izni atanmış uygulama havuzu tarafından kullanılan bulun.</span><span class="sxs-lookup"><span data-stu-id="263aa-138">In that case, find out what identity is used by the application pool assigned to the Contoso University application, and grant write permission to that identity.</span></span> <span data-ttu-id="263aa-139">Uygulama havuzu kimlikleri ilgili bağlantıları bu öğreticinin sonunda bakın.) Tıklatın **Tamam** her iki iletişim kutularında.</span><span class="sxs-lookup"><span data-stu-id="263aa-139">See the links about application pool identities at the end of this tutorial.) Click **OK** in both dialog boxes.</span></span>

## <a name="retest-error-logging-and-reporting"></a><span data-ttu-id="263aa-140">Hata günlüğü ve raporlama yeniden sınayın</span><span class="sxs-lookup"><span data-stu-id="263aa-140">Retest error logging and reporting</span></span>

<span data-ttu-id="263aa-141">Test hata tekrar aynı şekilde (istek bozuk bir URL) neden olarak ve çalıştırma **hata günlüğü** sayfası.</span><span class="sxs-lookup"><span data-stu-id="263aa-141">Test by causing an error again in the same way (request a bad URL) and run the **Error Log** page.</span></span> <span data-ttu-id="263aa-142">Bu süre hata sayfasında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="263aa-142">This time the error appears on the page.</span></span>

![ELMAH hata günlüğü sayfası](setting-folder-permissions/_static/image4.png)

## <a name="summary"></a><span data-ttu-id="263aa-144">Özet</span><span class="sxs-lookup"><span data-stu-id="263aa-144">Summary</span></span>

<span data-ttu-id="263aa-145">Contoso University almak gerekli görevlerin tümünü, yerel bilgisayarınızda IIS'de düzgün çalışmasını artık tamamladınız.</span><span class="sxs-lookup"><span data-stu-id="263aa-145">You have now completed all of the tasks necessary to get Contoso University working correctly in IIS on your local computer.</span></span> <span data-ttu-id="263aa-146">Sonraki öğreticide, sitenin genel kullanıma açık Azure'a dağıtarak hale getirir.</span><span class="sxs-lookup"><span data-stu-id="263aa-146">In the next tutorial, you will make the site publicly available by deploying it to Azure.</span></span>

## <a name="more-information"></a><span data-ttu-id="263aa-147">Daha fazla bilgi</span><span class="sxs-lookup"><span data-stu-id="263aa-147">More information</span></span>

<span data-ttu-id="263aa-148">Bu örnekte, neden Elmah günlük dosyalarını kaydedemedi nedeni çok açıktır.</span><span class="sxs-lookup"><span data-stu-id="263aa-148">In this example, the reason why Elmah was unable to save log files was fairly obvious.</span></span> <span data-ttu-id="263aa-149">Sorunun nedenini göreceksiniz olduğu durumlarda IIS izleme kullanabilirsiniz; bkz: [sorun giderme başarısız istekleri kullanarak izleme IIS 7'de](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis) IIS.NET sitesinde.</span><span class="sxs-lookup"><span data-stu-id="263aa-149">You can use IIS tracing in cases where the cause of the problem is not so obvious; see [Troubleshooting Failed Requests Using Tracing in IIS 7](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis) on the IIS.net site.</span></span>

<span data-ttu-id="263aa-150">Uygulama havuzu kimlikleri izinleri hakkında daha fazla bilgi için bkz: [uygulama havuzu kimlikleri](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) ve [içerik dosya sistemi ACL'lerini aracılığıyla IIS güvenli](https://www.iis.net/learn/get-started/planning-for-security/secure-content-in-iis-through-file-system-acls) IIS.NET sitesinde.</span><span class="sxs-lookup"><span data-stu-id="263aa-150">For more information about how to grant permissions to application pool identities, see [Application Pool Identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) and [Secure Content in IIS Through File System ACLs](https://www.iis.net/learn/get-started/planning-for-security/secure-content-in-iis-through-file-system-acls) on the IIS.net site.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="263aa-151">[Önceki](deploying-to-iis.md)
> [sonraki](deploying-to-production.md)</span><span class="sxs-lookup"><span data-stu-id="263aa-151">[Previous](deploying-to-iis.md)
[Next](deploying-to-production.md)</span></span>
