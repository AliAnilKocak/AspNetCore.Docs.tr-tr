---
title: ASP.NET Core SignalR 'de kimlik doğrulaması ve yetkilendirme
author: bradygaster
description: ASP.NET Core SignalR 'de kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/17/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: 258b6d92896d38b79116278abb7c70b6063e8131
ms.sourcegitcommit: ce2bfb01f2cc7dd83f8a97da0689d232c71bcdc4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531172"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>ASP.NET Core SignalR 'de kimlik doğrulaması ve yetkilendirme

, [Andrew Stanton-nurte](https://twitter.com/anurse)

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Bir SignalR hub 'ına bağlanan kullanıcıların kimliğini doğrulama

SignalR, bir kullanıcıyı her bağlantıyla ilişkilendirmek için [ASP.NET Core kimlik doğrulamasıyla](xref:security/authentication/identity) birlikte kullanılabilir. Bir hub 'da, kimlik doğrulama verilerine [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) özelliğinden erişilebilir. Kimlik doğrulaması, hub 'ın bir kullanıcıyla ilişkili tüm bağlantılar üzerinde Yöntemler çağırmasını sağlar. Daha fazla bilgi için bkz. [SignalR 'de kullanıcıları ve grupları yönetme](xref:signalr/groups). Birden çok bağlantı tek bir kullanıcıyla ilişkilendirilebilir.

Aşağıda, SignalR ve ASP.NET Core kimlik doğrulaması kullanan `Startup.Configure` bir örneği verilmiştir:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> SignalR ve ASP.NET Core kimlik doğrulama ara yazılımını kaydetme sırası önemli. SignalR 'nin `HttpContext` bir kullanıcısı olması için `UseSignalR` önce her zaman `UseAuthentication` çağırın.

::: moniker-end

### <a name="cookie-authentication"></a>Tanımlama bilgisi kimlik doğrulaması

Tarayıcı tabanlı bir uygulamada, tanımlama bilgisi kimlik doğrulaması, mevcut kullanıcı kimlik bilgilerinizin SignalR bağlantılarına otomatik olarak akmasını sağlar. Tarayıcı istemcisi kullanılırken ek yapılandırma gerekmez. Kullanıcı uygulamanızda oturum açtıysa, SignalR bağlantısı bu kimlik doğrulamasını otomatik olarak devralır.

Tanımlama bilgileri, erişim belirteçleri göndermek için tarayıcıya özgü bir yoldur, ancak tarayıcı olmayan istemciler bunları gönderebilir. [.NET istemcisi](xref:signalr/dotnet-client)kullanılırken, tanımlama bilgisi sağlamak için `.WithUrl` çağrısında `Cookies` özelliği yapılandırılabilir. Ancak, .NET istemcisinden tanımlama bilgisi kimlik doğrulamasını kullanmak, uygulamanın bir tanımlama bilgisi için kimlik doğrulama verilerini Exchange için bir API sağlamasını gerektirir.

### <a name="bearer-token-authentication"></a>Taşıyıcı belirteç kimlik doğrulaması

İstemci, tanımlama bilgisi kullanmak yerine bir erişim belirteci sağlayabilir. Sunucu belirteci doğrular ve kullanıcıyı tanımlamak için kullanır. Bu doğrulama yalnızca bağlantı kurulduunda yapılır. Bağlantının kullanım ömrü boyunca, sunucu otomatik olarak yeniden doğrulamadan belirteç iptalini kontrol etmez.

Sunucusunda, taşıyıcı belirteç kimlik doğrulaması [JWT taşıyıcı ara yazılımı](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)kullanılarak yapılandırılır.

JavaScript istemcisinde, belirteç [Accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) seçeneği kullanılarak sağlanabilirler.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

.NET istemcisinde, belirteci yapılandırmak için kullanılabilecek, benzer bir [Accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) özelliği vardır:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Sağladığınız erişim belirteci işlevi, SignalR tarafından yapılan **her** http isteğinden önce çağırılır. Bağlantıyı etkin tutmak için belirteci yenilemeniz gerekiyorsa (bağlantı sırasında süresi dolarsa), bunu bu işlevin içinden yapın ve güncelleştirilmiş belirteci döndürün.

Standart Web API 'Lerinde, taşıyıcı belirteçleri bir HTTP üst bilgisinde gönderilir. Ancak, SignalR bazı aktarımlar kullanılırken tarayıcılarda bu üst bilgileri kuramıyor. WebSockets ve sunucu tarafından gönderilen olaylar kullanılırken, belirteç bir sorgu dizesi parametresi olarak iletilir. Bunu sunucuda desteklemek için ek yapılandırma gerekir:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> Sorgu dizesi tarayıcı API 'SI sınırlamaları nedeniyle WebSockets ve sunucu tarafından gönderilen olaylarla bağlantı kurulurken tarayıcılarda kullanılır. HTTPS kullanılırken sorgu dizesi değerleri TLS bağlantısıyla korunmuş hale getirilir. Ancak, birçok sunucu günlük sorgu dizesi değerlerini günlüğe kaydeder. Daha fazla bilgi için bkz. [ASP.NET Core SignalR Içindeki güvenlik konuları](xref:signalr/security). SignalR, bunları destekleyen ortamlarda (.NET ve Java istemcileri gibi) belirteçleri iletmek için üst bilgileri kullanır.

### <a name="cookies-vs-bearer-tokens"></a>Tanımlama bilgileri ve taşıyıcı belirteçleri karşılaştırması 

Tanımlama bilgileri tarayıcılara özeldir. Diğer istemci türlerinden gönderilmesi, taşıyıcı belirteçlerinin gönderilmesine kıyasla karmaşıklık ekler. Sonuç olarak, uygulamanın yalnızca tarayıcı istemcisinden kullanıcıların kimliğini doğrulaması gerekmediği takdirde tanımlama bilgisi kimlik doğrulaması önerilmez. Taşıyıcı belirteç kimlik doğrulaması, tarayıcı istemcisi dışındaki istemcileri kullanırken önerilen yaklaşımdır.

### <a name="windows-authentication"></a>Windows kimlik doğrulaması

Uygulamanızda [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) yapılandırıldıysa, SignalR hub 'ları güvenli hale getirmek için bu kimliği kullanabilir. Ancak, bireysel kullanıcılara ileti göndermek için özel bir kullanıcı KIMLIĞI sağlayıcısı eklemeniz gerekir. Windows kimlik doğrulama sistemi, "ad tanımlayıcı" talebi sağlamaz. SignalR Kullanıcı adını öğrenmek için talebi kullanır.

@No__t_0 uygulayan yeni bir sınıf ekleyin ve Kullanıcı tarafından tanımlayıcı olarak kullanılacak taleplerden birini alın. Örneğin, "ad" talebini (`[Domain]\[Username]` biçimde Windows Kullanıcı adı) kullanmak için aşağıdaki sınıfı oluşturun:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

@No__t_0 yerine, `User` herhangi bir değeri (Windows SID tanımlayıcısı gibi) kullanabilirsiniz.

> [!NOTE]
> Seçtiğiniz değer, sisteminizdeki tüm kullanıcılar arasında benzersiz olmalıdır. Aksi takdirde, bir kullanıcı için tasarlanan bir ileti farklı bir kullanıcıya gidiyor olabilir.

Bu bileşeni `Startup.ConfigureServices` yöntemine kaydedin.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

.NET Istemcisinde, [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) özelliği ayarlanarak Windows kimlik doğrulamasının etkinleştirilmesi gerekir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Windows kimlik doğrulaması yalnızca Microsoft Internet Explorer veya Microsoft Edge kullanılırken tarayıcı istemcisi tarafından desteklenir.

### <a name="use-claims-to-customize-identity-handling"></a>Kimlik işlemeyi özelleştirmek için talepler kullanma

Kullanıcıların kimliğini doğrulayan bir uygulama, Kullanıcı taleplerinden bir SignalR Kullanıcı kimliği türetilebilir. SignalR 'nin Kullanıcı kimlikleri nasıl oluşturduğunu belirtmek için `IUserIdProvider` uygulayın ve uygulamayı kaydedin.

Örnek kod, tanımlama özelliği olarak kullanıcının e-posta adresini seçmek için talepleri nasıl kullanacağınızı gösterir. 

> [!NOTE]
> Seçtiğiniz değer, sisteminizdeki tüm kullanıcılar arasında benzersiz olmalıdır. Aksi takdirde, bir kullanıcı için tasarlanan bir ileti farklı bir kullanıcıya gidiyor olabilir.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Hesap kaydı, ASP.NET Identity veritabanına `ClaimsTypes.Email` türünde bir talep ekler.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Bu bileşeni `Startup.ConfigureServices` kaydedin.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Kullanıcılara, hub 'lara ve hub yöntemlerine erişim yetkisi verme

Varsayılan olarak, bir hub 'daki tüm yöntemler kimliği doğrulanmamış bir kullanıcı tarafından çağrılabilir. Kimlik doğrulaması gerektirmek için, [Yetkilendir](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) özniteliğini hub 'a uygulayın:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Yalnızca belirli [Yetkilendirme ilkeleriyle](xref:security/authorization/policies)eşleşen kullanıcılara erişimi kısıtlamak için `[Authorize]` özniteliğinin Oluşturucu bağımsız değişkenlerini ve özelliklerini kullanabilirsiniz. Örneğin, `MyAuthorizationPolicy` adlı bir özel yetkilendirme ilkeniz varsa, aşağıdaki kodu kullanarak yalnızca bu ilkeyle eşleşen kullanıcıların hub 'a erişmesini sağlayabilirsiniz:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Tek tek hub yöntemlerinde `[Authorize]` özniteliği de uygulanabilir. Geçerli Kullanıcı, yönteme uygulanan ilkeyle eşleşmezse, çağırana bir hata döndürülür:

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Hub yöntemi yetkilendirmesini özelleştirmek için yetkilendirme işleyicilerini kullanma

SignalR, bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar. Kaynak bir `HubInvocationContext` örneğidir. @No__t_0, `HubCallerContext`, çağrılan hub yönteminin adını ve hub yönteminin bağımsız değişkenlerini içerir.

Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası örneğini göz önünde bulundurun. Microsoft hesabı olan herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları yasaklatabilecek veya kullanıcıların sohbet geçmişlerini görüntüleyebilmelidir. Ayrıca, belirli kullanıcılardan belirli işlevleri kısıtlamak isteyebilirsiniz. ASP.NET Core 3,0 ' deki güncelleştirilmiş özellikleri kullanarak bu tamamen mümkündür. @No__t_0 nasıl özel bir `IAuthorizationRequirement` görevi gördüğüne göz önünde edin. Artık `HubInvocationContext` kaynak parametresi geçirildiğinden, iç mantık hub 'ın çağrıldığı bağlamı inceleyebilir ve kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar verebilir.

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

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

@No__t_0, yeni ilkeyi ekleyerek `DomainRestricted` ilkesini oluşturmak için parametre olarak özel `DomainRestrictedRequirement` gereksinimini sağlar.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

Yukarıdaki örnekte `DomainRestrictedRequirement` sınıfı, bu gereksinim için hem `IAuthorizationRequirement` hem de kendi `AuthorizationHandler`. Bu iki bileşeni birbirinden ayrı ayrı sınıflara bölmek kabul edilebilir. Örneğin yaklaşımın bir avantajı, gereksinim ve işleyicinin aynı şey olduğu için başlangıç sırasında `AuthorizationHandler` eklemeye gerek yoktur.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 'de taşıyıcı belirteç kimlik doğrulaması](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased)
