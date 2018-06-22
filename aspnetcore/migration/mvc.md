---
title: ASP.NET MVC ASP.NET Core MVC geçirme
author: ardalis
description: ASP.NET MVC projesinde ASP.NET Core MVC geçirme başlayacağınızı öğrenin.
ms.author: riande
ms.date: 03/07/2017
uid: migration/mvc
ms.openlocfilehash: 6fecc820177ca5033cfd00d632904a950c1b29bb
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36274781"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>ASP.NET MVC ASP.NET Core MVC geçirme

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), ve [Scott Addie](https://scottaddie.com)

Bu makalede, ASP.NET MVC projesinde geçirme başlamak gösterilmiştir [ASP.NET Core MVC](../mvc/overview.md). İşlem sırasında çoğunu ASP.NET MVC değişen vurgular. ASP.NET MVC geçiş birden çok adım bir işlemdir ve bu makalede, ilk Kurulum, temel denetleyicileri ve görünümler, statik içerik ve istemci tarafı bağımlılıkları yer almaktadır. Diğer makaleler geçirme yapılandırması ve kimlik kodu birçok ASP.NET MVC projelerinde bulunamadı kapsar.

> [!NOTE]
> Sürüm numaraları örneklerdeki geçerli olmayabilir. Buna göre projelerinizi güncelleştirmeniz gerekebilir.

## <a name="create-the-starter-aspnet-mvc-project"></a>ASP.NET MVC projesinin starter oluşturma

Yükseltme göstermek için bir ASP.NET MVC uygulaması oluşturarak başlayacağız. Adlı oluşturun *WebApp1* ad alanı sonraki adımda oluşturuyoruz ASP.NET Core proje eşleşecek şekilde.

![Visual Studio yeni proje iletişim kutusu](mvc/_static/new-project.png)

![Yeni Web uygulaması iletişim kutusu: ASP.NET şablonları panelinde seçili MVC proje şablonu](mvc/_static/new-project-select-mvc-template.png)

*İsteğe bağlı:* çözümden adını değiştirmek *WebApp1* için *Mvc5*. Visual Studio yeni çözüm adını görüntüler (*Mvc5*), hangi kolaylaştırır sonraki proje bu projeden bildirmek.

## <a name="create-the-aspnet-core-project"></a>ASP.NET Core projesi oluşturma

Yeni bir *boş* ASP.NET Core web uygulaması önceki projesi olarak aynı ada sahip (*WebApp1*) ad alanları iki proje ile eşleşecek şekilde. Aynı ad alanına sahip iki projeler arasında kodu kopyalamak kolaylaştırır. Aynı adı kullanmak üzere önceki proje daha farklı bir dizinde bu projeyi oluşturmak zorunda kalırsınız.

![Yeni Proje iletişim kutusu](mvc/_static/new_core.png)

![Yeni ASP.NET Web uygulaması iletişim kutusu: ASP.NET Core şablonları panelinde seçili boş proje şablonu](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *İsteğe bağlı:* kullanarak yeni bir ASP.NET Core uygulama oluştur *Web uygulaması* proje şablonu. Proje adı *WebApp1*, bir kimlik doğrulama seçeneğini seçip **tek tek kullanıcı hesaplarını**. Bu uygulama yeniden adlandırma *FullAspNetCore*. Size zaman proje kazandırır dönüştürmede oluşturuluyor. Sonuç görmek için veya dönüştürme projeye kodu kopyalamak için ' şablon oluşturulan kod bakabilirsiniz. Şablon tarafından oluşturulan proje ile karşılaştırmak için dönüştürme adım sıkışan gerektiğinde de yararlıdır.

## <a name="configure-the-site-to-use-mvc"></a>Siteyi MVC kullanacak şekilde yapılandırma

* .NET Core hedeflerken ASP.NET Core metapackage adlı projeye eklenen `Microsoft.AspNetCore.All` varsayılan olarak. Paketleri gibi bu pakette `Microsoft.AspNetCore.Mvc` ve `Microsoft.AspNetCore.StaticFiles`. .NET Framework'ü hedefleme, paket referanslarını ayrı ayrı *.csproj dosyasında listelenen gerekir.

`Microsoft.AspNetCore.Mvc` ASP.NET Core MVC çerçevedir. `Microsoft.AspNetCore.StaticFiles` statik dosya işleyici olur. ASP.NET çekirdeği çalışma zamanı modüler ve açıkça statik dosyaları işleme için kabul gerekir (bkz [statik dosyaları](xref:fundamentals/static-files)).

* Açık *haline* dosya ve kodu aşağıdaki ile eşleşecek şekilde değiştirin:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

`UseStaticFiles` Genişletme yöntemi statik dosya işleyici ekler. Daha önce belirtildiği gibi ASP.NET çalışma zamanı modüler ve açıkça statik dosyaları işleme için kabul gerekir. `UseMvc` Genişletme yöntemi ekler yönlendirme. Daha fazla bilgi için bkz: [uygulama başlangıç](xref:fundamentals/startup) ve [yönlendirme](xref:fundamentals/routing).

## <a name="add-a-controller-and-view"></a>Bir denetleyici ve Görünüm Ekle

Bu bölümde, bir en az denetleyici ve görünüm ASP.NET MVC denetleyicisi yer tutucular olarak hizmet verecek ve sonraki bölümde geçirmek görünümleri ekleyeceksiniz.

* Ekleme bir *denetleyicileri* klasör.

* Ekleme bir **denetleyici sınıfı** adlı *HomeController.cs* için *denetleyicileri* klasör.

![Yeni öğe iletişim ekleyin](mvc/_static/add_mvc_ctl.png)

* Ekleme bir *görünümleri* klasör.

* Ekleme bir *görünümler/giriş* klasör.

* Ekleme bir **Razor Görünüm** adlı *Index.cshtml* için *görünümler/giriş* klasör.

![Yeni öğe iletişim ekleyin](mvc/_static/view.png)

Proje yapısı aşağıda gösterilmiştir:

![Dosya ve klasörleri WebApp1 gösteren Çözüm Gezgini](mvc/_static/project-structure-controller-view.png)

Değiştir *Views/Home/Index.cshtml* aşağıdaki dosyasıyla:

```html
<h1>Hello world!</h1>
```

Uygulamayı çalıştırın.

![Web uygulaması Microsoft Edge'de Aç](mvc/_static/hello-world.png)

Bkz: [denetleyicileri](xref:mvc/controllers/actions) ve [görünümleri](xref:mvc/views/overview) daha fazla bilgi için.

En az bir çalışma ASP.NET Core proje sahibiz, biz işlevselliği ASP.NET MVC projesinden geçiş başlatabilirsiniz. Aşağıdaki taşımak ihtiyacımız var:

* istemci-tarafı içeriği (CSS, yazı tipleri ve komut dosyaları)

* denetleyiciler

* görünümler

* modeller

* Paketleme

* filtreler

* Giriş/Çıkış günlüğü, kimlik (Bu, sonraki öğreticide gerçekleştirilir.)

## <a name="controllers-and-views"></a>Denetleyicileri ve görünümler

* Yöntemlerin her biri ASP.NET MVC kopyalama `HomeController` yeni `HomeController`. ASP.NET MVC uygulamasında yerleşik şablonun denetleyici eylem yönteminin dönüş türü olduğunu unutmayın [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ASP.NET mvc'de çekirdek, dönüş eylem yöntemleri `IActionResult` yerine. `ActionResult` uygulayan `IActionResult`, bu yüzden, eylem yöntemleri dönüş türü değiştirmenize gerek yoktur.

* Kopya *About.cshtml*, *Contact.cshtml*, ve *Index.cshtml* ASP.NET Core proje için ASP.NET MVC projesindeki Razor görünüm dosyaları.

* ASP.NET Core uygulamayı çalıştırın ve her yöntemi sınayın. İşlenmiş görünüm yalnızca görünüm dosyalarının içeriği içeren böylece biz düzeni dosyasını veya stilleri henüz, geçirilen henüz. Düzen oluşturulan dosyanın bağlantılarını olmayacaktır `About` ve `Contact` tarayıcıdan çağırma gerekecek şekilde görünümler (Değiştir **4492** projenizde kullanılan bağlantı noktası numarası ile).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Kişi sayfası](mvc/_static/contact-page.png)

Stil ve menü öğelerini eksikliği unutmayın. Biz, sonraki bölümde düzeltmesi.

## <a name="static-content"></a>Statik içerik

ASP.NET MVC önceki sürümlerinde, statik içerik web projesi kökünden barındırılan ve sunucu tarafı dosyalarıyla intermixed. ASP.NET çekirdek statik içerik içinde barındırılan *wwwroot* klasör. Statik içerik eski ASP.NET MVC uygulamanıza kopyalamak istersiniz *wwwroot* ASP.NET Core projenizdeki klasöre. Bu örnek dönüştürmede:

* Kopya *favicon.ico* eski MVC proje dosyasından *wwwroot* ASP.NET Core proje klasöründe.

ASP.NET MVC eski proje kullanır [önyükleme](https://getbootstrap.com/) önyükleme dosyaları stil ve depoları *içerik* ve *betikleri* klasörler. Önyükleme düzeni dosyasında eski ASP.NET MVC projesinin oluşturulan, şablonu başvuruyor (*Views/Shared/_Layout.cshtml*). Kopyalamak *bootstrap.js* ve *bootstrap.css* ASP.NET MVC dosyalarından proje için *wwwroot* yeni proje klasöründe. Bunun yerine, sonraki bölümde CDN'ler kullanarak önyükleme desteği (ve diğer istemci-tarafı kitaplıklar) ekleyeceğiz.

## <a name="migrate-the-layout-file"></a>Düzen dosyasını geçirme

* Kopya *_ViewStart.cshtml* eski ASP.NET MVC projesinin dosyadan *görünümleri* ASP.NET Core projenin klasörüne *görünümleri* klasör. *_ViewStart.cshtml* dosya ASP.NET Core MVC'de değişmemiştir.

* Oluşturma bir *görünümler/paylaşılan* klasör.

* *İsteğe bağlı:* kopya *_viewımports.cshtml* gelen *FullAspNetCore* MVC projesinin *görünümleri* ASP.NET Core projenin klasörüne  *Görünümler* klasör. Tüm ad alanı bildirimini kaldırın *_viewımports.cshtml* dosya. *_Viewımports.cshtml* dosya ad alanları için tüm görünüm dosyaları sağlar ve getirir [etiket Yardımcıları](xref:mvc/views/tag-helpers/intro). Etiket Yardımcıları yeni düzen dosyasında kullanılır. *_Viewımports.cshtml* dosya ASP.NET Core için yenidir.

* Kopya *_Layout.cshtml* eski ASP.NET MVC projesinin dosyadan *görünümler/paylaşılan* ASP.NET Core projenin klasörüne *görünümler/paylaşılan* klasör.

Açık *_Layout.cshtml* dosya ve (tamamlanan kodu aşağıda gösterilmektedir) aşağıdaki değişiklikleri yapın:

* Değiştir `@Styles.Render("~/Content/css")` ile bir `<link>` yüklemek için öğesi *bootstrap.css* (aşağıya bakın).

* Kaldırma `@Scripts.Render("~/bundles/modernizr")`.

* Out açıklama `@Html.Partial("_LoginPartial")` satır (satırıyla çevreleyen `@*...*@`). Sonraki öğreticide kendisine getireceğiz.

* Değiştir `@Scripts.Render("~/bundles/jquery")` ile bir `<script>` öğesi (aşağıya bakın).

* Değiştir `@Scripts.Render("~/bundles/bootstrap")` ile bir `<script>` öğesi (aşağıya bakın).

Değiştirme biçimlendirme önyükleme CSS eklemek için:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery ve önyükleme JavaScript ekleme için değiştirme biçimlendirme:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Güncelleştirilmiş *_Layout.cshtml* dosya aşağıda gösterilmektedir:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Site tarayıcıda görüntüleyin. Artık doğru yerde beklenen stilleri ile yüklenmesi gerekir.

* *İsteğe bağlı:* yeni düzen dosyasını kullanmayı denemek isteyebilirsiniz. Bu proje için Düzen dosyasından kopyalayabilirsiniz *FullAspNetCore* projesi. Yeni düzen dosyasını kullanan [etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve diğer geliştirmeler sahiptir.

## <a name="configure-bundling-and-minification"></a>Paketleme ve küçültme yapılandırın

Paketleme ve küçültme yapılandırma hakkında daha fazla bilgi için bkz: [paketleme ve küçültme](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>HTTP 500 hataları çözme

Sorunun kaynağını hakkında hiçbir bilgi içeren bir HTTP 500 hata iletisini neden olan birçok sorunları vardır. Örneğin, varsa *Views/_ViewImports.cshtml* dosya içeriyorsa, projede mevcut olmayan bir ad alanı, bir HTTP 500 hata iletisi alırsınız. Varsayılan olarak ASP.NET Core uygulamaları `UseDeveloperExceptionPage` uzantı eklenir `IApplicationBuilder` ve yapılandırma olduğunda yürütülen *geliştirme*. Bu aşağıdaki kodda ayrıntılı olarak açıklanmıştır:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core HTTP 500 hata yanıtları bir web uygulaması işlenmemiş özel durumlardan dönüştürür. Normalde, hata ayrıntıları sunucu ile ilgili olası hassas bilgilerin açığa çıkmasını önlemek için bu yanıtları dahil değildir. Bkz: **Geliştirici özel durum sayfasını kullanarak** içinde [işleme hataları](../fundamentals/error-handling.md) daha fazla bilgi için.

## <a name="additional-resources"></a>Ek kaynaklar

* [İstemci tarafı geliştirme](xref:client-side/index)
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
