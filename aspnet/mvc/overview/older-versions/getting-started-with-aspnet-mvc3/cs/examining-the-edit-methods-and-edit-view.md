---
uid: mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/examining-the-edit-methods-and-edit-view
title: Düzenleme görünümü (C#) ve düzenleme yöntemler inceleniyor | Microsoft Docs
author: Rick-Anderson
description: Bu öğretici Microsoft Visual Web Developer 2010 Express Service Pack olan 1, kullanarak bir ASP.NET MVC Web uygulaması oluşturmanın temellerini öğretmek...
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/12/2011
ms.topic: article
ms.assetid: 1d266bf0-a61e-423b-a3d2-13773d7dafe2
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-aspnet-mvc3/cs/examining-the-edit-methods-and-edit-view
msc.type: authoredcontent
ms.openlocfilehash: 581138bb25b95ef9002a2bd97d1fa28797d96bfa
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30875818"
---
<a name="examining-the-edit-methods-and-edit-view-c"></a>Düzenleme görünümü (C#) ve düzenleme yöntemler inceleniyor
====================
tarafından [Rick Anderson](https://github.com/Rick-Anderson)

> > [!NOTE]
> > Bu öğretici güncelleştirilmiş bir sürümü kullanılabilir [burada](../../../getting-started/introduction/getting-started.md) ASP.NET MVC 5 ve Visual Studio 2013'ü kullanır. Daha güvenli, izlemek çok daha kolaydır ve daha fazla özelliklerini gösterir.
> 
> 
> Bu öğretici Microsoft Visual Web Developer 2010 Express Service Pack ücretsiz Microsoft Visual Studio sürümünü olan 1, kullanarak bir ASP.NET MVC Web uygulaması oluşturmanın temellerini öğretmek. Başlamadan önce aşağıda listelenen önkoşulları kurduğunuzdan emin olun. Bunların tümünün aşağıdaki bağlantıyı tıklatarak yükleyin: [Web Platformu yükleyicisi](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Alternatif olarak, aşağıdaki bağlantıları kullanarak önkoşulları ayrı ayrı yükleyebilirsiniz:
> 
> - [Visual Studio Web Developer Express SP1 önkoşulları](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
> - [ASP.NET MVC 3 araçları güncelleştirme](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
> - [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(çalışma zamanı + araçları destekler)
> 
> Visual Web Developer 2010 yerine Visual Studio 2010 kullanıyorsanız, aşağıdaki bağlantıyı tıklatarak önkoşulları yükleyin: [Visual Studio 2010 önkoşulları](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).
> 
> C# kaynak kodu ile Visual Web Developer projesi bu konuya eşlik etmek kullanılabilir. [C# sürümü](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098). Visual Basic tercih ederseniz, geçiş [Visual Basic sürüm](../vb/intro-to-aspnet-mvc-3.md) Bu öğreticinin.


Bu bölümde, oluşturulan eylem yöntemleri ve film denetleyicisi için görünümleri inceleyeceğiz. Ardından özel arama sayfası ekleyeceksiniz.

Uygulamayı çalıştırın ve Gözat `Movies` ekleyerek denetleyicisi */Movies* tarayıcınızın adres çubuğundaki URL'ye. Fare işaretçisini tutun bir **Düzenle** bağlandığı URL'yi görmek için bağlantı.

[![EditLink_sm](examining-the-edit-methods-and-edit-view/_static/image2.png)](examining-the-edit-methods-and-edit-view/_static/image1.png)

**Düzenle** bağlantı tarafından üretilen `Html.ActionLink` yönteminde *Views\Movies\Index.cshtml* görünümü:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample1.cshtml)]

[![Html.ActionLink](examining-the-edit-methods-and-edit-view/_static/image4.png)](examining-the-edit-methods-and-edit-view/_static/image3.png)

`Html` Nesnesi üzerinde bir özelliği kullanılarak kullanıma sunulan bir yardımcı olan `WebViewPage` temel sınıfı. `ActionLink` Yardımcı yöntemini eylem yöntemlerine denetleyicilerde bağlantı HTML köprüler dinamik olarak oluşturulacak kolaylaştırır. İlk bağımsız değişken `ActionLink` yöntemdir işlemek için bağlantı metni (örneğin, `<a>Edit Me</a>`). İkinci bağımsız değişkeni çağrılacak eylem yöntemi adıdır. Son bağımsız değişken bir [anonim nesneyi](https://weblogs.asp.net/scottgu/archive/2007/05/15/new-orcas-language-feature-anonymous-types.aspx) (Bu durumda, 4 kimliği) rota verilerini oluşturur.

Önceki görüntüde gösterildiği oluşturulan bağlantı `http://localhost:xxxxx/Movies/Edit/4`. Varsayılan rota URL deseni alır `{controller}/{action}/{id}`. Bu nedenle, ASP.NET çevirir `http://localhost:xxxxx/Movies/Edit/4` bir istek içine `Edit` eylem yöntemi `Movies` parametresiyle denetleyicisi `ID` 4 eşittir.

Eylem yöntemi parametrelerini bir sorgu dizesi kullanarak da geçirebilirsiniz. Örneğin, URL `http://localhost:xxxxx/Movies/Edit?ID=4` parametresi de geçirir `ID` için 4'ün `Edit` eylem yöntemi `Movies` denetleyicisi.

[![EditQueryString](examining-the-edit-methods-and-edit-view/_static/image6.png)](examining-the-edit-methods-and-edit-view/_static/image5.png)

Açık `Movies` denetleyicisi. İki `Edit` eylem yöntemleri aşağıda gösterilmektedir.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample2.cs)]

İkinci fark `Edit` tarafından eylem yöntemi öncesinde `HttpPost` özniteliği. Bu özniteliği, bu aşırı yüklemesini belirtir `Edit` yöntemi yalnızca POST istekleri için çağrılan. Geçerli olabilir `HttpGet` ilk öznitelik Düzenle yöntemi, ancak bu gerekli değildir, varsayılan olduğundan. (Örtük olarak atanmış olan eylem yöntemlerine bakın `HttpGet` olarak özniteliği `HttpGet` yöntemlerini.)

`HttpGet` `Edit` Yöntemi film ID parametresi alır, Entity Framework kullanarak filmi arar `Find` yöntemi ve seçili film düzenleme görünümü döndürür. Yapı iskelesi sistem düzenleme görünümü oluşturduğunuzda, incelenmesi `Movie` sınıfı ve işlemek için oluşturulan kodu `<label>` ve `<input>` sınıfın her bir özellik için öğeleri. Aşağıdaki örnek, oluşturulan düzenleme görünümü gösterir:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample3.cshtml)]

Şablonu görüntüleme nasıl sahip fark bir `@model MvcMovie.Models.Movie` deyimini dosyanın üst — bu görünüm model türünde olmasını şablonu görüntüleme için beklediğini belirtir `Movie`.

İskele kurulmuş kodu birkaç kullanan *yardımcı yöntemler* HTML biçimlendirmesi kolaylaştırmak için. [ `Html.LabelFor` ](https://msdn.microsoft.com/library/gg401864(VS.98).aspx) Yardımcısı ("Title", "ReleaseDate", "Tarz" veya "Price") alan adını görüntüler. [ `Html.EditorFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.editorextensions.editorfor(VS.98).aspx) Yardımcı görüntüleyen bir HTML `<input>` öğesi. [ `Html.ValidationMessageFor` ](https://msdn.microsoft.com/library/system.web.mvc.html.validationextensions.validationmessagefor(VS.98).aspx) Yardımcısı bu özellik ile ilişkili herhangi bir doğrulama iletisi görüntüler.

Uygulamayı çalıştırın ve gidin */Movies* URL. Tıklatın bir **Düzenle** bağlantı. Tarayıcıda, sayfa için kaynağı görüntüleyin. HTML sayfasında, aşağıdaki gibi görünüyor. (Menü biçimlendirme daha anlaşılır olması için hariç tutuldu.)

[!code-html[Main](examining-the-edit-methods-and-edit-view/samples/sample4.html)]

`<input>` Öğeleridir bir HTML `<form>` öğesi, `action` özniteliği postalamak için ayarlanmış */filmler/düzenleme* URL. Form verileri sunucuya nakledilir zaman **Düzenle** düğmesine tıklandığında.

## <a name="processing-the-post-request"></a>POST isteği işleme

Aşağıdaki liste gösterildiği `HttpPost` sürümü `Edit` eylem yöntemi.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample5.cs)]

ASP.NET framework model bağlayıcı gönderilen form değerleri alır ve oluşturan bir `Movie` olarak geçirilen nesne `movie` parametresi. `ModelState.IsValid` Onay kodu doğrular biçiminde gönderilen veriler değiştirmek için kullanılabilir bir `Movie` nesnesi. Veriler geçerliyse, kod film verileri kaydeder `Movies` koleksiyonu `MovieDBContext` örneği. Kod sonra yeni film verileri veritabanına çağırarak kaydeder `SaveChanges` yöntemi `MovieDBContext`, değişiklikler veritabanına devam ettirir. Veriler kaydedildikten sonra kodu kullanıcı için yönlendiren `Index` eylem yöntemi `MoviesController` film listesinde görüntülenecek güncelleştirilmiş film neden sınıfı.

Gönderilen değerler geçerli değilse, formda yeniden görüntülenir. `Html.ValidationMessageFor` Yardımcıları içinde *Edit.cshtml* şablonu uygun hata iletilerini görüntüleme ilgilenebilmek görünümü.

[![abcNotValid](examining-the-edit-methods-and-edit-view/_static/image8.png)](examining-the-edit-methods-and-edit-view/_static/image7.png)

> **Yerel ayarlar hakkında not** normalde İngilizce dışındaki yerel ayar ile çalışıyorsanız, bkz: [İngilizce dışındaki yerel ayarlar ile ASP.NET MVC 3 doğrulama destekleme.](https://msdn.microsoft.com/library/gg674880(VS.98).aspx)


## <a name="making-the-edit-method-more-robust"></a>Düzenleme yöntemi daha sağlam hale getirme

`HttpGet` `Edit` Yapı iskelesi sistem tarafından oluşturulan değil çek yöntemi kendisine geçirilen Kimliğinin geçerli olduğundan emin. Bir kullanıcı kimliği kesimi URL'den kaldırır varsa (`http://localhost:xxxxx/Movies/Edit`), aşağıdaki hata görüntülenir:

[![Null_ID](examining-the-edit-methods-and-edit-view/_static/image10.png)](examining-the-edit-methods-and-edit-view/_static/image9.png)

Bir kullanıcı aynı zamanda veritabanında gibi mevcut olmayan bir kimliği geçirebilirdiniz `http://localhost:xxxxx/Movies/Edit/1234`. İki değişiklikler yapabilir `HttpGet` `Edit` bu sınırlamaya adres için eylem yöntemi. İlk olarak, değişiklik `ID` parametresini kullanarak bir kimliği açıkça geçirildiğinde değil sıfır varsayılan bir değere sahip. Ayrıca, kontrol edebilirsiniz `Find` yöntemi gerçekten şablonu görüntüleme Film nesnesini geri dönmeden önce film bulunamadı. Güncelleştirilmiş `Edit` yöntemi aşağıda gösterilmektedir.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample6.cs)]

Hiçbir film bulunursa, `HttpNotFound` yöntemi çağrılır.

Tüm `HttpGet` benzer bir desen yöntemleri uygulayın. Film nesnesini alın (veya durumunda nesnelerin listesini `Index`) ve görünüm model geçirin. `Create` Yöntemi bir boş film nesnesi oluşturma görünümüne geçirir. Bu nedenle oluşturmak, düzenlemek, silmek veya aksi halde verileri değiştirme tüm yöntemleri yapmak `HttpPost` yönteminin. Bir HTTP GET yöntemi verileri değiştirme olan bir güvenlik riski blog gönderisine girişi açıklandığı gibi [ASP.NET MVC ipucu #46 – güvenlik açıklarını oluşturduğundan bağlantılarını sil kullanmayan](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx). GET yöntemi verileri değiştirme de HTTP en iyi yöntemler ve GET istekleri uygulamanızın durumunu değiştirmemelisiniz belirtir mimari REST desenini ihlal ediyor. Diğer bir deyişle, bir GET işlemi gerçekleştirilirken hiçbir yan etkisi olan güvenli bir işlem olmalıdır.

## <a name="adding-a-search-method-and-search-view"></a>Search yöntemini ve arama görünümü ekleme

Bu bölümde, ekleyeceksiniz bir `SearchIndex` olanak sağlayan eylem yöntemi arama filmler Tarz veya adı. Bu kullanılabilir kullanarak olacaktır */filmler/SearchIndex* URL. İstek için bir filmi aramak için bir kullanıcı doldurabilirsiniz giriş öğeleri içeren bir HTML formuna görüntüler. Kullanıcı formu gönderdiğinde, eylem yöntemi kullanıcı tarafından gönderilen arama değerleri alır ve değerlerini veritabanını aramak için kullanın.

## <a name="displaying-the-searchindex-form"></a>SearchIndex Form görüntüleme

Başlangıç ekleyerek bir `SearchIndex` varolan eylem yönteminin `MoviesController` sınıfı. Bir HTML formu içeren bir görünüm yöntemi döndürür. Kod aşağıdaki gibidir:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample7.cs)]

İlk satırı `SearchIndex` yöntemi, aşağıdaki oluşturur [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) filmler seçmek için sorgu:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample8.cs)]

Sorgu, bu noktada tanımlandı ancak karşı veri deposu henüz çalıştırılmadı.

Varsa `searchString` parametre içeren bir dize, filmler sorgu aşağıdaki kodu kullanarak, arama dizesi değerini filtre şekilde değiştirilir:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample9.cs)]

LINQ sorgularını değil tanımlanmış olan veya ne zaman bir yöntemi çağrılarak değiştirildiğinde yürütülen `Where` veya `OrderBy`. Bunun yerine, sorgu yürütme, gerçekleşen değeri gerçekte üzerinden yinelendiğinde kadar bir ifadenin değerlendirmesine Gecikmeli yani ertelenir veya [ `ToList` ](https://msdn.microsoft.com/library/bb342261.aspx) yöntemi çağrılır. İçinde `SearchIndex` örnek, sorgu SearchIndex görünümünde yürütülür. Ertelenmiş sorgu yürütme hakkında daha fazla bilgi için bkz: [sorgu yürütme](https://msdn.microsoft.com/library/bb738633.aspx).

Uygulayabileceğiniz artık `SearchIndex` form kullanıcıya görüntüleyecek görünümü. İçinde sağ `SearchIndex` yöntemi ve ardından **Görünüm Ekle**. İçinde **Görünüm Ekle** iletişim kutusunda, geçirilecek başlatacağınız belirtin bir `Movie` nesne modeli sınıfı olarak görünüm şablon. İçinde **İskele şablonu** listesinde, seçin **listesi**, ardından **Ekle**.

[![AddSearchView](examining-the-edit-methods-and-edit-view/_static/image12.png)](examining-the-edit-methods-and-edit-view/_static/image11.png)

Tıkladığınızda **Ekle** düğmesini *Views\Movies\SearchIndex.cshtml* görünüm şablonu oluşturulur. Seçtiğiniz çünkü **listesi** içinde **İskele şablonu** listesinde, otomatik olarak oluşturulan Visual Web Developer (iskele kurulmuş) görünümünde bazı varsayılan içerik. Yapı iskelesi bir HTML formuna oluşturulur. Bunu incelenmesi `Movie` sınıfı ve işlemek için oluşturulan kodu `<label>` sınıfın her bir özellik için öğeleri. Listenin altında oluşturulan oluşturma görünüm gösterir:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample10.cshtml)]

Uygulamayı çalıştırın ve gidin */filmler/SearchIndex*. Bir sorgu dizesi gibi ekleme `?searchString=ghost` URL. Filtrelenmiş filmler görüntülenir.

[![SearchQryStr](examining-the-edit-methods-and-edit-view/_static/image14.png)](examining-the-edit-methods-and-edit-view/_static/image13.png)

İmzası değiştirirseniz `SearchIndex` adlı bir parametreye sahip yöntemi `id`, `id` parametresi eşleşir `{id}` varsayılan için yer tutucu yönlendirir kümesinde *Global.asax* dosya.

[!code-json[Main](examining-the-edit-methods-and-edit-view/samples/sample11.json)]

Değiştirilen `SearchIndex` yöntemi uygulamamız görünecektir gibi:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample12.cs)]

Bir sorgu dizesi değeri olarak rota verilerini (URL kesimi) yerine olarak arama başlık şimdi geçirebilirsiniz.

[![SearchRouteData](examining-the-edit-methods-and-edit-view/_static/image16.png)](examining-the-edit-methods-and-edit-view/_static/image15.png)

Ancak, bunlar bir filmi için arama yapmak istediğiniz her zaman URL'sini değiştirmek için kullanıcıların beklediğiniz olamaz. Size yardımcı olmak için kullanıcı Arabirimi ekleyeceksiniz artık bunu filmler filtre. İmzası değiştirdiyseniz `SearchIndex` rota bağlı ID parametresi geçirmek nasıl test etmek için yöntemini değiştirme geri böylece, `SearchIndex` yöntemi adlı bir dize parametresi alan `searchString`:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample13.cs)]

Açık *Views\Movies\SearchIndex.cshtml* dosya ve henüz sonra `@Html.ActionLink("Create New", "Create")`, aşağıdakileri ekleyin:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample14.cshtml)]

Aşağıdaki örnek, bir kısmı gösterir *Views\Movies\SearchIndex.cshtml* eklenen filtreleme biçimlendirme dosyasıyla.

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample15.cshtml)]

`Html.BeginForm` Yardımcı oluşturur açılış `<form>` etiketi. `Html.BeginForm` Yardımcı neden tıklayarak kullanıcı formu gönderdiğinde kendisine gönderme formun **filtre** düğmesi.

Uygulamayı çalıştırın ve bir filmi için arama yapmayı deneyin.

[![SearchIndxIE9_title](examining-the-edit-methods-and-edit-view/_static/image18.png)](examining-the-edit-methods-and-edit-view/_static/image17.png)

Var olan hiçbir `HttpPost` , aşırı `SearchIndex` yöntemi. Yöntemi uygulama durumunu değiştirme değil çünkü bu, yalnızca verileri filtreleme gerekmez.

Aşağıdaki ekleyebilirsiniz `HttpPost SearchIndex` yöntemi. Bu durumda, eylem Başlatıcısı eşleşir `HttpPost SearchIndex` yöntemi ve `HttpPost SearchIndex` yöntemi, aşağıdaki resimde gösterildiği gibi çalışıyor.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample16.cs)]

[![SearchPostGhost](examining-the-edit-methods-and-edit-view/_static/image20.png)](examining-the-edit-methods-and-edit-view/_static/image19.png)

Ancak, bu bile eklerseniz `HttpPost` sürümü `SearchIndex` yöntemini nasıl bu tüm uygulanmıştır içinde bir sınırlama yoktur. Belirli bir arama yer işareti koymak istediğiniz veya bunlar filmler aynı filtrelenmiş listesini görmek için tıklatabilirsiniz arkadaşlarınıza bağlantı göndermek istediğiniz düşünün. HTTP POST isteği için URL GET isteğini (localhost:xxxxx/filmler/SearchIndex) için URL ile aynıdır--URL arama bilgisi yok dikkat edin. Sağ şimdi, arama dizesi bilgileri sunucuya bir form alanı değerini gönderilir. Bu, yer işareti veya bir URL içinde arkadaş göndermek için arama bilgilerini yakalayamazsınız anlamına gelir.

Çözüm bir aşırı yüklemesini kullanmaktır `BeginForm` POST isteğini arama bilgilerini URL'sini eklemeniz gerekir ve bu olmalıdır belirtir HttpGet sürümüne yönlendirilen `SearchIndex` yöntemi. Parametresiz varolan `BeginForm` aşağıdaki yöntemiyle:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample17.cshtml)]

[![BeginFormPost_SM](examining-the-edit-methods-and-edit-view/_static/image22.png)](examining-the-edit-methods-and-edit-view/_static/image21.png)

Şimdi bir arama gönderdiğinizde, URL bir arama sorgu dizesi içeriyor. Arama da gider için `HttpGet SearchIndex` eylem yöntemi, varsa bile bir `HttpPost SearchIndex` yöntemi.

[![SearchIndexWithGetURL](examining-the-edit-methods-and-edit-view/_static/image24.png)](examining-the-edit-methods-and-edit-view/_static/image23.png)

## <a name="adding-search-by-genre"></a>Türe göre arama ekleme

Eklediyseniz `HttpPost` sürümü `SearchIndex` yöntemi, şimdi silin.

Ardından, filmler için türe göre arama kullanıcıların izin vermek için bir özellik ekleyeceksiniz. Değiştir `SearchIndex` aşağıdaki kod ile yöntemi:

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample18.cs)]

Bu sürümü `SearchIndex` yöntemi alır ek bir parametre öğesine `movieGenre`. İlk birkaç satır kod oluşturma bir `List` film türler veritabanından tutacak nesne.

Tüm türler veritabanından alır bir LINQ Sorgu kodudur.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample19.cs)]

Kod kullanan `AddRange` genel yöntemini `List` farklı türler listesine eklemek için koleksiyon. (Olmadan `Distinct` değiştiricisi, yinelenen türler eklenmesi — Örneğin, iki kez örneğimizde Komedi ekleneceği). Kod içinde türler listesi sonra depolar `ViewBag` nesnesi.

Aşağıdaki kod nasıl denetleneceğini gösterir `movieGenre` parametresi. Boş değilse kodu daha fazla seçili filmlere belirtilen Tarz sınırlamak için filmler sorgu kısıtlar.

[!code-csharp[Main](examining-the-edit-methods-and-edit-view/samples/sample20.cs)]

## <a name="adding-markup-to-the-searchindex-view-to-support-search-by-genre"></a>Türe göre arama desteklemek için SearchIndex görünümü biçimlendirme ekleme

Ekleme bir `Html.DropDownList` yardımcıya *Views\Movies\SearchIndex.cshtml* hemen önce dosya `TextBox` Yardımcısı. Tamamlanan biçimlendirme aşağıda gösterilmiştir:

[!code-cshtml[Main](examining-the-edit-methods-and-edit-view/samples/sample21.cshtml)]

Uygulamayı çalıştırın ve Gözat */filmler/SearchIndex*. Bir arama Tarz, film adı ve her iki ölçütlere göre deneyin.

Bu bölümde çerçevesi tarafından oluşturulan görünümleri ve CRUD eylem yöntemleri incelendi. Bir arama eylem yöntemi ve filmi ve türe göre arama, kullanıcıların izin görünüm oluşturduğunuz. Sonraki bölümde, yeni özellik eklemek nasıl göreceğiz `Movie` modeli ve otomatik olarak bir test veritabanı oluşturacak bir başlatıcı ekleme.

> [!div class="step-by-step"]
> [Önceki](accessing-your-models-data-from-a-controller.md)
> [sonraki](adding-a-new-field.md)
