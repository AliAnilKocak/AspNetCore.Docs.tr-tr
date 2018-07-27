---
title: Visual Studio ile Azure'a bir ASP.NET Core uygulaması yayımlama
author: rick-anderson
description: Visual Studio kullanarak Azure App Service'e bir ASP.NET Core uygulaması yayımlama hakkında bilgi edinin.
ms.author: riande
ms.date: 12/16/2017
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7211da268c1c52e7b859e6f98ce433fd19b218ff
ms.sourcegitcommit: 516d0645c35ea784a3ae807be087ae70446a46ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39320732"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Visual Studio ile Azure'a bir ASP.NET Core uygulaması yayımlama

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs), ve [Rachel Appel](https://twitter.com/rachelappel)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Bkz: [Mac için Visual Studio'dan azure'a Yayımla](https://blog.xamarin.com/publish-azure-visual-studio-mac/) macOS üzerinde çalışıyorsanız ve o.

App Service dağıtım sorun gidermek için bkz: [Azure App Service'te ASP.NET Core sorunlarını giderme](xref:host-and-deploy/azure-apps/troubleshoot).

## <a name="set-up"></a>Ayarlama

* Açık bir [ücretsiz Azure hesabı](https://azure.microsoft.com/free/dotnet/) tane yoksa. 

## <a name="create-a-web-app"></a>Bir web uygulaması oluşturma

Visual Studio Başlangıç sayfasında, seçin **Dosya > Yeni > Proje...**

![Dosya menüsü](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Tamamlamak **yeni proje** iletişim:

* Sol bölmede seçin **.NET Core**.
* Orta bölmede seçin **ASP.NET Core Web uygulaması**.
* Seçin **Tamam**.

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj.png)

İçinde **yeni ASP.NET Core Web uygulaması** iletişim:

* Seçin **Web uygulaması**.
* Seçin **kimlik doğrulamayı Değiştir**.

![Yeni Proje iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**Kimlik doğrulamayı Değiştir** iletişim kutusu görüntülenir. 

* Seçin **bireysel kullanıcı hesapları**.
* Seçin **Tamam** dönmek için **yeni ASP.NET Core Web uygulaması**, ardından **Tamam** yeniden.

![Yeni ASP.NET Core Web kimlik doğrulaması iletişim kutusu](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio çözüm oluşturur.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

* Projeyi çalıştırmak için CTRL + F5 tuşlarına basın.
* Test **hakkında** ve **kişi** bağlantıları.

![Web uygulamasını localhost üzerinde Microsoft Edge'de açın](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Bir kullanıcı kaydı

* Seçin **kaydetme** ve yeni bir kullanıcı kaydı. Kurgusal bir e-posta adresini kullanabilirsiniz. Gönderdiğinizde, sayfa şu iletiyi görüntüler:

    *"İç sunucu hatası: veritabanı işlemi, istek işlenirken başarısız oldu. SQL özel durum: veritabanı açılamıyor. Uygulama DB bağlamı için mevcut geçişler uygulama bu sorunu çözebilir."*
* Seçin **geçerli geçişleri** ve Sayfa güncelleştirmelerini yenileyin sonra sayfa.

![İç sunucu hatası: İstek işlenirken bir veritabanı işlemi başarısız oldu. SQL özel durum: veritabanı açılamıyor. Uygulama DB bağlamı için mevcut geçişler uygulama, bu sorunu çözebilir.](publish-to-azure-webapp-using-vs/_static/mig.png)

Yeni kullanıcı kaydetmek için kullanılan e-posta uygulaması görüntüler ve **oturumunuzu** bağlantı.

![Web uygulaması, Microsoft Edge'de açın. Kaydet bağlantısına Hello metin tarafından değiştirilir email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Uygulamayı Azure'a dağıtma

Çözüm Gezgini'nde projeye sağ tıklayıp **Yayımla...** .

![Bağlamsal menüyü Yayımla bağlantısının vurgulandığı Aç](publish-to-azure-webapp-using-vs/_static/pub.png)

İçinde **Yayımla** iletişim:

* Seçin **Microsoft Azure App Service'te**.
* Dişli simgesini seçin ve ardından **profili oluştur**.
* Seçin **profili oluşturma**.

![Yayımla iletişim kutusu](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Azure kaynakları oluşturma

**App Service Oluştur** iletişim kutusu görüntülenir:

* Aboneliğinizi girin.
* **Uygulama adı**, **kaynak grubu**, ve **App Service planı** giriş alanları doldurulur. Bu adlar tutun veya bunları değiştirme.

![App Service iletişim kutusu](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Seçin **Hizmetleri** yeni bir veritabanı oluşturmak için sekmesinde.

* Yeşili **+** simgesini yeni SQL veritabanı oluşturma

![Yeni SQL veritabanı](publish-to-azure-webapp-using-vs/_static/sql.png)

* Seçin **yeni...**  üzerinde **SQL veritabanını Yapılandır** yeni bir veritabanı oluşturma iletişim kutusu.

![Yeni SQL veritabanı ve sunucu](publish-to-azure-webapp-using-vs/_static/conf.png)

**SQL Server'ı Yapılandır** iletişim kutusu görüntülenir.

* Bir yönetici kullanıcı adı ve parola girin ve ardından **Tamam**. Varsayılan koruyabilirsiniz **sunucu adı**. 

> [!NOTE]
> Yönetici kullanıcı adı olarak "admin" izin verilmiyor.

![SQL Server iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Seçin **Tamam**.

Visual Studio döner **App Service Oluştur** iletişim.

* Seçin **Oluştur** üzerinde **App Service Oluştur** iletişim.

![SQL veritabanı iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio, Azure üzerinde SQL Server ve Web uygulaması oluşturur. Bu adım birkaç dakika sürebilir. Oluşturulan kaynakları hakkında daha fazla bilgi için bkz: [ek kaynakları](#additonal-resources).

Dağıtım tamamlandığında, seçin **ayarları**:

![SQL Server iletişim yapılandırın](publish-to-azure-webapp-using-vs/_static/set.png)

Üzerinde **ayarları** sayfasının **Yayımla** iletişim:

  * Genişletin **veritabanları** ve **çalışma zamanında Bu bağlantı dizesini kullan**.
  * Genişletin **varlık çerçevesi geçişleriyle** ve **bu geçişi yayımlama Uygula**.

* Seçin **Kaydet**. Visual Studio döner **Yayımla** iletişim. 

![Yayımla iletişim kutusu: ayarlar paneli](publish-to-azure-webapp-using-vs/_static/pubs.png)

Tıklayın **yayımlama**. Visual Studio publishs uygulamanızı azure'a. Dağıtım tamamlandığında, uygulamayı bir tarayıcıda açılır.

### <a name="test-your-app-in-azure"></a>Azure'da uygulamanızı test etme

* Test **hakkında** ve **kişi** bağlantıları

* Yeni bir kullanıcı kaydı

![Microsoft Edge üzerinde Azure App Service açılan web uygulaması](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Uygulamayı güncelleştirme

* Düzen *Pages/About.cshtml* Razor sayfasını ve içeriğini değiştirin. Örneğin, "Hello ASP.NET Core!" söylemek paragraf değiştirebilirsiniz: [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Sağ tıklatın ve proje **Yayımla...**  yeniden.

![Bağlamsal menüyü Yayımla bağlantısının vurgulandığı Aç](publish-to-azure-webapp-using-vs/_static/pub.png)

* Uygulamayı yayımladıktan sonra yaptığınız değişiklikleri Azure üzerinde kullanılabilir olduğunu doğrulayın.

![Görev tamamlandığında doğrulayın](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Temizleme

Uygulamayı test etme işlemini tamamladığınızda, Git [Azure portalında](https://portal.azure.com/) ve uygulamayı silin.

* Seçin **kaynak grupları**, ardından oluşturduğunuz kaynak grubunu seçin.

![Azure portalı: Kaynak gruplarını kenar çubuğu menüsü](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* İçinde **kaynak grupları** sayfasında **Sil**.

![Azure portalı: Kaynak grupları sayfası](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Kaynak grubu adını girin ve seçin **Sil**. Artık uygulamanız ve Bu öğreticide oluşturulan tüm kaynakları Azure'dan silinecektir.

### <a name="next-steps"></a>Sonraki adımlar

* [Visual Studio ve Git ile azure'a sürekli dağıtım](xref:host-and-deploy/azure-apps/azure-continuous-deployment)

## <a name="additonal-resources"></a>Ek kaynaklar

* [Azure uygulama hizmeti](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
* [Azure kaynak grupları](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL veritabanı](https://docs.microsoft.com/azure/sql-database/)
* [Azure App Service’te uygulama sorunlarını giderme](xref:host-and-deploy/azure-apps/troubleshoot)
