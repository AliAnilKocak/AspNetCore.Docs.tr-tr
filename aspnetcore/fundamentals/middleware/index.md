---
title: ASP.NET Core ara yazılımı
author: rick-anderson
description: ASP.NET Core ara yazılımı ve istek işlem hattı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 5c8e9e58ab222e482ef029f5099d0a8acd07d8a6
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972027"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Core ara yazılımı

::: moniker range=">= aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)

Ara yazılım, istekleri ve yanıtları işlemek için bir uygulama ardışık düzenine çevrilmiş yazılımdır. Her bileşen:

* İsteğin işlem hattında sonraki bileşene geçirilip geçemeyeceğini seçer.
* İşlem hattındaki sonraki bileşenden önce ve sonra iş gerçekleştirebilir.

İstek işlem hattını oluşturmak için istek temsilcileri kullanılır. İstek temsilcileri her HTTP isteğini işler.

İstek temsilcileri <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>ve <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> uzantı yöntemleri kullanılarak yapılandırılır. Tek bir istek temsilcisi, bir anonim Yöntem (çevrimiçi ara yazılım olarak adlandırılır) olarak satır içinde belirtilebilir veya yeniden kullanılabilir bir sınıfta tanımlanabilir. Bu yeniden kullanılabilir sınıflar ve satır içi anonim yöntemler, *Ara yazılım bileşenleri*olarak da adlandırılan *ara yazılımlar*. İstek ardışık düzeninde bulunan her bir ara yazılım bileşeni, işlem hattındaki bir sonraki bileşeni çağırmaktan veya işlem hattının kısa süreli olarak sağlanmasından sorumludur. Bir ara yazılım kısa devre dışı bırakıldığında, bu, diğer ara yazılımların isteği işlemesini önlediği için *Terminal ara yazılımı* olarak adlandırılır.

<xref:migration/http-modules>, ASP.NET Core ve ASP.NET 4. x içindeki istek işlem hatları arasındaki farkı açıklar ve ek ara yazılım örnekleri sağlar.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma

ASP.NET Core isteği ardışık düzeni, bir dizi istekten oluşur ve bunlardan sonra çağırılır. Aşağıdaki diyagramda kavram gösterilmektedir. Yürütmenin iş parçacığı siyah okları izler.

![İsteğin geliş, üç middlewares üzerinden işleme ve uygulamayı bırakma yanıtı gösteren istek işleme deseninin. Her bir ara yazılım mantığını çalıştırır ve sonraki () deyimindeki bir sonraki ara yazılım için isteği kapatır. Üçüncü ara yazılım isteği işledikten sonra, istek bir sonraki iki middlewares üzerinden geri geçirilir ve sonra, uygulamayı istemciye yanıt olarak bırakmadan önce Next () deyimlerinden sonra ek işleme için ters sırada geri geçirilir.](index/_static/request-delegate-pipeline.png)

Her temsilci bir sonraki temsilciden önce ve sonra işlemleri gerçekleştirebilir. Özel durum işleme temsilcileri işlem hattında erken çağrılmalıdır, bu sayede işlem hattının sonraki aşamalarında oluşan özel durumları yakalayabilirler.

Mümkün olan en basit ASP.NET Core uygulaması, tüm istekleri işleyen tek bir istek temsilcisi kurar. Bu durum gerçek bir istek işlem hattı içermez. Bunun yerine, her HTTP isteğine yanıt olarak tek bir anonim işlev çağırılır.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Birden çok istek temsilciyi <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>ile birlikte zinciri. `next` parametresi, ardışık düzendeki bir sonraki temsilciyi temsil eder. Ardışık düzen, *sonraki* *parametreyi çağırarak işlem* hattı için kısa devre dışı bırakabilirsiniz. Aşağıdaki örnekte gösterildiği gibi genellikle sonraki temsilciden önce ve sonra eylemler gerçekleştirebilirsiniz:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Bir temsilci bir sonraki temsilciye bir istek iletmezse, *istek ardışık düzenini, kısa*devre olarak gerçekleştirmektir. Gereksiz çalışmayı önlediği için kısa devre, genellikle tercih edilir. Örneğin, [statik dosya ara yazılımı](xref:fundamentals/static-files) , bir statik dosya için bir isteği işleyerek ve işlem hattının geri kalanını gerçekleştirerek bir *Terminal ara yazılımı* görevi görebilir. Daha fazla işlemeyi sonlandıran ara yazılım, `next.Invoke` deyimlerinden sonra kodu işlerken işlem hattına eklenen ara yazılımlar. Ancak, zaten gönderilmiş bir yanıta yazma girişimi hakkında aşağıdaki uyarıya bakın.

> [!WARNING]
> İstemciye yanıt gönderildikten sonra `next.Invoke` çağırmayın. Yanıt başladıktan sonra <xref:Microsoft.AspNetCore.Http.HttpResponse> değişiklikler özel durum oluşturur. Örneğin, üstbilgileri ayarlama ve durum kodu gibi değişiklikler özel durum oluşturur. `next`çağrıldıktan sonra yanıt gövdesine yazma:
>
> * Protokol ihlaline neden olabilir. Örneğin, belirtilen `Content-Length`daha fazla yazma.
> * Gövde biçimi bozulabilir. Örneğin, bir CSS dosyasına bir HTML altbilgisi yazma.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>, üstbilgilerin gönderilip gönderilmediğini veya gövdenin yazıldığını göstermek için faydalı bir ipucu.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> temsilciler bir `next` parametresi almaz. İlk `Run` temsilci her zaman terminaldir ve ardışık düzeni sonlandırır. `Run` bir kuraldır. Bazı ara yazılım bileşenleri, işlem hattının sonunda çalışan `Run[Middleware]` Yöntemler sunabilir:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]

Yukarıdaki örnekte, `Run` temsilci yanıta `"Hello from 2nd delegate."` yazar ve ardından işlem hattını sonlandırır. `Run` temsilcisinden sonra başka bir `Use` veya `Run` temsilcisi eklenirse, çağrılmaz.

<a name="order"></a>

## <a name="middleware-order"></a>Ara yazılım sırası

Ara yazılım bileşenlerinin `Startup.Configure` yöntemi içinde eklendiği sıra, ara yazılım bileşenlerinin isteklerde çağrıldığı sırayı ve yanıtın ters sırasını tanımlar. Sıra, güvenlik, performans ve işlevsellik açısından **önemlidir** .

Aşağıdaki `Startup.Configure` yöntemi, güvenlikle ilgili ara yazılım bileşenlerini önerilen sırayla ekler:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

Önceki kodda:

* [Bireysel kullanıcılar hesaplarıyla](xref:security/authentication/identity) yeni bir Web uygulaması oluştururken eklenmemiş olan ara yazılım, yorum yapılır.
* Her ara yazılımın bu tam sıra, ancak birçok do olması gerekmez. Örneğin, `UseCors`, `UseAuthentication`ve `UseAuthorization` gösterilen sırayla gelmelidir.

Aşağıdaki `Startup.Configure` yöntemi, genel uygulama senaryoları için ara yazılım bileşenleri ekler:

1. Özel durum/hata işleme
   * Uygulama geliştirme ortamında çalıştığında:
     * Geliştirici özel durum sayfası ara yazılımı (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) uygulama çalışma zamanı hatalarını raporlar.
     * Veritabanı hata sayfası ara yazılımı veritabanı çalışma zamanı hatalarını raporlar.
   * Uygulama, üretim ortamında çalıştığında:
     * Özel durum Işleyici ara yazılımı (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) aşağıdaki middlewares oluşturulan özel durumları yakalar.
     * HTTP katı aktarım güvenliği Protokolü (HSTS) ara yazılımı (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) `Strict-Transport-Security` üstbilgisini ekler.
1. HTTPS yeniden yönlendirme ara yazılımı (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) HTTP isteklerini HTTPS 'ye yönlendirir.
1. Statik dosya ara yazılımı (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) statik dosyaları ve kısa devre dışı istek işlemeyi döndürür.
1. Tanımlama bilgisi Ilkesi ara yazılımı (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) uygulamayı AB Genel Veri Koruma Yönetmeliği (GDPR) düzenlemelerine uyar.
1. İstekleri yönlendirmek için ara yazılım (`UseRouting`).
1. Kimlik doğrulama ara yazılımı (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), güvenli kaynaklara erişim izni vermeden önce kullanıcının kimliğini doğrulamaya çalışır.
1. Yetkilendirme ara yazılımı (`UseAuthorization`), bir kullanıcıya güvenli kaynaklara erişim yetkisi verir.
1. Oturum ara yazılımı (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) oturum durumunu oluşturur ve korur. Uygulama oturum durumunu kullanıyorsa, tanımlama bilgisi Ilkesi ara yazılımı ve MVC ara yazılımı öncesinde oturum ara yazılımını çağırın.
1. İstek ardışık düzenine Razor Pages uç noktaları eklemek için uç nokta yönlendirme ara yazılımı (`MapRazorPages``UseEndpoints`).

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Yukarıdaki örnek kodda, her bir ara yazılım uzantısı yöntemi <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> ad alanı aracılığıyla <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> gösterilir.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>, işlem hattına eklenen ilk ara yazılım bileşenidir. Bu nedenle, özel durum Işleyicisi ara yazılımı sonraki çağrılarında oluşan tüm özel durumları yakalar.

Statik dosya ara yazılımı, geri kalan bileşenlere geçmeden istekleri ve kısa devre dışı bırakabilirsiniz. bu sayede işlem hattının başlarında çağrılır. Statik dosya ara **yazılımı yetkilendirme denetimleri sağlamaz.** *Wwwroot*altındakiler de dahil olmak üzere statik dosya ara yazılımı tarafından sunulan tüm dosyalar herkese açık bir şekilde sunulur. Statik dosyaların güvenliğini sağlamaya yönelik bir yaklaşım için bkz. <xref:fundamentals/static-files>.

İstek statik dosya ara yazılımı tarafından işlenmemişse, kimlik doğrulaması yapan kimlik doğrulama ara yazılım (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) üzerinden geçirilir. Kimlik doğrulaması kısa devre dışı kimliği doğrulanmamış istekler değildir. Kimlik doğrulama ara yazılımı isteklerin kimliğini doğrulayabilse de, yetkilendirme (ve reddetme) yalnızca MVC, belirli bir Razor sayfası veya MVC denetleyicisi ve eylemi seçerse oluşur.

Aşağıdaki örnek, yanıt sıkıştırma ara yazılımı ile önce statik dosya isteklerinin statik dosya ara yazılımı tarafından işlendiği bir ara yazılım sırasını gösterir. Statik dosyalar bu ara yazılım sırasıyla sıkıştırılmaz. Razor Pages yanıtları sıkıştırılabilirler.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

## <a name="branch-the-middleware-pipeline"></a>Ara yazılım ardışık düzenini dallandırma

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> uzantıları, işlem hattının dallanması için bir kural olarak kullanılır. `Map`, istek ardışık düzenini, verilen istek yolunun eşleşenleri temelinde dallandırır. İstek yolu verilen yol ile başlıyorsa, dal yürütülür.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

Aşağıdaki tabloda, önceki kodu kullanarak `http://localhost:1234` gelen istekler ve yanıtlar gösterilmektedir.

| İstek             | Yanıt                     |
| ------------------- | ---------------------------- |
| localhost: 1234      | Eşleme olmayan temsilciden Merhaba. |
| localhost: 1234/Map1 | Eşleme testi 1                   |
| localhost: 1234/MAP2 | Eşleme testi 2                   |
| localhost: 1234/map3 | Eşleme olmayan temsilciden Merhaba. |

`Map` kullanıldığında, eşleşen yol kesimleri `HttpRequest.Path` kaldırılır ve her istek için `HttpRequest.PathBase` eklenir.

`Map` iç içe geçirmeyi destekler, örneğin:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` aynı anda birden çok kesimde eşleşir:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, belirtilen koşulun sonucuna göre istek ardışık düzenini dallandırır. `Func<HttpContext, bool>` türündeki herhangi bir koşul, istekleri işlem hattının yeni bir dalına eşlemek için kullanılabilir. Aşağıdaki örnekte, bir sorgu dizesi değişkeninin varlığını algılamak için bir koşul kullanılır `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

Aşağıdaki tabloda, önceki kodu kullanarak `http://localhost:1234` gelen istekler ve yanıtlar gösterilmektedir:

| İstek                       | Yanıt                     |
| ----------------------------- | ---------------------------- |
| localhost: 1234                | Eşleme olmayan temsilciden Merhaba. |
| localhost: 1234/? dalı = ana | Kullanılan dal = ana         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> Ayrıca, belirtilen koşulun sonucuna göre istek ardışık düzenini dallandırır. `MapWhen`aksine, bu dal, kısa devre veya bir Terminal ara yazılımı içeriyorsa ana işlem hattına yeniden katılır:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=23-24)]

Yukarıdaki örnekte, "ana ardışık düzen üzerinden Merhaba" yanıtı. Tüm istekler için yazılmıştır. İstek bir sorgu dizesi değişkeni içeriyorsa `branch`, ana işlem hattı yeniden birleştirilmadan önce değeri günlüğe kaydedilir.

## <a name="built-in-middleware"></a>Yerleşik ara yazılım

ASP.NET Core aşağıdaki ara yazılım bileşenleriyle birlikte gönderilir. *Order* sütunu, istek işleme ardışık düzeninde ara yazılım yerleştirme ve ara yazılımın istek işlemeyi sonlandırabilecekleri koşullar bölümünde notlar sağlar. Bir ara yazılım, istek işlem hattının ne kadar kısa süreli olduğunu ve daha fazla aşağı akış ara yazılımı bir isteği işlemesini engelliyorsa, bu, *Terminal ara yazılımı*olarak adlandırılır. Kısa devre oluşturma hakkında daha fazla bilgi için, [IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma](#create-a-middleware-pipeline-with-iapplicationbuilder) bölümüne bakın.

| Ara yazılım | Açıklama | Sipariş verme |
| ---------- | ----------- | ----- |
| [Kimlik Doğrulaması](xref:security/authentication/identity) | Kimlik doğrulama desteği sağlar. | `HttpContext.User` önce. OAuth geri çağırmaları için Terminal. |
| [Yetkilendirme](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | Yetkilendirme desteği sağlar. | Kimlik doğrulama ara yazılımı hemen sonrasında. |
| [Tanımlama bilgisi Ilkesi](xref:security/gdpr) | Kişisel bilgileri depolamak için kullanıcılardan onay izler ve `secure` ve `SameSite`gibi tanımlama bilgisi alanları için en düşük standartları uygular. | Tanımlama bilgilerini veren ara yazılım öncesi. Örnekler: Authentication, Session, MVC (TempData). |
| [CORS](xref:security/cors) | Çıkış noktaları arası kaynak paylaşımını yapılandırır. | CORS kullanan bileşenlerden önce. |
| [Tanılama](xref:fundamentals/error-handling) | Geliştirici özel durum sayfası, özel durum işleme, durum kodu sayfaları ve yeni uygulamalar için varsayılan Web sayfası sağlayan çeşitli ayrı middlewares. | Hata oluşturan bileşenlerden önce. Özel durumlar için Terminal veya yeni uygulamalar için varsayılan Web sayfasına hizmet sunma. |
| [İletilen üstbilgiler](xref:host-and-deploy/proxy-load-balancer) | Proxy üst bilgilerini geçerli istek üzerine iletir. | Güncelleştirilmiş alanları kullanan bileşenlerden önce. Örnekler: Scheme, Host, istemci IP, yöntem. |
| [Sistem durumu denetimi](xref:host-and-deploy/health-checks) | ASP.NET Core uygulamasının sistem durumunu ve bağımlılıklarını denetler (örneğin, veritabanı kullanılabilirliğini denetleme). | Bir istek bir sistem durumu denetimi uç noktasıyla eşleşiyorsa Terminal. |
| [Üst bilgi yayma](xref:fundamentals/http-requests#header-propagation-middleware) | Gelen istekten gelen HTTP üstbilgilerini giden HTTP Istemci isteklerine yayar. |
| [HTTP yöntemini geçersiz kılma](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Gelen POST isteğinin yöntemi geçersiz kılmasına izin verir. | Güncelleştirilmiş yöntemini kullanan bileşenlerden önce. |
| [HTTPS yönlendirmesi](xref:security/enforcing-ssl#require-https) | Tüm HTTP isteklerini HTTPS 'ye yeniden yönlendirin. | URL 'YI kullanan bileşenlerden önce. |
| [HTTP katı taşıma güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Özel bir yanıt üst bilgisi ekleyen güvenlik geliştirme ara yazılımı. | Yanıtlar gönderilmeden önce ve istekleri değiştiren bileşenler. Örnekler: Iletilen üstbilgiler, URL yeniden yazma. |
| [MVC](xref:mvc/overview) | MVC/Razor Pages ile istekleri işler. | Bir istek bir rota ile eşleşiyorsa Terminal. |
| [OWıN](xref:fundamentals/owin) | OWIN tabanlı uygulamalar, sunucular ve ara yazılım ile birlikte çalışma. | OWıN ara yazılımı isteği tam olarak işliyorsa Terminal. |
| [Yanıtları Önbelleğe Alma](xref:performance/caching/middleware) | Yanıtları önbelleğe almak için destek sağlar. | Önbelleğe alma gerektiren bileşenlerden önce. |
| [Yanıt sıkıştırması](xref:performance/response-compression) | Yanıtları sıkıştırmak için destek sağlar. | Sıkıştırma gerektiren bileşenlerden önce. |
| [Yerelleştirme iste](xref:fundamentals/localization) | Yerelleştirme desteği sağlar. | Yerelleştirmenin önemli bileşenlerinden önce. |
| [Uç nokta yönlendirme](xref:fundamentals/routing) | İstek yollarını tanımlar ve kısıtlar. | Eşleşen yolların terminali. |
| [Oturum](xref:fundamentals/app-state) | Kullanıcı oturumlarını yönetmek için destek sağlar. | Oturum gerektiren bileşenlerden önce. |
| [Statik dosyalar](xref:fundamentals/static-files) | Statik dosyaları ve dizin taramayı sunma desteği sağlar. | Bir istek bir dosyayla eşleşiyorsa Terminal. |
| [URL yeniden yazma](xref:fundamentals/url-rewriting) | URL 'Leri yeniden yazma ve istekleri yeniden yönlendirme desteği sağlar. | URL 'YI kullanan bileşenlerden önce. |
| [WebSockets](xref:fundamentals/websockets) | WebSockets protokolünü etkinleştirilir. | WebSocket isteklerini kabul etmek için gereken bileşenlerden önce. |

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)

Ara yazılım, istekleri ve yanıtları işlemek için bir uygulama ardışık düzenine çevrilmiş yazılımdır. Her bileşen:

* İsteğin işlem hattında sonraki bileşene geçirilip geçemeyeceğini seçer.
* İşlem hattındaki sonraki bileşenden önce ve sonra iş gerçekleştirebilir.

İstek işlem hattını oluşturmak için istek temsilcileri kullanılır. İstek temsilcileri her HTTP isteğini işler.

İstek temsilcileri <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>ve <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> uzantı yöntemleri kullanılarak yapılandırılır. Tek bir istek temsilcisi, bir anonim Yöntem (çevrimiçi ara yazılım olarak adlandırılır) olarak satır içinde belirtilebilir veya yeniden kullanılabilir bir sınıfta tanımlanabilir. Bu yeniden kullanılabilir sınıflar ve satır içi anonim yöntemler, *Ara yazılım bileşenleri*olarak da adlandırılan *ara yazılımlar*. İstek ardışık düzeninde bulunan her bir ara yazılım bileşeni, işlem hattındaki bir sonraki bileşeni çağırmaktan veya işlem hattının kısa süreli olarak sağlanmasından sorumludur. Bir ara yazılım kısa devre dışı bırakıldığında, bu, diğer ara yazılımların isteği işlemesini önlediği için *Terminal ara yazılımı* olarak adlandırılır.

<xref:migration/http-modules>, ASP.NET Core ve ASP.NET 4. x içindeki istek işlem hatları arasındaki farkı açıklar ve ek ara yazılım örnekleri sağlar.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma

ASP.NET Core isteği ardışık düzeni, bir dizi istekten oluşur ve bunlardan sonra çağırılır. Aşağıdaki diyagramda kavram gösterilmektedir. Yürütmenin iş parçacığı siyah okları izler.

![İsteğin geliş, üç middlewares üzerinden işleme ve uygulamayı bırakma yanıtı gösteren istek işleme deseninin. Her bir ara yazılım mantığını çalıştırır ve sonraki () deyimindeki bir sonraki ara yazılım için isteği kapatır. Üçüncü ara yazılım isteği işledikten sonra, istek bir sonraki iki middlewares üzerinden geri geçirilir ve sonra, uygulamayı istemciye yanıt olarak bırakmadan önce Next () deyimlerinden sonra ek işleme için ters sırada geri geçirilir.](index/_static/request-delegate-pipeline.png)

Her temsilci bir sonraki temsilciden önce ve sonra işlemleri gerçekleştirebilir. Özel durum işleme temsilcileri işlem hattında erken çağrılmalıdır, bu sayede işlem hattının sonraki aşamalarında oluşan özel durumları yakalayabilirler.

Mümkün olan en basit ASP.NET Core uygulaması, tüm istekleri işleyen tek bir istek temsilcisi kurar. Bu durum gerçek bir istek işlem hattı içermez. Bunun yerine, her HTTP isteğine yanıt olarak tek bir anonim işlev çağırılır.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

İlk <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> temsilci, işlem hattını sonlandırır.

Birden çok istek temsilciyi <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>ile birlikte zinciri. `next` parametresi, ardışık düzendeki bir sonraki temsilciyi temsil eder. Ardışık düzen, *sonraki* *parametreyi çağırarak işlem* hattı için kısa devre dışı bırakabilirsiniz. Aşağıdaki örnekte gösterildiği gibi genellikle sonraki temsilciden önce ve sonra eylemler gerçekleştirebilirsiniz:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Bir temsilci bir sonraki temsilciye bir istek iletmezse, *istek ardışık düzenini, kısa*devre olarak gerçekleştirmektir. Gereksiz çalışmayı önlediği için kısa devre, genellikle tercih edilir. Örneğin, [statik dosya ara yazılımı](xref:fundamentals/static-files) , bir statik dosya için bir isteği işleyerek ve işlem hattının geri kalanını gerçekleştirerek bir *Terminal ara yazılımı* görevi görebilir. Daha fazla işlemeyi sonlandıran ara yazılım, `next.Invoke` deyimlerinden sonra kodu işlerken işlem hattına eklenen ara yazılımlar. Ancak, zaten gönderilmiş bir yanıta yazma girişimi hakkında aşağıdaki uyarıya bakın.

> [!WARNING]
> İstemciye yanıt gönderildikten sonra `next.Invoke` çağırmayın. Yanıt başladıktan sonra <xref:Microsoft.AspNetCore.Http.HttpResponse> değişiklikler özel durum oluşturur. Örneğin, üstbilgileri ayarlama ve durum kodu gibi değişiklikler özel durum oluşturur. `next`çağrıldıktan sonra yanıt gövdesine yazma:
>
> * Protokol ihlaline neden olabilir. Örneğin, belirtilen `Content-Length`daha fazla yazma.
> * Gövde biçimi bozulabilir. Örneğin, bir CSS dosyasına bir HTML altbilgisi yazma.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>, üstbilgilerin gönderilip gönderilmediğini veya gövdenin yazıldığını göstermek için faydalı bir ipucu.

<a name="order"></a>

## <a name="middleware-order"></a>Ara yazılım sırası

Ara yazılım bileşenlerinin `Startup.Configure` yöntemi içinde eklendiği sıra, ara yazılım bileşenlerinin isteklerde çağrıldığı sırayı ve yanıtın ters sırasını tanımlar. Sıra, güvenlik, performans ve işlevsellik açısından **önemlidir** .

Aşağıdaki `Startup.Configure` yöntemi, güvenlikle ilgili ara yazılım bileşenlerini önerilen sırayla ekler:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

Önceki kodda:

* [Bireysel kullanıcılar hesaplarıyla](xref:security/authentication/identity) yeni bir Web uygulaması oluştururken eklenmemiş olan ara yazılım, yorum yapılır.
* Her ara yazılımın bu tam sıra, ancak birçok do olması gerekmez. Örneğin, `UseCors` ve `UseAuthentication` gösterilen sırada olmalıdır.

Aşağıdaki `Startup.Configure` yöntemi, genel uygulama senaryoları için ara yazılım bileşenleri ekler:

1. Özel durum/hata işleme
   * Uygulama geliştirme ortamında çalıştığında:
     * Geliştirici özel durum sayfası ara yazılımı (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) uygulama çalışma zamanı hatalarını raporlar.
     * Veritabanı hata sayfası ara yazılımı (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) veritabanı çalışma zamanı hatalarını raporlar.
   * Uygulama, üretim ortamında çalıştığında:
     * Özel durum Işleyici ara yazılımı (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) aşağıdaki middlewares oluşturulan özel durumları yakalar.
     * HTTP katı aktarım güvenliği Protokolü (HSTS) ara yazılımı (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) `Strict-Transport-Security` üstbilgisini ekler.
1. HTTPS yeniden yönlendirme ara yazılımı (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) HTTP isteklerini HTTPS 'ye yönlendirir.
1. Statik dosya ara yazılımı (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) statik dosyaları ve kısa devre dışı istek işlemeyi döndürür.
1. Tanımlama bilgisi Ilkesi ara yazılımı (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) uygulamayı AB Genel Veri Koruma Yönetmeliği (GDPR) düzenlemelerine uyar.
1. Kimlik doğrulama ara yazılımı (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), güvenli kaynaklara erişim izni vermeden önce kullanıcının kimliğini doğrulamaya çalışır.
1. Oturum ara yazılımı (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) oturum durumunu oluşturur ve korur. Uygulama oturum durumunu kullanıyorsa, tanımlama bilgisi Ilkesi ara yazılımı ve MVC ara yazılımı öncesinde oturum ara yazılımını çağırın.
1. MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) istek ardışık düzenine MVC eklemek için.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

Yukarıdaki örnek kodda, her bir ara yazılım uzantısı yöntemi <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> ad alanı aracılığıyla <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> gösterilir.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>, işlem hattına eklenen ilk ara yazılım bileşenidir. Bu nedenle, özel durum Işleyicisi ara yazılımı sonraki çağrılarında oluşan tüm özel durumları yakalar.

Statik dosya ara yazılımı, geri kalan bileşenlere geçmeden istekleri ve kısa devre dışı bırakabilirsiniz. bu sayede işlem hattının başlarında çağrılır. Statik dosya ara **yazılımı yetkilendirme denetimleri sağlamaz.** *Wwwroot*altındakiler de dahil olmak üzere statik dosya ara yazılımı tarafından sunulan tüm dosyalar herkese açık bir şekilde sunulur. Statik dosyaların güvenliğini sağlamaya yönelik bir yaklaşım için bkz. <xref:fundamentals/static-files>.

İstek statik dosya ara yazılımı tarafından işlenmemişse, kimlik doğrulaması yapan kimlik doğrulama ara yazılım (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) üzerinden geçirilir. Kimlik doğrulaması kısa devre dışı kimliği doğrulanmamış istekler değildir. Kimlik doğrulama ara yazılımı isteklerin kimliğini doğrulayabilse de, yetkilendirme (ve reddetme) yalnızca MVC, belirli bir Razor sayfası veya MVC denetleyicisi ve eylemi seçerse oluşur.

Aşağıdaki örnek, yanıt sıkıştırma ara yazılımı ile önce statik dosya isteklerinin statik dosya ara yazılımı tarafından işlendiği bir ara yazılım sırasını gösterir. Statik dosyalar bu ara yazılım sırasıyla sıkıştırılmaz. <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> MVC yanıtları sıkıştırılabilirler.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Kullanın, çalıştırın ve eşleyin

<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>ve <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>kullanarak HTTP işlem hattını yapılandırın. `Use` yöntemi, işlem hattı kısa devre dışı olabilir (yani, bir `next` isteği temsilcisi çağırmazsa). `Run` bir kuraldır ve bazı ara yazılım bileşenleri, işlem hattının sonunda çalışan `Run[Middleware]` Yöntemler sunabilir.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> uzantıları, işlem hattının dallanması için bir kural olarak kullanılır. `Map`, istek ardışık düzenini, verilen istek yolunun eşleşenleri temelinde dallandırır. İstek yolu verilen yol ile başlıyorsa, dal yürütülür.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

Aşağıdaki tabloda, önceki kodu kullanarak `http://localhost:1234` gelen istekler ve yanıtlar gösterilmektedir.

| İstek             | Yanıt                     |
| ------------------- | ---------------------------- |
| localhost: 1234      | Eşleme olmayan temsilciden Merhaba. |
| localhost: 1234/Map1 | Eşleme testi 1                   |
| localhost: 1234/MAP2 | Eşleme testi 2                   |
| localhost: 1234/map3 | Eşleme olmayan temsilciden Merhaba. |

`Map` kullanıldığında, eşleşen yol kesimleri `HttpRequest.Path` kaldırılır ve her istek için `HttpRequest.PathBase` eklenir.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, belirtilen koşulun sonucuna göre istek ardışık düzenini dallandırır. `Func<HttpContext, bool>` türündeki herhangi bir koşul, istekleri işlem hattının yeni bir dalına eşlemek için kullanılabilir. Aşağıdaki örnekte, bir sorgu dizesi değişkeninin varlığını algılamak için bir koşul kullanılır `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

Aşağıdaki tabloda, önceki kodu kullanarak `http://localhost:1234` gelen istekler ve yanıtlar gösterilmektedir.

| İstek                       | Yanıt                     |
| ----------------------------- | ---------------------------- |
| localhost: 1234                | Eşleme olmayan temsilciden Merhaba. |
| localhost: 1234/? dalı = ana | Kullanılan dal = ana         |

`Map` iç içe geçirmeyi destekler, örneğin:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` aynı anda birden çok kesimde eşleşir:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Yerleşik ara yazılım

ASP.NET Core aşağıdaki ara yazılım bileşenleriyle birlikte gönderilir. *Order* sütunu, istek işleme ardışık düzeninde ara yazılım yerleştirme ve ara yazılımın istek işlemeyi sonlandırabilecekleri koşullar bölümünde notlar sağlar. Bir ara yazılım, istek işlem hattının ne kadar kısa süreli olduğunu ve daha fazla aşağı akış ara yazılımı bir isteği işlemesini engelliyorsa, bu, *Terminal ara yazılımı*olarak adlandırılır. Kısa devre oluşturma hakkında daha fazla bilgi için, [IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma](#create-a-middleware-pipeline-with-iapplicationbuilder) bölümüne bakın.

| Ara yazılım | Açıklama | Sipariş verme |
| ---------- | ----------- | ----- |
| [Kimlik Doğrulaması](xref:security/authentication/identity) | Kimlik doğrulama desteği sağlar. | `HttpContext.User` önce. OAuth geri çağırmaları için Terminal. |
| [Tanımlama bilgisi Ilkesi](xref:security/gdpr) | Kişisel bilgileri depolamak için kullanıcılardan onay izler ve `secure` ve `SameSite`gibi tanımlama bilgisi alanları için en düşük standartları uygular. | Tanımlama bilgilerini veren ara yazılım öncesi. Örnekler: Authentication, Session, MVC (TempData). |
| [CORS](xref:security/cors) | Çıkış noktaları arası kaynak paylaşımını yapılandırır. | CORS kullanan bileşenlerden önce. |
| [Tanılama](xref:fundamentals/error-handling) | Geliştirici özel durum sayfası, özel durum işleme, durum kodu sayfaları ve yeni uygulamalar için varsayılan Web sayfası sağlayan çeşitli ayrı middlewares. | Hata oluşturan bileşenlerden önce. Özel durumlar için Terminal veya yeni uygulamalar için varsayılan Web sayfasına hizmet sunma. |
| [İletilen üstbilgiler](xref:host-and-deploy/proxy-load-balancer) | Proxy üst bilgilerini geçerli istek üzerine iletir. | Güncelleştirilmiş alanları kullanan bileşenlerden önce. Örnekler: Scheme, Host, istemci IP, yöntem. |
| [Sistem durumu denetimi](xref:host-and-deploy/health-checks) | ASP.NET Core uygulamasının sistem durumunu ve bağımlılıklarını denetler (örneğin, veritabanı kullanılabilirliğini denetleme). | Bir istek bir sistem durumu denetimi uç noktasıyla eşleşiyorsa Terminal. |
| [HTTP yöntemini geçersiz kılma](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Gelen POST isteğinin yöntemi geçersiz kılmasına izin verir. | Güncelleştirilmiş yöntemini kullanan bileşenlerden önce. |
| [HTTPS yönlendirmesi](xref:security/enforcing-ssl#require-https) | Tüm HTTP isteklerini HTTPS 'ye yeniden yönlendirin. | URL 'YI kullanan bileşenlerden önce. |
| [HTTP katı taşıma güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Özel bir yanıt üst bilgisi ekleyen güvenlik geliştirme ara yazılımı. | Yanıtlar gönderilmeden önce ve istekleri değiştiren bileşenler. Örnekler: Iletilen üstbilgiler, URL yeniden yazma. |
| [MVC](xref:mvc/overview) | MVC/Razor Pages ile istekleri işler. | Bir istek bir rota ile eşleşiyorsa Terminal. |
| [OWıN](xref:fundamentals/owin) | OWIN tabanlı uygulamalar, sunucular ve ara yazılım ile birlikte çalışma. | OWıN ara yazılımı isteği tam olarak işliyorsa Terminal. |
| [Yanıtları Önbelleğe Alma](xref:performance/caching/middleware) | Yanıtları önbelleğe almak için destek sağlar. | Önbelleğe alma gerektiren bileşenlerden önce. |
| [Yanıt sıkıştırması](xref:performance/response-compression) | Yanıtları sıkıştırmak için destek sağlar. | Sıkıştırma gerektiren bileşenlerden önce. |
| [Yerelleştirme iste](xref:fundamentals/localization) | Yerelleştirme desteği sağlar. | Yerelleştirmenin önemli bileşenlerinden önce. |
| [Uç nokta yönlendirme](xref:fundamentals/routing) | İstek yollarını tanımlar ve kısıtlar. | Eşleşen yolların terminali. |
| [Oturum](xref:fundamentals/app-state) | Kullanıcı oturumlarını yönetmek için destek sağlar. | Oturum gerektiren bileşenlerden önce. |
| [Statik dosyalar](xref:fundamentals/static-files) | Statik dosyaları ve dizin taramayı sunma desteği sağlar. | Bir istek bir dosyayla eşleşiyorsa Terminal. |
| [URL yeniden yazma](xref:fundamentals/url-rewriting) | URL 'Leri yeniden yazma ve istekleri yeniden yönlendirme desteği sağlar. | URL 'YI kullanan bileşenlerden önce. |
| [WebSockets](xref:fundamentals/websockets) | WebSockets protokolünü etkinleştirilir. | WebSocket isteklerini kabul etmek için gereken bileşenlerden önce. |

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
