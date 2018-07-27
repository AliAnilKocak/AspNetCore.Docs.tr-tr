---
title: HTTP isteklerini başlatma
author: stevejgordon
description: ASP.NET core'da mantıksal HttpClient örneğini yönetmek için IHttpClientFactory arabirimi kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2018
uid: fundamentals/http-requests
ms.openlocfilehash: 87424eaea499ba7ece1e5ef88649fcbb2e297635
ms.sourcegitcommit: 516d0645c35ea784a3ae807be087ae70446a46ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39320661"
---
# <a name="initiate-http-requests"></a>HTTP isteklerini başlatma

Tarafından [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), ve [Steve Gordon](https://github.com/stevejgordon)

Bir [IHttpClientFactory](/dotnet/api/system.net.http.ihttpclientfactory) kayıtlı ve oluşturma ve yapılandırma için kullanılan [HttpClient](/dotnet/api/system.net.http.httpclient) uygulama örnekleri. Aşağıdaki avantajları sunar:

* Adlandırma ve mantıksal yapılandırmak için merkezi bir konum sağlar `HttpClient` örnekleri. Örneğin, "github" istemci kaydettirilmeli ve GitHub erişim sağlamak için yapılandırılmış. Varsayılan istemci diğer amaçlar için kaydedilebilir.
* İşleyicileri temsilci aracılığıyla giden ara yazılım kavramı'ı kodlar `HttpClient` ve, yararlanmak Polly tabanlı ara yazılım için uzantılar sağlar.
* Havuzu ve arka plandaki, yaşam süresini yöneten `HttpClientMessageHandler` el ile yönetilmesi sırasında oluşan Genel DNS sorunları önlemek için örnekleri `HttpClient` yaşam süresi yok.
* Yapılandırılabilir günlük deneyimi ekler (aracılığıyla `ILogger`) fabrikası tarafından oluşturulan istemcileri aracılığıyla gönderilen tüm istekler için.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

.NET Framework'ü hedefleyen projeleri yüklenmesini gerektiren [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketi. .NET Core ve başvuru hedefleyen projeler [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) zaten dahil `Microsoft.Extensions.Http` paket.

## <a name="consumption-patterns"></a>Tüketim modelleri

Birkaç şekilde `IHttpClientFactory` bir uygulamada kullanılabilir:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Türü belirlenmiş istemci](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

Bunların hiçbiri diğerine kesinlikle üst. En iyi yaklaşım, uygulamanın kısıtlamaları bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

`IHttpClientFactory` Çağırarak kayıtlı `AddHttpClient` genişletme yöntemini `IServiceCollection`içine `Startup.ConfigureServices` yöntemi.

[!code-csharp[](http-requests/samples/Startup.cs?name=snippet1)]

Kaydedildikten sonra kod kabul edebilen bir `IHttpClientFactory` Hizmetleri ile her yerde yerleştirilebilir [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı). `IHttpClientFactory` Oluşturmak için kullanılan bir `HttpClient` örneği:

[!code-csharp[](http-requests/samples/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,20)]

Kullanarak `IHttpClientFactory` bu şekilde var olan bir uygulamayı yeniden düzenleme için harika bir yoludur. Yolda hiçbir etkisi olmaz `HttpClient` kullanılır. Yerde nerede `HttpClient` örnekleri şu anda oluşturulur, bu oluşumları çağrısı ile Değiştir `CreateClient`.

### <a name="named-clients"></a>Adlandırılmış istemciler

Bir uygulama birden çok farklı kullanımlarını gerektiriyorsa `HttpClient`, her farklı bir yapılandırma ile bir seçenek kullanmaktır **istemcileri adlı**. Yapılandırma için bir adlandırılmış `HttpClient` kaydı sırasında belirtilen `Startup.ConfigureServices`.

[!code-csharp[](http-requests/samples/Startup.cs?name=snippet2)]

Önceki kodda, `AddHttpClient` , adı "github" sağlama çağrılır. Bu istemci bazı varsayılan yapılandırma uygulandı sahip&mdash;taban adresini ve GitHub API ile çalışması için gereken iki üstbilgi.

Her zaman `CreateClient` çağrılır, yeni bir örneğini `HttpClient` oluşturulur ve yapılandırma eylem olarak adlandırılır.

Adlandırılmış bir istemcinin kullanılacağı bir dize parametresi geçirilebilir `CreateClient`. Oluşturulacak istemci adını belirtin:

[!code-csharp[](http-requests/samples/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=20)]

Önceki kodda, istek bir ana bilgisayar adı belirtmeniz gerekmez. İstemcisi için yapılandırılan taban adresi kullanıldığından, yol yalnızca geçirebilirsiniz.

### <a name="typed-clients"></a>Türü belirlenmiş istemci

Türü belirlenmiş istemci anahtarı olarak dizeleri kullanmak zorunda kalmadan adlandırılmış istemcileri ile aynı özellikleri sağlar. Türü belirlenmiş istemci yaklaşım, IntelliSense ve derleyici istemciler tüketildiğinde Yardım sağlar. Yapılandırma ve belirli bir ile etkileşim kurmak için tek bir konum sağladıkları `HttpClient`. Örneğin, tek bir türü belirlenmiş istemci tek bir arka uç noktası için kullanılabilir ve bu uç nokta tüm mantığı uğraşmanızı kapsüller. Başka bir avantajı DI, iş ve uygulamanızı gerekli olduğu durumlarda yerleştirilebilir ' dir.

Türü belirlenmiş istemci kabul eden bir `HttpClient` oluşturucusuna parametre:

[!code-csharp[](http-requests/samples/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Önceki kodda, türü belirlenmiş istemci yapılandırma taşınır. `HttpClient` Nesne, ortak bir özellik olarak gösterilir. Kullanıma sunan bir özel API yöntemleri tanımlamak mümkündür `HttpClient` işlevselliği. `GetAspNetDocsIssues` Yöntemi en son açık sorunlar bir GitHub deposundan ayrıştırabilir ve sorgulamak için gereken kodu kapsüller.

Türü belirlenmiş bir istemci, genel kaydedilecek `AddHttpClient` genişletme yöntemi içinde kullanılabilir `Startup.ConfigureServices`, türü belirlenmiş istemci sınıfı belirtme:

[!code-csharp[](http-requests/samples/Startup.cs?name=snippet3)]

Türü belirlenmiş istemci DI ile geçici olarak kaydedilir. Türü belirlenmiş istemci eklenen ve doğrudan tüketilen:

[!code-csharp[](http-requests/samples/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Kaydı sırasında tercih etmeleri durumunda, türü belirlenmiş istemci yapılandırmasını belirtilebilir `Startup.ConfigureServices`, yerine belirlenmiş istemcinin Oluşturucusu:

[!code-csharp[](http-requests/samples/Startup.cs?name=snippet4)]

Tamamen yalıtılacak mümkündür `HttpClient` türü belirlenmiş istemci içinde. Bir özellik olarak gösterme yerine genel yöntemleri arama sağlanabilir `HttpClient` dahili olarak örneği.

[!code-csharp[](http-requests/samples/GitHub/RepoService.cs?name=snippet1&highlight=3)]

Önceki kodda, `HttpClient` özel bir alan olarak depolanır. Dış çağrı yapmak için tüm erişim geçtiği `GetRepos` yöntemi.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory` diğer üçüncü taraf kitaplıklar ile birlikte gibi kullanılabilir [yeniden donatılması](https://github.com/paulcbetts/refit). Refit, .NET için bir REST kitaplıktır. Bu REST API Canlı arabirimleri dönüştürür. Arabiriminin göre dinamik olarak oluşturulan `RestService`kullanarak `HttpClient` dış HTTP çağrısı.

Dış API ve yanıtını temsil etmek için bir arabirim ve bir yanıt tanımlanmıştır:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Uygulama oluşturmak için Refit kullanarak bir türü belirlenmiş istemci eklenebilir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

Tanımlanan arabirimi gerektiğinde DI ve Refit tarafından sağlanan uygulama ile kullanılabilecek:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Giden istek ara yazılımı

`HttpClient` Giden HTTP istekleri için birbirine işleyicileri temsilci olarak görevlendirme kavramı zaten sahip. `IHttpClientFactory` Adlandırılmış her istemci için uygulanacak işleyicilerini tanımlamak kolaylaştırır. Bu, kayıt ve giden bir istek ara yazılım ardışık düzenini oluşturmak için birden çok işleyicileri zincirleme destekler. Bu işleyiciler, her giden istekten önce ve sonra çalışma gerçekleştiremezsiniz. Bu düzen, ASP.NET Core gelen ara yazılım ardışık benzerdir. Desen etrafında HTTP isteklerini, önbelleğe alma, hata, seri hale getirme, işleme ve günlüğe kaydetme gibi çapraz kesme konuları yönetmek için bir mekanizma sağlar.

Bir işleyici oluşturmak için türetilen bir sınıf tanımlama `DelegatingHandler`. Geçersiz kılma `SendAsync` yöntemi istek ardışık düzende sonraki işleyici geçirmeden önce kodu çalıştırmak için:

[!code-csharp[Main](http-requests/samples/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Yukarıdaki kod, bir temel işleyicisini tanımlar. Bir X-API-KEY üst bilgi isteği dahil edilmiş görmek için denetler. Üst bilgisi eksik, bu HTTP çağrısı kaçınmak ve uygun bir yanıt döndürür.

Kayıt sırasında bir veya daha fazla işleyicileri için yapılandırmasına eklenebilir bir `HttpClient`. Bu görev üzerinde genişletme yöntemleri gerçekleştirilir `IHttpClientBuilder`.

[!code-csharp[](http-requests/samples/Startup.cs?name=snippet5)]

Önceki kodda, `ValidateHeaderHandler` DI ile kaydedilir. İşleyici **gerekir** DI geçici olarak kayıtlı olmalıdır. Bir kez kayıtlı `AddHttpMessageHandler` türü için işleyici geçirme çağrılabilir.

Birden fazla işleyici sırayla yürütülmesi gerektiğini kaydedilebilir. Her işleyici son kadar bir sonraki işleyici sarmalar `HttpClientHandler` isteği yürütür:

[!code-csharp[](http-requests/samples/Startup.cs?name=snippet6)]

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyicileri kullanın

`IHttpClientFactory` adlı bir popüler üçüncü taraf kitaplığı ile tümleştirilir [Polly](https://github.com/App-vNext/Polly). Polly kapsamlı esnekliği ve .NET için geçici hata işleme Kitaplığı ' dir. Bu hizmet sayesinde geliştiriciler, yeniden deneme, devre kesici, zaman aşımı, bölme perdesi yalıtım ve geri dönüş gibi ilkeleri fluent ve iş parçacığı açısından güvenli bir şekilde ifade etmek.

Genişletme yöntemleri, Polly ilkeleriyle kullanımını etkinleştirmek için yapılandırılmış sağlanan `HttpClient` örnekleri. Polly uzantıları kullanılabilir [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketi. Bu paket bulunup bulunmadığına [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app). Açık bir uzantıları kullanmak için `<PackageReference />` projeye eklenmelidir.

[!code-csharp[](http-requests/samples/HttpClientFactorySample.csproj?highlight=9)]

Bu paket geri yükledikten sonra istemcileri Polly tabanlı işleyicileri ekleme desteklemek genişletme yöntemleri kullanılabilir.

### <a name="handle-transient-faults"></a>Geçici hataları işleme

Dış HTTP çağrıları yapma meydana gelmesini beklediğiniz en yaygın hataların geçici olacaktır. Bir uzantı yöntemi `AddTransientHttpErrorPolicy` olduğundan geçici hataları işlemek için tanımladığı bir ilke sağlayan dahil. Bu uzantı metot tanıtıcısı ile yapılandırılmış ilkeler `HttpRequestException`, HTTP 5xx yanıtları ve HTTP 408 yanıtlar.

`AddTransientHttpErrorPolicy` Uzantı içinde kullanılabilir `Startup.ConfigureServices`. Uzantı erişim sağlayan bir `PolicyBuilder` olası bir geçici hata temsil eden hataları işlemek için yapılandırılmış nesne:

[!code-csharp[Main](http-requests/samples/Startup.cs?name=snippet7)]

Önceki kodda, bir `WaitAndRetryAsync` İlkesi tanımlanmıştır. Başarısız istekler en fazla 600 ms denemeler arasındaki gecikme ile üç kez yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilkelerini seçin

Polly tabanlı işleyicileri eklemek için kullanılabilecek ek genişletme yöntemleri mevcut. Böyle bir uzantısıdır `AddPolicyHandler`, birden çok aşırı yüklemeleri vardır. Aşırı yüklemelerden birine uygulamak için ilkeyi tanımlarken denetlenecek istek sağlar:

[!code-csharp[Main](http-requests/samples/Startup.cs?name=snippet8)]

Önceki kodda, giden istek bir GET ise 10 saniyelik zaman aşımı uygulanır. Diğer HTTP yöntemi için 30 saniyelik zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyicileri ekleme

Polly ilkeleri gelişmiş işlevsellik sağlamak için iç içe yaygın bir uygulamadır:

[!code-csharp[Main](http-requests/samples/Startup.cs?name=snippet9)]

Önceki örnekte, iki işleyicisi eklenir. İlk kullandığı `AddTransientHttpErrorPolicy` bir yeniden deneme ilkesi eklemek için uzantı. En fazla üç kez başarısız istek yeniden denenir. İçin yapılan ikinci çağrı `AddTransientHttpErrorPolicy` devre kesici ilke ekler. Daha fazla ardışık olarak beş başarısız girişim meydana gelirse dış istekleri 30 saniye engellenir. Devre kesici ilkeleri bilgisi yok. Tüm çağrılar bu istemciyi aynı bağlantı hattı durumu paylaşın.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilkeleri ekleme

Bir kez tanımlayın ve bunları kaydetmek için düzenli olarak kullanılan ilkeleri yönetme yaklaşım, bir `PolicyRegistry`. Kayıt defterinden bir ilke kullanarak eklenecek bir işleyici olanak tanıyan bir genişletme yöntemi sağlanır:

[!code-csharp[Main](http-requests/samples/Startup.cs?name=snippet10)]

Önceki kodda, bir PolicyRegistry eklenen `ServiceCollection` ve iki ilke ile kayıtlı. Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır, uygulanacak ilke adını geçirerek.

Daha fazla bilgi hakkında `IHttpClientFactory` ve Polly tümleştirmeler bulunabilir [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient ve ömür boyu Yönetimi

Her zaman `CreateClient` üzerinde çağrılır `IHttpClientFactory`, yeni bir örneğini bir `HttpClient` döndürülür. Olacaktır bir `HttpMessageHandler` adlandırılmış istemci başına. `IHttpClientFactory` havuzunun `HttpMessageHandler` Fabrika kaynak tüketimini azaltmak için oluşturulan örnekleri. A `HttpMessageHandler` örneği yeniden kullanılabilir havuzundan yeni bir oluştururken `HttpClient` yaşam süresi dolmadıysa örneği. 

Her işleyicisi genellikle kendi temel alınan HTTP bağlantıları yöneten işleyicileri havuzu tercih edilir; gerekenden daha fazla işleyicileri oluşturma bağlantı gecikmelerine neden olabilir. Bazı işleyiciler da bağlantıları açık süresiz olarak, DNS değişiklikleri tepki gelen, işleyici engelleyebilir tutun.

Varsayılan işleyici yaşam süresi iki dakika olmalıdır. Varsayılan değer üzerinde geçersiz kılınabilir bir adlandırılmış istemci temelinde. Geçersiz kılmak için çağrı `SetHandlerLifetime` üzerinde `IHttpClientBuilder` istemci oluştururken döndürülür:

[!code-csharp[Main](http-requests/samples/Startup.cs?name=snippet11)]

## <a name="logging"></a>Günlüğe Kaydetme

İstemcileri aracılığıyla oluşturulan `IHttpClientFactory` tüm isteklere ait günlük iletilerini kaydedin. Varsayılan günlük iletilerini görmek için günlüğü yapılandırmanızda uygun bilgi düzeyi sağlamak gerekir. İstek üst bilgilerinin günlüğe kaydetme gibi ek günlük kaydı sırasında dahil yalnızca izleme düzeyi.

Her istemci için kullanılan günlük kategorisi istemci adını içerir. Örneğin, "MyNamedClient" adlı bir istemci ile kategorisi iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. İletileri "LogicalHandler" soneki ile istek işleyicisi işlem hattının dış oluşur. Herhangi bir işlem hattı işleyicilerindeki işlenen önce istekte, iletileri günlüğe kaydedilir. Yanıtta, herhangi bir işlem hattı işleyicileri yanıt aldığınız sonra iletileri günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi Ardışık düzenin iç de gerçekleşir. Karşı günlük kategorisi "MyNamedClient" örnek söz konusu olduğunda, bu iletileri günlüğe kaydedilen `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`. İstek için tüm işleyicileri çalıştırdıktan sonra ve istek ağda hemen gönderilmeden önce gerçekleşir. Geri işleyici işlem hattı geçirmeden önce yanıtta yanıt durumu bu günlük kaydı içerir.

Dış ve işlem hattı içinde günlük kaydını etkinleştirme, bir işlem hattı işleyicileri tarafından yapılan değişiklikleri incelemesini etkinleştirir. Bu örneğin veya yanıt durum kodu istek üst bilgileri, değişiklik içerebilir.

Günlük kategorisinde istemci adı dahil olmak üzere, gerektiğinde belirli adlandırılmış istemciler için filtreleme günlük sağlar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler yapılandırın

İç'ın yapılandırmasını kontrol gerekebilir `HttpMessageHandler` bir istemci tarafından kullanılmış.

Bir `IHttpClientBuilder` adlı eklerken, veya yazılan istemciler döndürülür. `ConfigurePrimaryHttpMessageHandler` Genişletme yöntemi, bir temsilci tanımlamak için kullanılabilir. Temsilci oluşturmak ve birincil yapılandırmak için kullanılan `HttpMessageHandler` istemci tarafından kullanılan:

[!code-csharp[Main](http-requests/samples/Startup.cs?name=snippet12)]
