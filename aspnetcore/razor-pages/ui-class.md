---
title: ASP.NET Core Sınıf Kitaplığı'nda yeniden kullanılabilir Razor UI
author: Rick-Anderson
description: Yeniden kullanılabilir Razor kısmi görünümler, ASP.NET Core sınıf kitaplığında kullanarak kullanıcı Arabirimi oluşturma açıklanır.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/24/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 96ef8fc055a6b92cd0808d02031d917b8446f305
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394752"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>ASP.NET Core Razor sınıf kitaplığı projesi kullanarak yeniden kullanılabilir kullanıcı Arabirimi oluşturma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor görünümleri, sayfalar, denetleyicileri, sayfa modelleri [Razor bileşenleri](xref:blazor/class-libraries), [görüntülemek bileşenleri](xref:mvc/views/view-components), ve veri modelleri bir Razor sınıf kitaplığı (RCL) derlenebilir. RCL paketlenir ve yeniden kullanılabilir. Uygulamalar RCL içerir ve görünümlere ve sayfalara içerdiği geçersiz kılar. Zaman görünümü, kısmi görünüm veya Razor sayfası hem web uygulaması hem de RCL Razor işaretlemesi bulunur ( *.cshtml* dosya) web uygulaması önceliklidir.

Bu özellik gerektirir [!INCLUDE[](~/includes/2.1-SDK.md)]

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Razor UI içeren bir sınıf kitaplığı oluşturma

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio'dan **dosya** menüsünde **yeni** > **proje**.
* Seçin **ASP.NET Core Web uygulaması**.
* (Örneğin, "RazorClassLib") kitaplığı adı > **Tamam**. Oluşturulan görünüm kitaplığı ile bir dosya adı çakışması önlemek için kitaplık adını bitmiyor olun `.Views`.
* Doğrulama **ASP.NET Core 2.1** veya daha sonra seçilir.
* Seçin **Razor sınıf kitaplığı** > **Tamam**.

Bir RCL aşağıdaki proje dosyası vardır:

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından çalıştırmak `dotnet new razorclasslib`. Örneğin:

```console
dotnet new razorclasslib -o RazorUIClassLib
```

Daha fazla bilgi için [yeni dotnet](/dotnet/core/tools/dotnet-new). Oluşturulan görünüm kitaplığı ile bir dosya adı çakışması önlemek için kitaplık adını bitmiyor olun `.Views`.

---

Razor dosyaları için RCL ekleyin.

ASP.NET Core şablonları RCL içeriği olduğu varsayılır *alanları* klasör. Bkz: [RCL sayfa düzeni](#afs) kullanıma sunan bir RCL içeriği oluşturmak için `~/Pages` yerine `~/Areas/Pages`.

## <a name="referencing-rcl-content"></a>Başvuru RCL içeriği

RCL tarafından başvurulabilir:

* NuGet paketi. Bkz: [oluşturma NuGet paketlerini](/nuget/create-packages/creating-a-package) ve [dotnet paketini ekleyin](/dotnet/core/tools/dotnet-add-package) ve [oluştur ve NuGet paket yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName} .csproj*. Bkz: [dotnet-Başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>İzlenecek yol: Bir RCL projesi oluşturun ve bir Razor sayfaları projeden kullanın

İndirebileceğiniz [tam proje](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ve yerine oluşturma test edin. Örnek indirme, ek kod ve test etmek proje kolaylaştırmak bağlantılar içerir. Geri bildirim bırakabilirsiniz [bu GitHub sorunu](https://github.com/aspnet/AspNetCore.Docs/issues/6098) yorumlarınızı üzerinde adım adım yönergeler ve örnekleri indirme ile.

### <a name="test-the-download-app"></a>İndirme uygulamayı test etme

Tamamlanmış uygulamayı karşıdan henüz ve bunun yerine izlenecek projesi oluşturacak, atlamak [sonraki bölümde](#create-an-rcl).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Açık *.sln* dosyasını Visual Studio'da. Uygulamayı çalıştırın.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Bir komut isteminden *CLI* dizin RCL oluşturun ve web uygulaması.

```console
dotnet build
```

Taşı *WebApp1* dizin ve uygulamayı çalıştırın:

```console
dotnet run
```

---

Bölümündeki yönergeleri [Test WebApp1](#test)

## <a name="create-an-rcl"></a>Bir RCL oluşturma

Bu bölümde, bir RCL oluşturulur. Razor dosyaları için RCL eklenir.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

RCL projesi oluşturun:

* Visual Studio'dan **dosya** menüsünde **yeni** > **proje**.
* Seçin **ASP.NET Core Web uygulaması**.
* Uygulamayı adlandırma **RazorUIClassLib** > **Tamam**.
* Doğrulama **ASP.NET Core 2.1** veya daha sonra seçilir.
* Seçin **Razor sınıf kitaplığı** > **Tamam**.
* Razor kısmi Görünüm adlı bir dosya ekleyin *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Komut satırından şu komutu çalıştırın:

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Yukarıdaki komutlar:

* Oluşturur `RazorUIClassLib` RCL.
* Bir Razor il_eti sayfası oluşturur ve için RCL ekler. `-np` Parametresi olmadan sayfa oluşturur bir `PageModel`.
* Oluşturur bir [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) dosya ve için RCL ekler.

*_ViewStart.cshtml* dosyası (sonraki bölümde eklenir) Razor sayfaları proje düzenini kullanmak için gereklidir.

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Razor dosyaları ve klasörleri projeye ekleyin.

* Biçimlendirmeyi Değiştir *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* aşağıdaki kod ile:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Biçimlendirmeyi Değiştir *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* aşağıdaki kod ile:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` Kısmi görünümü kullanmak için gereklidir (`<partial name="_Message" />`). Dahil olmak üzere yerine `@addTagHelper` yönergesi ekleyebileceğiniz bir *_viewımports.cshtml* dosya. Örneğin:

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

Daha fazla bilgi için *_viewımports.cshtml*, bkz: [paylaşılan yönergeleri alma](xref:mvc/views/layout#importing-shared-directives)

* Derleyici hata doğrulamak için sınıf kitaplığı derleme:

```console
dotnet build RazorUIClassLib
```

Derleme çıktısını içeren *RazorUIClassLib.dll* ve *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* derlenmiş Razor içeriği.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Bir Razor sayfaları projeden Razor kullanıcı Arabirimi kitaplığını kullanma

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor sayfaları web uygulaması oluşturun:

* Gelen **Çözüm Gezgini**, çözüme sağ tıklayın > **Ekle** >  **yeni proje**.
* Seçin **ASP.NET Core Web uygulaması**.
* Uygulamayı adlandırma **WebApp1**.
* Doğrulama **ASP.NET Core 2.1** veya daha sonra seçilir.
* Seçin **Web uygulaması** > **Tamam**.

* Gelen **Çözüm Gezgini**, sağ **WebApp1** seçip **başlangıç projesi olarak ayarla**.
* Gelen **Çözüm Gezgini**, sağ **WebApp1** seçip **yapı bağımlılıkları** > **proje bağımlılıkları**.
* Denetleme **RazorUIClassLib** bağımlılık olarak **WebApp1**.
* Gelen **Çözüm Gezgini**, sağ **WebApp1** seçip **Ekle** > **başvuru**.
* İçinde **başvuru Yöneticisi** iletişim kutusunda, onay **RazorUIClassLib** > **Tamam**.

Uygulamayı çalıştırın.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Razor sayfaları web uygulaması ve Razor sayfaları uygulama ve RCL içeren bir çözüm dosyası oluşturun:

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Web uygulaması derleyebilir ve:

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>Test WebApp1

Razor Arabirimi sınıf kitaplığını kullanımda olmadığını doğrulayın:

* konumuna gözatın `/MyFeature/Page1`.

## <a name="override-views-partial-views-and-pages"></a>Görünümleri, kısmi görünümleri ve sayfa geçersiz kıl

Zaman görünümü, kısmi görünüm veya Razor sayfası hem web uygulaması hem de RCL Razor işaretlemesi bulunur ( *.cshtml* dosya) web uygulaması önceliklidir. Örneğin, ekleme *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* WebApp1 için ve Sayfa1 WebApp1 içinde önceliklidir Sayfa1 RCL içinde.

Örnek indirme Yeniden Adlandır *WebApp1/alanlar/MyFeature2* için *WebApp1/alanlar/MyFeature* öncelik test etmek için.

Kopyalama *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* kısmi görünüme *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Yeni bir konum belirtmek için işaretleme güncelleştirin. Derleme ve uygulamanın sürümünü kısmi kullanılan doğrulamak için uygulamayı çalıştırın.

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>RCL sayfa düzeni

RCL içerik web uygulaması'nın bir parçasıdır ancak başvuru *sayfaları* klasörü, aşağıdaki dosya yapısı ile RCL projesi oluşturun:

* *RazorUIClassLib/sayfaları*
* *RazorUIClassLib/sayfalar/paylaşılan*

Varsayalım *RazorUIClassLib/sayfaları/paylaşılan* iki kısmi dosyaları içerir: *_Header.cshtml* ve *_Footer.cshtml*. `<partial>` Etiketleri için eklenebiliyordu *_Layout.cshtml* dosyası:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Bir RCL ile statik varlıkları oluşturma

Bir RCL RCL kullanan uygulama tarafından başvurulan statik varlıklar Yardımcısı gerektirebilir. ASP.NET Core, kullanan bir uygulama için kullanılabilir olan statik varlıkları içeren RCLs oluşturulmasını sağlar.

Yardımcı varlıklar bir RCL bir parçası olarak dahil etmek için oluşturma bir *wwwroot* sınıf kitaplığı klasöründe ve tüm gerekli dosyaları bu klasörde içerir.

Bir RCL paketleme, tüm varlıkları Yahoo! companion *wwwroot* klasörü paketine otomatik olarak dahil edilen ve bu paketi uygulamaları için kullanılabilir hale getirilir.

### <a name="consume-content-from-a-referenced-rcl"></a>Başvurulan RCL ait içerikleri kullanabilirsiniz

Eklenen dosyalar *wwwroot* RCL klasörü altında önek kullanan uygulamaya sunulur `_content/{LIBRARY NAME}/`. `{LIBRARY NAME}` Kitaplığı proje adı dönüştürülür nokta ile küçük harf (`.`) kaldırıldı. Örneğin, adında bir kitaplığı *Razor.Class.Lib* statik içeriği bir yolda sonuçlarını `_content/razorclasslib/`.

Kitaplıkla sağlanan statik varlıkları kullanan uygulama başvuran `<script>`, `<style>`, `<img>`ve diğer HTML etiketleri. Alıcı uygulamasına sahip olmanız gerekir [statik dosya desteği](xref:fundamentals/static-files) etkin.

### <a name="multi-project-development-flow"></a>Birden çok proje geliştirme akış

Kullanıcı uygulamayı çalıştırdığında:

* Varlıkları RCL özgün konumlarında kalır. Varlıkları kullanan uygulamaya taşınabilir değildir.
* RCL'ın içinde herhangi bir değişiklik *wwwroot* klasör RCL yeniden oluşturulduktan sonra kullanan uygulamada ve alıcı uygulamanın yeniden olmadan yansıtılır.

RCL oluşturulduğunda, statik web varlık konumlarını tanımlayan bir bildirim oluşturulur. Tüketim uygulama bildirimi, başvurulan projeler ve paketleri varlıklarından kullanmak için çalışma zamanında okur. Yeni bir varlık için bir RCL eklendiğinde, yeni varlık kullanan bir uygulamaya erişmeniz bildirimi güncelleştirilecek RCL yeniden oluşturulması gerekir.

### <a name="publish"></a>Yayımlama

Uygulama yayınlandıktan sonra başvuruda bulunulan tüm projelerin ve paketleri Yardımcısı varlıklarından içine kopyalanır *wwwroot* klasörü altında yayımlanan uygulamanın `_content/{LIBRARY NAME}/`.
