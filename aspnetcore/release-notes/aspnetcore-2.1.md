---
title: ASP.NET Core 2,1 ' deki yenilikler
author: isaac2004
description: ASP.NET Core 2,1 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: 1f68bd5347ba1f67e56b7a2fe8914ffdaef8010c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774086"
---
# <a name="whats-new-in-aspnet-core-21"></a>ASP.NET Core 2,1 ' deki yenilikler

Bu makalede, ASP.NET Core 2,1 ' deki en önemli değişiklikler, ilgili belgelerin bağlantılarıyla vurgulanır.

## SignalR

SignalRASP.NET Core 2,1 için yeniden yazıldı. ASP.NET Core SignalR birkaç geliştirme içerir:

* Basitleştirilmiş bir genişleme modeli.
* JQuery bağımlılığı olmayan yeni bir JavaScript istemcisi.
* MessagePack tabanlı yeni bir Compact binary protokolü.
* Özel protokoller için destek.
* Yeni bir akış yanıt modeli.
* Çıplak WebSockets tabanlı istemciler için destek.

Daha fazla bilgi için bkz [. SignalRASP.NET Core ](xref:signalr/introduction).

## <a name="razor-class-libraries"></a>Razorsınıf kitaplıkları

ASP.NET Core 2,1, bir kitaplıkta derleme ve ekleme Razortemelli Kullanıcı arabirimini daha kolay hale getirir ve bunu birden fazla proje arasında paylaşabilir. Yeni Razor SDK, bir NuGet Razor paketine paketlenebilecek bir sınıf kitaplığı projesinde dosya oluşturulmasına izin vermez. Kitaplıklardaki görünümler ve sayfalar otomatik olarak keşfedilir ve uygulama tarafından geçersiz kılınabilir. Derlemeyi derlemeyle Razor tümleştirerek:

* Uygulama başlatma süresi önemli ölçüde daha hızlıdır.
* Çalışma zamanında görünümler Razor ve sayfalar için hızlı güncelleştirmeler, yinelemeli bir geliştirme iş akışının bir parçası olarak hala kullanılabilir.

Daha fazla bilgi için bkz. [ Razor Sınıf Kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>IdentityUI kitaplığı & yapı iskelesi

ASP.NET Core 2,1, bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar. Dahil Identity edilen uygulamalar, Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodunu seçmeli olarak eklemek için yeni desteği uygulayabilir. Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz. Oluşturulan kod Identity RCL 'deki aynı koda göre önceliklidir.

Kimlik **doğrulaması içermeyen uygulamalar** , RCL Identity paketini eklemek Identity için desteği uygulayabilir. Oluşturulacak kodu seçme Identity seçeneğiniz vardır.

Daha fazla bilgi için bkz. [ASP.NET Core projelerinde Identity Scaffold](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Güvenlik ve gizlilikle ilgili daha fazla odaklanarak Web Apps için HTTPS 'nin etkinleştirilmesi önemlidir. HTTPS zorlaması Web üzerinde giderek daha sıkı hale geliyor. HTTPS kullanmayan siteler güvensiz olarak değerlendirilir. Tarayıcılar (Bermıum, Mozilla), Web özelliklerinin güvenli bir içerikten kullanılması gerektiğini zorlamaya başlıyor. [GDPR](xref:security/gdpr) , Kullanıcı gizliliğini korumak için https kullanılmasını gerektirir. Üretimde HTTPS kullanılması kritiktir, ancak geliştirme sırasında HTTPS kullanılması, dağıtımdaki sorunları önlemeye yardımcı olabilir (örneğin, güvenli olmayan bağlantılar). ASP.NET Core 2,1, geliştirme sırasında HTTPS kullanmayı ve üretimde HTTPS 'yi yapılandırmayı kolaylaştıran bir dizi geliştirme içerir. Daha fazla bilgi için bkz. [https 'Yi zorlama](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Varsayılan olarak açık

Güvenli Web sitesi geliştirmeyi kolaylaştırmak için, HTTPS artık varsayılan olarak etkinleştirilmiştir. 2,1 ' den başlayarak, Kestrel bir `https://localhost:5001` yerel geliştirme sertifikası olduğunda dinler. Geliştirme sertifikası oluşturuldu:

* İlk kez .NET Core SDK ilk çalıştırma deneyiminin bir parçası olarak SDK 'Yı ilk kez kullandığınızda.
* Yeni `dev-certs` aracı kullanarak el ile.

Sertifikaya `dotnet dev-certs https --trust` güvenmek için ' i çalıştırın.

### <a name="https-redirection-and-enforcement"></a>HTTPS yönlendirmesi ve zorlaması

Web uygulamalarının genellikle hem HTTP hem de HTTPS üzerinde dinlemesi gerekir, ancak tüm HTTP trafiğini HTTPS 'ye yeniden yönlendirir. 2,1 ' de, yapılandırma veya bağlı sunucu bağlantı noktalarının varlığına göre daha fazla yeniden yönlendirilen özelleştirilmiş HTTPS yeniden yönlendirme ara yazılımı sunulmuştur.

HTTPS kullanımı, [http katı aktarım güvenliği Protokolü (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)kullanılarak daha fazla zorlanabilir. HSTS, tarayıcıların siteye her zaman HTTPS aracılığıyla erişmesini söyler. ASP.NET Core 2,1, maksimum yaş, alt etki alanları ve HSTS önyükleme listesi seçeneklerini destekleyen HSTS ara yazılımı ekler.

### <a name="configuration-for-production"></a>Üretim için yapılandırma

Üretimde HTTPS 'nin açıkça yapılandırılması gerekir. 2,1 ' de, Kestrel için HTTPS yapılandırmasına yönelik varsayılan yapılandırma şeması eklenmiştir. Uygulamalar aşağıdakileri kullanacak şekilde yapılandırılabilir:

* URL 'Ler dahil olmak üzere birden fazla uç nokta. Daha fazla bilgi için bkz. [Kestrel Web Server Implementation: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Diskteki bir dosyadan veya bir sertifika deposundan HTTPS için kullanılacak sertifika.

## <a name="gdpr"></a>GDPR

ASP.NET Core, bazı [ab genel veri koruma yönetmeliği (GDPR)](https://www.eugdpr.org/) gereksinimlerini karşılamaya yardımcı olmak Için API 'ler ve şablonlar sağlar. Daha fazla bilgi için bkz. [GDPR Support in ASP.NET Core](xref:security/gdpr). [Örnek bir uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) , ' nin nasıl kullanılacağını gösterir ve ASP.NET Core 2,1 ŞABLONLARıNA eklenen GDPR uzantı noktalarının ve API 'lerin çoğunu test etmenizi sağlar.

## <a name="integration-tests"></a>Tümleştirme testleri

Test oluşturma ve yürütmeyi kolaylaştıran yeni bir paket tanıtılmıştır. [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) paketi aşağıdaki görevleri gerçekleştirir:

* Bağımlılık dosyasını (*\*. Deps*) test projesinin *bin* klasörüne kopyalar.
* Testler yürütüldüğünde statik dosya ve sayfa/görünümlerin bulunması için, içerik kökünü test edilen uygulamanın proje köküne ayarlar.
* [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)ile sınanan uygulamayı daha kolay hale getirmek Için [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar.

Aşağıdaki test, dizin sayfasının başarılı durum kodu ve doğru Content-Type üst bilgisi ile yüklenip yüklenduğunu denetlemek için [xUnit](https://xunit.github.io/) kullanır:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Daha fazla bilgi için bkz. [tümleştirme testleri](xref:test/integration-tests) konusu.

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2,1, temiz ve açıklayıcı Web API 'Leri oluşturmayı kolaylaştıran yeni programlama kuralları ekliyor. `ActionResult<T>`, bir uygulamanın yanıt türünü ya da başka bir eylem sonucunu (ıactionresult gibi) döndürmesini sağlamak için yeni bir tür eklenmiştir, ancak yine de yanıt türünü gösterir. Özniteliği `[ApiController]` , Web API 'sine özgü kuralları ve davranışları kabul etmenin yolu olarak da eklenmiştir.

Daha fazla bilgi için bkz. [ASP.NET Core Web API 'Leri oluşturma](xref:web-api/index).

## <a name="ihttpclientfactory"></a>Ihttpclientfactory

ASP.NET Core 2,1, `HttpClient` uygulamalarda örneklerini `IHttpClientFactory` yapılandırmayı ve kullanmayı kolaylaştıran yeni bir hizmet içerir. `HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramına zaten sahiptir. Fabrika:

* Adlandırılmış istemci `HttpClient` başına örnek kaydı daha sezgisel hale gelir.
* Yeniden deneme, devre kesiciler vb. için, Polly ilkelerinin kullanılmasına izin veren bir Polly işleyici uygular.

Daha fazla bilgi için bkz. [http Isteklerini başlatma](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Kestrel aktarma yapılandırması

ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır. Daha fazla bilgi için bkz. [Kestrel Web sunucusu uygulama: aktarım yapılandırması](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Genel konak Oluşturucu

Genel ana bilgisayar Oluşturucu (`HostBuilder`) sunuldu. Bu Oluşturucu, HTTP isteklerini (mesajlaşma, arka plan görevleri vb.) işlemeyecek uygulamalar için kullanılabilir.

Daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Güncelleştirilmiş SPA şablonları

Redux ile ilgili angular, tepki verme ve tepki verme için tek sayfalı uygulama şablonları, her bir çerçeve için Standart proje yapılarını ve derleme sistemlerini kullanacak şekilde güncelleştirilir.

Angular şablonu angular CLı 'yı temel alır ve yanıt verme şablonları Create-tepki-App ' i temel alır.

Daha fazla bilgi için bkz.

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>RazorVarlıklar için Razor arama sayfaları

2,1 ' de Razor sayfalar, listelenen Razor sırada aşağıdaki dizinlerde bulunan varlıkları (örneğin, düzenler ve partileri) arar:

1. Geçerli sayfalar klasörü.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="razor-pages-in-an-area"></a>RazorBir alandaki sayfalar

RazorSayfalar artık [bölgeleri](xref:mvc/controllers/areas)destekliyor. Bir alan örneği görmek için bireysel kullanıcı hesaplarıyla Yeni Razor bir sayfa Web uygulaması oluşturun. Bireysel Razor Kullanıcı hesapları içeren bir sayfa Web uygulaması */Areas/Identity/Pages*içerir.

## <a name="mvc-compatibility-version"></a>MVC uyumluluk sürümü

Yöntemi <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> , bir UYGULAMANıN ASP.NET Core MVC 2,1 veya sonraki bir sürümde sunulmuş olabilecek davranış değişikliklerinin kabul etmesine veya devre dışı olmasına izin verir.

Daha fazla bilgi için bkz. <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>2,0 ' den 2,1 ' e geçiş

Bkz. [ASP.NET Core 2,0 ' den 2,1 ' e geçiş](xref:migration/20_21).

## <a name="additional-information"></a>Ek bilgiler

Değişikliklerin tamamı listesi için [ASP.NET Core 2,1 sürüm notlarına](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0)bakın.
