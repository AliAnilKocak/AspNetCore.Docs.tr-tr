---
title: ASP.NET Core 3,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 3,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 09/20/2019
uid: aspnetcore-3.0
ms.openlocfilehash: 97703b8d67c148ef6b3ee4a93cdfc07ab970ecd4
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187490"
---
# <a name="whats-new-in-aspnet-core-30"></a>ASP.NET Core 3,0 ' deki yenilikler

Bu makalede, ASP.NET Core 3,0 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.

## <a name="blazor"></a>Blazor

Blazor, .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için ASP.NET Core yeni bir çerçevedir:

* JavaScript yerine zengin etkileşimli Uıusing C# oluşturma.
* .NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.
* Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.

Blazor Framework desteklenen senaryolar:

* Yeniden kullanılabilir kullanıcı arabirimi bileşenleri (Razor bileşenleri)
* İstemci tarafı yönlendirme
* Bileşen düzenleri
* Bağımlılık ekleme desteği
* Formlar ve doğrulama
* Razor sınıf kitaplıkları ile bileşen kitaplıkları derleme
* JavaScript ile birlikte çalışma

Daha fazla bilgi için bkz. <xref:blazor/index>.

### <a name="blazor-server"></a>Blazor Server

Blazor, Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır. Blazor Server, Razor bileşenlerini bir ASP.NET Core uygulamasındaki sunucuda barındırmak için destek sağlar. Kullanıcı Arabirimi güncelleştirmeleri bir SignalR bağlantısı üzerinden işlenir. Blazor sunucusu ASP.NET Core 3,0 ' de desteklenir.

### <a name="blazor-webassembly-preview"></a>Blazor WebAssembly (Önizleme)

Blazor uygulamalar, bir WebAssembly tabanlı .NET çalışma zamanı kullanarak doğrudan tarayıcıda da çalıştırılabilir. Blazor WebAssembly Önizleme *aşamasındadır ve ASP.NET Core 3,0 ' de* desteklenmez. Blazor WebAssembly ASP.NET Core gelecek bir sürümünde desteklenecektir.

### <a name="razor-components"></a>Razor bileşenleri

Blazor uygulamaları bileşenlerinden oluşturulmuştur. Bileşenler, bir sayfa, iletişim kutusu veya form gibi kullanıcı arabirimi (UI) için kendi içinde yer alan öbeklerdir. Bileşenler, Kullanıcı arabirimi işleme mantığını ve istemci tarafı olay işleyicilerini tanımlayan normal .NET sınıflarıdır. JavaScript olmadan zengin etkileşimli Web uygulamaları oluşturabilirsiniz.

Blazor içindeki bileşenler genellikle HTML ve C#doğal bir Blend Razor söz dizimi kullanılarak yazılır. Razor bileşenleri, her ikisi de Razor kullanan Razor Pages ve MVC görünümlerine benzerdir. Bir istek-yanıt modelini temel alan sayfaların ve görünümlerin aksine, bileşenler Kullanıcı arabirimi oluşturmayı işlemek için kullanılır.

## <a name="grpc"></a>gRPC

[GRPC](https://grpc.io/):

* Popüler, yüksek performanslı bir RPC (uzak yordam çağrısı) çerçevesidir.
* , API geliştirmeye yönelik olarak yapılan bir sözleşmenin ilk yaklaşımını sağlar.
* , Gibi modern teknolojiler kullanır:

  * Taşıma için HTTP/2.
  * Arabirim açıklaması dili olarak protokol arabellekleri.
  * İkili serileştirme biçimi.
* Şöyle özellikler sağlar:

  * Kimlik doğrulaması
  * Çift yönlü akış ve akış denetimi.
  * İptal ve zaman aşımları.

ASP.NET Core 3,0 ' deki gRPC işlevselliği şunları içerir:

* [GRPC. aspnetcore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; , GRPC hizmetlerini barındırmak için bir ASP.NET Core çerçevesi. gRPC on ASP.NET Core, günlüğe kaydetme, bağımlılık ekleme (dı), kimlik doğrulama ve yetkilendirme gibi standart ASP.NET Core özelliklerle tümleştirilir.
* [GRPC .net. istemci](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; .NET Core için bir GRPC istemcisini tanıdık `HttpClient`bir şekilde oluşturur.
* [GRPC .net. clientfactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; GRPC istemci tümleştirmesi ile `HttpClientFactory`tümleştirme.

Daha fazla bilgi için bkz. <xref:grpc/index>.

## <a name="signalr"></a>SignalR

Bkz. geçiş yönergeleri için [SignalR kodunu güncelleştirme](xref:migration/22-to-30#signalr) . SignalR artık JSON `System.Text.Json` iletilerini seri hale getirmek/seri durumdan çıkarmak için kullanır. Tabanlı serileştiriciyi geri yükleme `Newtonsoft.Json`yönergeleri için bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson) .

SignalR için JavaScript ve .NET Istemcilerinde otomatik yeniden bağlantı için destek eklenmiştir. Varsayılan olarak, istemci hemen yeniden bağlanmaya çalışır ve gerekirse 2, 10 ve 30 saniye sonra yeniden dener. İstemci başarıyla yeniden bağlanırsa, yeni bir bağlantı KIMLIĞI alır. Otomatik yeniden bağlanma kabul etme:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Yeniden bağlantı aralıkları bir dizi milisaniyelik tabanlı süre geçirerek belirtilebilir:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Yeniden bağlantı aralıklarının tam denetimi için özel bir uygulama geçirilebilir.

Son yeniden bağlanma aralığından sonra yeniden bağlantı başarısız olursa:

* İstemci, bağlantının çevrimdışı olduğunu varsayar.
* İstemci yeniden bağlanmayı denemeyi durduruyor.

Yeniden bağlanma denemeleri sırasında, kullanıcıya yeniden bağlantı denenmekte olduğunu bildirmek için uygulama kullanıcı arabirimini güncelleştirin.

Bağlantı kesildiğinde UI geri bildirimi sağlamak için, SignalR istemci API 'SI aşağıdaki olay işleyicilerini içerecek şekilde genişletilir:

* `onreconnecting`:  Geliştiricilere Kullanıcı arabirimini devre dışı bırakma veya kullanıcıların uygulamanın çevrimdışı olduğunu bilmesini sağlayan bir fırsat sağlar.
* `onreconnected`: Bağlantı kurulduktan sonra geliştiricilere Kullanıcı arabirimini güncelleştirme fırsatı verir.

Aşağıdaki kod, bağlanmaya `onreconnecting` çalışırken kullanıcı arabirimini güncelleştirmek için kullanır:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Aşağıdaki kod, bağlantıda `onreconnected` Kullanıcı arabirimini güncelleştirmek için kullanır:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 3,0 ve üzeri, bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar. Kaynak bir örneğidir `HubInvocationContext`. `HubInvocationContext` Şunları içerir:

* `HubCallerContext`
* Çağrılan hub yönteminin adı.
* Hub yöntemi için bağımsız değişkenler.

Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası uygulamasının aşağıdaki örneğini göz önünde bulundurun. Microsoft hesabı herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları veya kullanıcıların sohbet geçmişlerini görüntüleyebilir. Uygulama belirli kullanıcılardan belirli işlevleri kısıtlayabilir.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

Yukarıdaki kodda `DomainRestrictedRequirement` özel `IAuthorizationRequirement`işlevi görür. `HubInvocationContext` Kaynak parametresi geçirildiğinden iç mantık şunları yapabilir:

* Hub 'ın çağrıldığı bağlamı inceleyin.
* Kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar alın.

Tek tek hub yöntemleri, çalışma zamanında kodun denetlediği ilkenin adı ile birlikte kullanılabilir. İstemciler tek tek hub yöntemlerini çağırmayı denediğinden, `DomainRestrictedRequirement` işleyici çalışır ve yöntemlere erişimi denetler. `DomainRestrictedRequirement` Denetimlerin erişim yöntemine göre:

* Tüm oturum açmış kullanıcılar `SendMessage` yöntemini çağırabilir.
* Yalnızca bir `@jabbr.net` e-posta adresiyle oturum açan kullanıcılar, kullanıcıların geçmişlerini görüntüleyebilir.
* Yalnızca `bob42@jabbr.net` sohbet odasından kullanıcıları Ban yapabilir.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

`DomainRestricted` İlkeyi oluşturmak şunları içerebilir:

* *Startup.cs*' de, yeni ilkeyi ekleme.
* Özel `DomainRestrictedRequirement` gereksinimi parametre olarak belirtin.
* Yetkilendirme `DomainRestricted` ara yazılımı ile kaydetme.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalR hub 'ları [Endpoint Routing](xref:fundamentals/routing)kullanır. SignalR Hub bağlantısı daha önce açık şekilde yapıldı:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Önceki sürümde, geliştiriciler, Razor sayfaları ve hub 'ları çeşitli farklı yerlerde bağlamak için gereklidir. Açık bağlantı, neredeyse aynı bir dizi yönlendirme segmentine neden olur:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR 3,0 hub 'ları, uç nokta yönlendirme aracılığıyla yönlendirilebilir. Uç nokta yönlendirme ile, genellikle tüm yönlendirme ' de `UseRouting`yapılandırılabilir:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3,0 SignalR eklendi:

İstemciden sunucuya akış. İstemciden sunucuya akışla, sunucu tarafı yöntemleri `IAsyncEnumerable<T>` veya `ChannelReader<T>`örneklerinden birini alabilir. Aşağıdaki C# örnekte, `UploadStream` hub 'daki Yöntem istemciden dizelerin akışını alacaktır:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

.NET istemci uygulamaları `IAsyncEnumerable<T>` , yukarıdaki `UploadStream` hub yönteminin `stream` bağımsız `ChannelReader<T>` değişkeni olarak bir ya da örneği geçirebilir.

`for` Döngü tamamlandıktan ve yerel işlev çıktıktan sonra, akış tamamlama gönderilir:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

JavaScript istemci `Subject` uygulamaları, yukarıdaki `UploadStream` hub yönteminin `stream` bağımsız değişkeni için SignalR (veya bir [rxjs Subject](https://rxjs.dev/api/index/class/Subject)) kullanır.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

JavaScript kodu, yakalandıkları ve `subject.next` sunucuya gönderilmeye hazırlanıyor gibi dizeleri işlemek için yöntemini kullanabilir.

```javascript
subject.next("example");
subject.complete();
```

Önceki iki kod parçacığı gibi kodu kullanarak gerçek zamanlı akış deneyimleri oluşturulabilir.

### <a name="new-json-serialization"></a>Yeni JSON serileştirmesi

ASP.NET Core 3,0 artık JSON <xref:System.Text.Json> serileştirme için varsayılan olarak kullanır:

* JSON 'yi zaman uyumsuz olarak okur ve yazar.
* UTF-8 metni için iyileştirilmiştir.
* Genellikle daha yüksek performans `Newtonsoft.Json`.

ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft. JSON tabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Yeni Razor yönergeleri

Aşağıdaki listede yeni Razor yönergeleri yer almaktadır:

* [@attribute](xref:mvc/views/razor#attribute)&ndash; Yönergesiverilenözniteliğioluşturulansayfanın`@attribute` veya görünümün sınıfına uygular. Örneğin, uygulamasında yönetilen Hyper-V konakları olarak eklemek için aşağıdaki yordamı kullanabilirsiniz.
* [@implements](xref:mvc/views/razor#implements)&ndash; Yönergesi,oluşturulansınıf`@implements` için bir arabirim uygular. Örneğin, uygulamasında yönetilen Hyper-V konakları olarak eklemek için aşağıdaki yordamı kullanabilirsiniz.

## <a name="certificate-and-kerberos-authentication"></a>Sertifika ve Kerberos kimlik doğrulaması

Sertifika kimlik doğrulaması şunları gerektirir:

* Sunucu, sertifikaları kabul edecek şekilde yapılandırılıyor.
* Kimlik doğrulama ara yazılımı ' `Startup.Configure`ye ekleme.
* ' De `Startup.ConfigureServices`sertifika kimlik doğrulama hizmeti ekleniyor.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Sertifika kimlik doğrulaması seçenekleri şunları yapabilme:

* Otomatik olarak imzalanan sertifikaları kabul edin.
* Sertifika iptali olup olmadığını denetleyin.
* Profili oluşturulan sertifikanın, içinde doğru kullanım bayrakları olduğunu denetleyin.

Varsayılan bir Kullanıcı sorumlusu, sertifika özelliklerinden oluşturulur. Kullanıcı sorumlusu, sorumlunun takıma veya değiştirilmesine izin veren bir olay içerir. Daha fazla bilgi için bkz. <xref:security/authentication/certauth>.

[Windows kimlik doğrulaması](/windows-server/security/windows-authentication/windows-authentication-overview) , Linux ve MacOS üzerine genişletildi. Önceki sürümlerde, Windows kimlik doğrulaması [IIS](xref:host-and-deploy/iis/index) ve [httpsys](xref:fundamentals/servers/httpsys)ile sınırlandırıldı. ASP.NET Core 3,0 ' de [Kestrel](xref:fundamentals/servers/kestrel) , Windows etki alanına katılmış konaklar için Windows, Linux ve MacOS üzerinde Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)ve [NTLM](/windows-server/security/kerberos/ntlm-overview)kullanma olanağına sahiptir. Bu kimlik doğrulama düzenlerinin Kestrel desteği [Microsoft. AspNetCore. Authentication. Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi tarafından sağlanır. Diğer kimlik doğrulama hizmetlerinde olduğu gibi, kimlik doğrulama uygulaması genelinde yapılandırın ve ardından hizmeti yapılandırın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Ana bilgisayar gereksinimleri:

* Windows Konakları, uygulamayı barındıran Kullanıcı hesabına eklenmiş [hizmet sorumlusu adlarına](/windows/win32/ad/service-principal-names) (SPN) sahip olmalıdır.
* Linux ve macOS makineleri etki alanına katılmalıdır.
  * Web işlemi için SPN oluşturulması gerekir.
  * Ana makinede [keytab dosyalarının](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) oluşturulup yapılandırılması gerekir.

Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Şablon değişiklikleri

Web UI şablonları (Razor Pages, denetleyici ve görünümlerle MVC) şu şekilde kaldırılmıştır:

* Tanımlama bilgisi onayı Kullanıcı arabirimi artık dahil değildir. ASP.NET Core 3,0 şablonu tarafından oluşturulan bir uygulamada tanımlama bilgisi onay özelliğini etkinleştirmek için, bkz <xref:security/gdpr>.
* Betiklerin ve ilgili statik varlıkların artık CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor. Daha fazla bilgi için, bkz. [betiklerin ve ilgili statik varlıkların artık geçerli ortama göre CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).

Angular şablonu, angular 8 ' i kullanacak şekilde güncelleştirildi.

Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirmeyi varsayılan olarak belirler. Visual Studio 'da yeni bir şablon seçeneği, sayfalar ve görünümler için şablon desteği sağlar. Bir komut kabuğunda şablondan RCL oluştururken, ( `-support-pages-and-views` `dotnet new razorclasslib -support-pages-and-views`) seçeneğini geçirin.

## <a name="generic-host"></a>Genel Konak

ASP.NET Core 3,0 şablonları kullanılır <xref:fundamentals/host/generic-host>. Önceki sürümler kullanıldı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. .NET Core Genel Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) kullanımı, Web 'e özgü olmayan diğer sunucu senaryolarıyla ASP.NET Core uygulamaları daha iyi tümleştirme sağlar. Daha fazla bilgi için bkz. [Hostbuilder WebHostBuilder 'ın yerini alır](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Konak yapılandırması

ASP.NET Core 3,0 ' nin yayınlanmasından önce, Web konağının ana bilgisayar `ASPNETCORE_` yapılandırması için, öneki olan ortam değişkenleri yüklendi. 3,0 ' de `AddEnvironmentVariables` , ile `CreateDefaultBuilder`konak yapılandırması `DOTNET_` için önekli ortam değişkenlerini yüklemek için kullanılır.

### <a name="changes-to-startup-contructor-injection"></a>Başlangıç özniteliği Oluşturucu Ekleme değişiklikleri

Genel konak yalnızca Oluşturucu ekleme için `Startup` aşağıdaki türleri destekler:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Tüm hizmetler yine de `Startup.Configure` yönteme bağımsız değişken olarak eklenebilir. Daha fazla bilgi için bkz. [genel ana bilgisayar başlangıç Oluşturucu Ekleme (ASPNET/duyurular #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* Kestrel yapılandırması, genel konağa geçiş için güncelleştirildi. 3,0 ' de, Kestrel tarafından `ConfigureWebHostDefaults`sağlanmış Web ana bilgisayar Oluşturucu üzerinde yapılandırılır.
* Bağlantı bağdaştırıcıları, Kestrel adresinden kaldırılmıştır ve bağlantı ara yazılımı ile değiştirilmiştir ve bu, ASP.NET Core işlem hattındaki HTTP ara hattına benzer ancak alt düzey bağlantılar için kullanılır.
* Kestrel aktarım katmanı, içinde `Connections.Abstractions`ortak bir arabirim olarak kullanıma sunuldu.
* Sondaki üstbilgiler yeni bir koleksiyona taşınarak üstbilgiler ve tanıtımları arasındaki belirsizlik çözüldü.
* Gibi zaman uyumlu GÇ API 'leri `HttpReqeuest.Body.Read`, uygulama kilitlenmelerine neden olan yaygın bir iş parçacığı kaynağıdır. 3,0 ' `AllowSynchronousIO` de varsayılan olarak devre dışıdır.

Daha fazla bilgi için bkz. <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>Varsayılan olarak etkin HTTP/2

HTTP/2, HTTPS uç noktaları için Kestrel içinde varsayılan olarak etkindir. IIS veya HTTP. sys için HTTP/2 desteği, işletim sistemi tarafından desteklendikleri zaman etkindir.

## <a name="request-counters"></a>İstek sayaçları

Barındırmada EventSource (Microsoft. AspNetCore. Hosting), gelen isteklerle ilgili aşağıdaki EventCounters ' i yayar:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Uç nokta yönlendirme

Çerçeveler 'in (örneğin, MVC) ara yazılım ile iyi çalışmasına izin veren uç nokta yönlendirme geliştirildi:

* Ara yazılım ve uç noktaların sırası istek işleme ardışık düzeninde `Startup.Configure`yapılandırılabilir.
* Uç noktalar ve ara yazılımlar, sistem durumu denetimleri gibi diğer ASP.NET Core tabanlı teknolojilerle birlikte.
* Uç noktalar, hem yazılım hem de MVC 'de CORS veya yetkilendirme gibi bir ilke uygulayabilir.
* Filtreler ve öznitelikler, denetleyicilerde yöntemler üzerine yerleştirilebilir.

Daha fazla bilgi için bkz. <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Sistem durumu denetimleri

Sistem durumu denetimleri, genel ana bilgisayar ile Endpoint Routing kullanır. ' `Startup.Configure`De, `MapHealthChecks` uç nokta URL 'si veya göreli yol ile Endpoint Builder ' ı çağırın:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Durum denetimleri uç noktaları şunları yapabilir:

* İzin verilen bir veya daha fazla Konakları/bağlantı noktasını belirtin.
* Yetkilendirme gerektir.
* CORS gerektir.

Daha fazla bilgi için aşağıdaki makalelere bakın:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>HttpContext üzerindeki kanallar

Artık istek gövdesini okumak ve yanıt gövdesini <xref:System.IO.Pipelines> API kullanarak yazmak mümkündür. Bu <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`özelliği, istek <xref:System.IO.Pipelines.PipeReader> gövdesini okumak için kullanılabilecek bir sağlar. Bu <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`özelliği, yanıt <xref:System.IO.Pipelines.PipeWriter> gövdesini yazmak için kullanılabilecek bir sağlar. `HttpRequest.BodyReader``HttpRequest.Body` akışın analog bir kadır. `HttpResponse.BodyWriter``HttpResponse.Body` akışın analog bir kadır.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>IIS 'de geliştirilmiş hata raporlama

IIS 'de ASP.NET Core uygulamalar barındırırken başlatma hataları artık daha zengin Tanılama verileri oluşturuyor. Bu hatalar, geçerli her yerde yığın izlemelerle Windows olay günlüğü 'ne bildirilir. Ayrıca, tüm uyarılar, hatalar ve işlenmemiş özel durumlar Windows olay günlüğü 'ne kaydedilir.

## <a name="worker-service-and-worker-sdk"></a>Çalışan hizmeti ve çalışan SDK 'Sı

.NET Core 3,0 yeni çalışan hizmeti uygulama şablonunu tanıtır. Bu şablon, .NET Core 'da uzun süre çalışan hizmetler yazmak için bir başlangıç noktası sağlar.

Daha fazla bilgi için bkz.:

* [Windows Hizmetleri olarak .NET Core çalışanları](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>İletilen üstbilgiler ara yazılımı geliştirmeleri

Önceki ASP.NET Core sürümlerinde, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bir Azure Linux 'a dağıtılırken veya IIS dışında herhangi bir ters proxy 'nin arkasında sorun. Önceki sürümlere yönelik düzeltmeler, [Linux ve IIS olmayan ters proxy 'lerin düzenini iletme](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)bölümünde belgelenmiştir.

Bu senaryo ASP.NET Core 3,0 ' de düzeltilmiştir. `ASPNETCORE_FORWARDEDHEADERS_ENABLED` Ortam değişkeni olarak`true`ayarlandığında, ana bilgisayar [iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) sağlar. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`, kapsayıcı görüntülerimizde olarak `true` ayarlanır.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3,0 yalnızca .NET Core 3,0 üzerinde çalışır

ASP.NET Core 3,0 itibariyle, .NET Framework artık desteklenen bir hedef çerçeve değildir. .NET Framework hedefleyen projeler [.NET Core 2,1 LTS sürümünü](https://www.microsoft.com/net/download/dotnet-core/2.1)kullanarak tam olarak desteklenen bir biçimde devam edebilir. ASP.NET Core 2.1. x ile ilgili paketlerin çoğu, .NET Core 2,1 için 3 yıllık LTS döneminin ötesinde süresiz olarak desteklenecektir.

Geçiş bilgileri için bkz. [kodunuzu .NET Core 'a .NET Framework](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>ASP.NET Core paylaşılan çerçevesini kullanma

[Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunan ASP.NET Core 3,0 paylaşılan çerçevesi artık proje dosyasında açık `<PackageReference />` bir öğe gerektirmez. Proje dosyasında `Microsoft.NET.Sdk.Web` SDK kullanılırken paylaşılan çerçeveye otomatik olarak başvurulur:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>ASP.NET Core paylaşılan çerçevesinden kaldırılan derlemeler

ASP.NET Core 3,0 paylaşılan çerçevesinden çıkarılan en önemli derlemeler şunlardır:

* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net). ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft. JSON tabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3,0, `System.Text.Json` JSON okuma ve yazma için tanıtılmıştır. Daha fazla bilgi için bu belgede [yenı JSON serileştirmesi](#new-json-serialization) bölümüne bakın.
* [Entity Framework Core](/ef/core/)

Paylaşılan çerçeveden kaldırılan derlemelerin tamamen listesi için bkz [. Microsoft. AspNetCore. App 3,0 ' den kaldırılan derlemeler](https://github.com/aspnet/AspNetCore/issues/3755). Bu değişiklik için mosyon hakkında daha fazla bilgi için bkz. [3,0 'de Microsoft. AspNetCore. app 'e yönelik son değişiklikler](https://github.com/aspnet/Announcements/issues/325) ve [ASP.NET Core 3,0 ' de gelen değişikliklere ilk bakış](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
