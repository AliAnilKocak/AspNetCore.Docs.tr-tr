---
title: ASP.NET Core 2,1 ' deki yenilikler
author: isaac2004
description: ASP.NET Core 2,1 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: a45ba44fb7911a21927a4a996c0d6fa9eb776357
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963188"
---
# <a name="whats-new-in-aspnet-core-21"></a>ASP.NET Core 2,1 ' deki yenilikler

Bu makalede, ASP.NET Core 2,1 ' deki en önemli değişiklikler, ilgili belgelerin bağlantılarıyla vurgulanır.

## SignalR

SignalR ASP.NET Core 2,1 için yeniden yazıldı. ASP.NET Core SignalR bir dizi geliştirme içerir:

* Basitleştirilmiş bir genişleme modeli.
* JQuery bağımlılığı olmayan yeni bir JavaScript istemcisi.
* MessagePack tabanlı yeni bir Compact binary protokolü.
* Özel protokoller için destek.
* Yeni bir akış yanıt modeli.
* Çıplak WebSockets tabanlı istemciler için destek.

Daha fazla bilgi için bkz. [ASP.NET Core SignalR](xref:signalr/introduction).

## <a name="razor-class-libraries"></a>Razor sınıf kitaplıkları

ASP.NET Core 2,1, bir kitaplıkta Razor tabanlı kullanıcı arabirimini oluşturmayı ve eklemeyi kolaylaştırır ve birden fazla proje arasında paylaşabilir. Yeni Razor SDK 'Sı, bir NuGet paketine paketlenebilecek bir sınıf kitaplığı projesinde Razor dosyaları oluşturulmasına izin verebilir. Kitaplıklardaki görünümler ve sayfalar otomatik olarak keşfedilir ve uygulama tarafından geçersiz kılınabilir. Razor derlemesini yapıyla tümleştirerek:

* Uygulama başlatma süresi önemli ölçüde daha hızlıdır.
* Çalışma zamanında Razor görünümlerinin ve sayfalarının hızlı güncelleştirmeleri, yinelemeli bir geliştirme iş akışının bir parçası olarak hala kullanılabilir.

Daha fazla bilgi için bkz. [Razor Sınıf Kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma](xref:razor-pages/ui-class).

## <a name="identity-ui-library--scaffolding"></a>Identity UI kitaplığı & Yapı iskelesi

ASP.NET Core 2,1, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar. Kimlik içeren uygulamalar, kimlik Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için yeni Identity desteği 'ı uygulayabilir. Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz. Oluşturulan kod, RCL kimliği içindeki aynı koddan önceliklidir.

Kimlik **doğrulaması içermeyen uygulamalar** , RCL kimlik paketini eklemek için Identity desteği 'ı uygulayabilir. Oluşturulacak kimlik kodunu seçme seçeneğiniz vardır.

Daha fazla bilgi için [ASP.NET Core projelerinde yapı Iskelesi kimliği](xref:security/authentication/scaffold-identity)' ne bakın.

## <a name="https"></a>HTTPS

Güvenlik ve gizlilikle ilgili daha fazla odaklanarak Web Apps için HTTPS 'nin etkinleştirilmesi önemlidir. HTTPS zorlaması Web üzerinde giderek daha sıkı hale geliyor. HTTPS kullanmayan siteler güvensiz olarak değerlendirilir. Tarayıcılar (Bermıum, Mozilla), Web özelliklerinin güvenli bir içerikten kullanılması gerektiğini zorlamaya başlıyor. [GDPR](xref:security/gdpr) , Kullanıcı gizliliğini korumak için https kullanılmasını gerektirir. Üretimde HTTPS kullanılması kritiktir, ancak geliştirme sırasında HTTPS kullanılması, dağıtımdaki sorunları önlemeye yardımcı olabilir (örneğin, güvenli olmayan bağlantılar). ASP.NET Core 2,1, geliştirme sırasında HTTPS kullanmayı ve üretimde HTTPS 'yi yapılandırmayı kolaylaştıran bir dizi geliştirme içerir. Daha fazla bilgi için bkz. [https 'Yi zorlama](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Varsayılan olarak açık

Güvenli Web sitesi geliştirmeyi kolaylaştırmak için, HTTPS artık varsayılan olarak etkinleştirilmiştir. 2,1 ' den başlayarak, yerel bir geliştirme sertifikası mevcut olduğunda, Kestrel `https://localhost:5001` dinler. Geliştirme sertifikası oluşturuldu:

* İlk kez .NET Core SDK ilk çalıştırma deneyiminin bir parçası olarak SDK 'Yı ilk kez kullandığınızda.
* Yeni `dev-certs` aracını kullanarak el ile.

Sertifikaya güvenmek için `dotnet dev-certs https --trust` çalıştırın.

### <a name="https-redirection-and-enforcement"></a>HTTPS yönlendirmesi ve zorlaması

Web uygulamalarının genellikle hem HTTP hem de HTTPS üzerinde dinlemesi gerekir, ancak tüm HTTP trafiğini HTTPS 'ye yeniden yönlendirir. 2,1 ' de, yapılandırma veya bağlı sunucu bağlantı noktalarının varlığına göre daha fazla yeniden yönlendirilen özelleştirilmiş HTTPS yeniden yönlendirme ara yazılımı sunulmuştur.

HTTPS kullanımı, [http katı aktarım güvenliği Protokolü (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)kullanılarak daha fazla zorlanabilir. HSTS, tarayıcıların siteye her zaman HTTPS aracılığıyla erişmesini söyler. ASP.NET Core 2,1, maksimum yaş, alt etki alanları ve HSTS önyükleme listesi seçeneklerini destekleyen HSTS ara yazılımı ekler.

### <a name="configuration-for-production"></a>Üretim için yapılandırma

Üretimde HTTPS 'nin açıkça yapılandırılması gerekir. 2,1 ' de, Kestrel için HTTPS yapılandırmasına yönelik varsayılan yapılandırma şeması eklenmiştir. Uygulamalar aşağıdakileri kullanacak şekilde yapılandırılabilir:

* URL 'Ler dahil olmak üzere birden fazla uç nokta. Daha fazla bilgi için bkz. [Kestrel Web Server Implementation: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Diskteki bir dosyadan veya bir sertifika deposundan HTTPS için kullanılacak sertifika.

## <a name="gdpr"></a>GDPR

ASP.NET Core, bazı [ab genel veri koruma yönetmeliği (GDPR)](https://www.eugdpr.org/) gereksinimlerini karşılamaya yardımcı olmak Için API 'ler ve şablonlar sağlar. Daha fazla bilgi için bkz. [GDPR Support in ASP.NET Core](xref:security/gdpr). [Örnek bir uygulama](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) , ' nin nasıl kullanılacağını gösterir ve ASP.NET Core 2,1 ŞABLONLARıNA eklenen GDPR uzantı noktalarının ve API 'lerin çoğunu test etmenizi sağlar.

## <a name="integration-tests"></a>Tümleştirme testleri

Test oluşturma ve yürütmeyi kolaylaştıran yeni bir paket tanıtılmıştır. [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) paketi aşağıdaki görevleri gerçekleştirir:

* Bağımlılık dosyasını ( *\*. Deps*) test projesinin *bin* klasörüne kopyalar.
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

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T >

ASP.NET Core 2,1, temiz ve açıklayıcı Web API 'Leri oluşturmayı kolaylaştıran yeni programlama kuralları ekliyor. `ActionResult<T>`, bir uygulamanın yanıt türünü ya da başka bir eylem sonucunu (ıactionresult gibi) döndürmesini sağlamak için eklenen yeni bir türdür, yine de yanıt türünü gösterir. `[ApiController]` özniteliği, Web API 'sine özgü kuralları ve davranışları kabul etmenin yolu olarak da eklenmiştir.

Daha fazla bilgi için bkz. [ASP.NET Core Web API 'Leri oluşturma](xref:web-api/index).

## <a name="ihttpclientfactory"></a>Ihttpclientfactory

ASP.NET Core 2,1, uygulamalarda `HttpClient` örneklerinin daha kolay yapılandırılacağını ve kullanılmasını kolaylaştıran yeni bir `IHttpClientFactory` hizmeti içerir. `HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramına zaten sahiptir. Fabrika:

* Adlandırılmış istemci başına `HttpClient` örneklerinin kaydını daha sezgisel hale getirir.
* Yeniden deneme, devre kesiciler vb. için, Polly ilkelerinin kullanılmasına izin veren bir Polly işleyici uygular.

Daha fazla bilgi için bkz. [http Isteklerini başlatma](xref:fundamentals/http-requests).

## <a name="kestrel-transport-configuration"></a>Kestrel aktarma yapılandırması

ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır. Daha fazla bilgi için bkz. [Kestrel Web sunucusu uygulama: aktarım yapılandırması](xref:fundamentals/servers/kestrel#transport-configuration).

## <a name="generic-host-builder"></a>Genel konak Oluşturucu

Genel ana bilgisayar Oluşturucu (`HostBuilder`) sunulmuştur. Bu Oluşturucu, HTTP isteklerini (mesajlaşma, arka plan görevleri vb.) işlemeyecek uygulamalar için kullanılabilir.

Daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Güncelleştirilmiş SPA şablonları

Redux ile ilgili angular, tepki verme ve tepki verme için tek sayfalı uygulama şablonları, her bir çerçeve için Standart proje yapılarını ve derleme sistemlerini kullanacak şekilde güncelleştirilir.

Angular şablonu angular CLı 'yı temel alır ve yanıt verme şablonları Create-tepki-App ' i temel alır.

Daha fazla bilgi için bkz.:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Razor varlıkları için arama Razor Pages

2,1 Razor Pages ' de, listelenen sırayla aşağıdaki dizinlerde Razor varlıklarını (örneğin, düzenler ve parals) arayın:

1. Geçerli sayfalar klasörü.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="razor-pages-in-an-area"></a>Bir alanda Razor Pages

Artık Razor Pages [alanı](xref:mvc/controllers/areas)desteklemektedir. Bir alan örneği görmek için, bireysel kullanıcı hesaplarıyla yeni bir Razor Pages Web uygulaması oluşturun. Bireysel kullanıcı hesaplarıyla bir Razor Pages Web uygulaması */Areas/Identity/Pages*içerir.

## <a name="mvc-compatibility-version"></a>MVC uyumluluk sürümü

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> yöntemi, bir uygulamanın, ASP.NET Core MVC 2,1 veya sonraki sürümlerde ortaya çıkan olası davranış değişikliklerinin kabul etmesine veya devre dışı olmasına izin verir.

Daha fazla bilgi için bkz. <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>2,0 ' den 2,1 ' e geçiş

Bkz. [ASP.NET Core 2,0 ' den 2,1 ' e geçiş](xref:migration/20_21).

## <a name="additional-information"></a>Ek bilgiler

Değişikliklerin tamamı listesi için [ASP.NET Core 2,1 sürüm notlarına](https://github.com/aspnet/Home/releases/tag/2.1.0)bakın.
