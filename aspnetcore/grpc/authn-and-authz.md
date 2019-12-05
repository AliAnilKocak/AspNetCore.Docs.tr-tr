---
title: ASP.NET Core için gRPC 'de kimlik doğrulaması ve yetkilendirme
author: jamesnk
description: ASP.NET Core için gRPC 'de kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/13/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 84903ee781588ff525d1dfce6a313e3867794762
ms.sourcegitcommit: 76d7fff62014c3db02564191ab768acea00f1b26
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852707"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>ASP.NET Core için gRPC 'de kimlik doğrulaması ve yetkilendirme

, [James bAyKiNg](https://twitter.com/jamesnk)

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>GRPC hizmetini çağıran kullanıcıların kimliğini doğrulama

gRPC, bir kullanıcıyı her çağrıyla ilişkilendirmek için [ASP.NET Core kimlik doğrulamasıyla](xref:security/authentication/identity) birlikte kullanılabilir.

Aşağıda, gRPC ve ASP.NET Core kimlik doğrulaması kullanan `Startup.Configure` bir örneği verilmiştir:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> ASP.NET Core kimlik doğrulama ara yazılımını kaydetme sırası önemli. `UseRouting` ve `UseEndpoints`önce `UseAuthentication` ve `UseAuthorization` her zaman çağırın.

Bir çağrı sırasında uygulamanızın kullandığı kimlik doğrulama mekanizması yapılandırılmalıdır. Kimlik doğrulama yapılandırması `Startup.ConfigureServices` eklenir ve uygulamanızın kullandığı kimlik doğrulama mekanizmasına bağlı olarak farklı olacaktır. ASP.NET Core uygulamaları güvenli hale getirmeye yönelik örnekler için bkz. [kimlik doğrulama örnekleri](xref:security/authentication/samples).

Kimlik doğrulaması kurulduktan sonra, kullanıcıya `ServerCallContext`aracılığıyla bir gRPC hizmeti yöntemleriyle erişilebilir.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Taşıyıcı belirteç kimlik doğrulaması

İstemci, kimlik doğrulaması için bir erişim belirteci sağlayabilir. Sunucu belirteci doğrular ve kullanıcıyı tanımlamak için kullanır.

Sunucusunda, taşıyıcı belirteç kimlik doğrulaması [JWT taşıyıcı ara yazılımı](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)kullanılarak yapılandırılır.

.NET gRPC istemcisinde, belirteç aramalar ile üst bilgi olarak gönderilebilir:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

Bir kanalda `ChannelCredentials` yapılandırmak, belirteci gRPC çağrılarıyla hizmete göndermenin alternatif bir yoludur. Kimlik bilgisi her bir gRPC çağrısının yapılışında çalıştırılır. Bu, belirteci kendi kendinize geçirmek için birden çok yere kod yazma gereksinimini ortadan kaldırır.

Aşağıdaki örnekteki kimlik bilgileri, kanalı her gRPC çağrısıyla birlikte gönderecek şekilde yapılandırır:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>İstemci sertifikası kimlik doğrulaması

İstemci alternatif olarak kimlik doğrulaması için bir istemci sertifikası sağlayabilir. [Sertifika kimlik doğrulaması](https://tools.ietf.org/html/rfc5246#section-7.4.4) TLS düzeyinde gerçekleşir ve bu süre ASP.NET Core. İstek ASP.NET Core girdiğinde, [istemci sertifikası kimlik doğrulama paketi](xref:security/authentication/certauth) , sertifikayı bir `ClaimsPrincipal`çözmenize olanak tanır.

> [!NOTE]
> Konağın istemci sertifikalarını kabul edecek şekilde yapılandırılması gerekir. Bkz. Kestrel, IIS ve Azure 'da istemci sertifikalarını kabul etme hakkında bilgi için bkz. [ana bilgisayarınızı yapılandırma](xref:security/authentication/certauth#configure-your-host-to-require-certificates) .

.NET gRPC istemcisinde istemci sertifikası, daha sonra gRPC istemcisini oluşturmak için kullanılan `HttpClientHandler` eklenir:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Diğer kimlik doğrulama mekanizmaları

Desteklenen birçok ASP.NET Core kimlik doğrulama mekanizması gRPC ile çalışır:

* Azure Active Directory
* İstemci sertifikası
* IdentityServer
* JWT belirteci
* OAuth 2.0
* OpenID Connect
* WS-Federation

Sunucuda kimlik doğrulamasını yapılandırma hakkında daha fazla bilgi için, [ASP.NET Core kimlik doğrulaması](xref:security/authentication/identity)' na bakın.

GRPC istemcisini kimlik doğrulaması kullanacak şekilde yapılandırmak, kullanmakta olduğunuz kimlik doğrulama mekanizmasına bağlı olarak değişir. Önceki taşıyıcı belirteci ve istemci sertifikası örnekleri, GRPC istemcisinin, gRPC çağrılarına yönelik kimlik doğrulama meta verilerini gönderecek şekilde yapılandırılabilmesinin birkaç yolunu gösterir:

* Türü kesin belirlenmiş gRPC istemcileri dahili olarak `HttpClient` kullanır. Kimlik doğrulaması [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)veya `HttpClient`özel [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) örnekleri ekleyerek yapılandırılabilir.
* Her gRPC çağrısının isteğe bağlı bir `CallOptions` bağımsız değişkeni vardır. Özel üstbilgiler, seçeneğin üstbilgiler koleksiyonu kullanılarak gönderilebilir.

> [!NOTE]
> Windows kimlik doğrulaması (NTLM/Kerberos/Negotiate), gRPC ile kullanılamaz. gRPC için HTTP/2 ve HTTP/2 Windows kimlik doğrulamasını desteklemez.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Kullanıcılara hizmetlere ve hizmet yöntemlerine erişim yetkisi verme

Varsayılan olarak, bir hizmette tüm yöntemler kimliği doğrulanmamış kullanıcılar tarafından çağrılabilir. Kimlik doğrulaması gerektirmek için, [[Yetkilendir]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliğini hizmete uygulayın:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Yalnızca belirli [Yetkilendirme ilkeleriyle](xref:security/authorization/policies)eşleşen kullanıcılara erişimi kısıtlamak için `[Authorize]` özniteliğinin Oluşturucu bağımsız değişkenlerini ve özelliklerini kullanabilirsiniz. Örneğin, `MyAuthorizationPolicy`adlı bir özel yetkilendirme ilkeniz varsa, aşağıdaki kodu kullanarak yalnızca bu ilkeyle eşleşen kullanıcıların hizmete erişebildiğinden emin olun:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Tek tek hizmet yöntemlerinde `[Authorize]` özniteliği de uygulanabilir. Geçerli Kullanıcı hem yönteme hem **de** sınıfa uygulanan ilkelerle eşleşmezse, çağırana bir hata döndürülür:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 'de taşıyıcı belirteç kimlik doğrulaması](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [ASP.NET Core 'de Istemci sertifikası kimlik doğrulamasını yapılandırma](xref:security/authentication/certauth)
