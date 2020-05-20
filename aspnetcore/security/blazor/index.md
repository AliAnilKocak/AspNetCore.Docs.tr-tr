---
Başlık: ' ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme ' Yazar: Açıklama: ' Blazor kimlik doğrulama ve yetkilendirme senaryoları hakkında bilgi edinin. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme

Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)

ASP.NET Core, uygulamalardaki güvenliğin yapılandırmasını ve yönetimini destekler Blazor .

Güvenlik Senaryoları Blazor sunucu ve Blazor webassembly uygulamaları arasında farklılık gösterir. Sunucu Blazor uygulamaları sunucuda çalıştığı için, yetkilendirme denetimleri şunları tespit edebilir:

* Kullanıcıya sunulan kullanıcı ARABIRIMI seçenekleri (örneğin, bir kullanıcı için hangi menü girişlerinin kullanılabildiği).
* Uygulama ve bileşenlerin bölgeleri için erişim kuralları.

BlazorWebAssembly uygulamaları istemcide çalışır. Yetkilendirme *yalnızca* hangi kullanıcı arabirimi seçeneklerinin gösterileceğini belirlemede kullanılır. İstemci tarafı denetimleri bir kullanıcı tarafından değiştirilebilecek veya atlandığından, Blazor webassembly uygulaması yetkilendirme erişim kurallarını zorunlu kılamaz.

[ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization) yönlendirilebilir Razor bileşenlere uygulanmaz. Bir sayfada yönlendirilemeyen bir Razor bileşen [gömüliyorsa](xref:blazor/integrate-components#render-components-from-a-page-or-view), sayfanın yetkilendirme kuralları, Razor sayfanın geri kalanı ile birlikte, bileşeni dolaylı olarak etkiler.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>ve <xref:Microsoft.AspNetCore.Identity.UserManager%601> Razor bileşenlerde desteklenmez.

## <a name="authentication"></a>Kimlik Doğrulaması

Blazor, kullanıcının kimliğini kurmak için mevcut ASP.NET Core kimlik doğrulama mekanizmalarını kullanır. Tam mekanizma Blazor , uygulamanın nasıl barındırıldığını, Blazor webassembly veya Server 'a bağlıdır Blazor .

### <a name="blazor-webassembly-authentication"></a>BlazorWebAssembly kimlik doğrulaması

BlazorWebassembly uygulamalarında, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için kimlik doğrulama denetimleri atlanabilir. Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.

Aşağıdakileri ekleyin:

* Uygulamanın proje dosyasına [Microsoft. AspNetCore. components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) için bir paket başvurusu.
* `Microsoft.AspNetCore.Components.Authorization`Uygulamanın *_Imports. Razor* dosyasının ad alanı.

Kimlik doğrulamasını işlemek için, yerleşik veya özel bir `AuthenticationStateProvider` hizmetin uygulanması aşağıdaki bölümlerde ele alınmıştır.

Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:security/blazor/webassembly/index> ..

### <a name="blazor-server-authentication"></a>BlazorSunucu kimlik doğrulaması

BlazorSunucu uygulamaları kullanılarak oluşturulan gerçek zamanlı bir bağlantı üzerinden çalışır SignalR . Bağlantı kurulurken [kimlik doğrulaması SignalR tabanlı uygulamalar](xref:signalr/authn-and-authz) işlenir. Kimlik doğrulaması, bir tanımlama bilgisine veya başka bir taşıyıcı belirtecine dayalı olabilir.

Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:security/blazor/server/index> ..

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider hizmeti

Yerleşik `AuthenticationStateProvider` hizmet ASP.NET Core ' den kimlik doğrulama durumu verileri alır `HttpContext.User` . Kimlik doğrulama durumu, mevcut ASP.NET Core kimlik doğrulama mekanizmalarıyla tümleştirilir.

`AuthenticationStateProvider`, `AuthorizeView` bileşen ve `CascadingAuthenticationState` bileşen tarafından kimlik doğrulama durumunu almak için kullanılan temel hizmettir.

Genellikle doğrudan kullanmazsınız `AuthenticationStateProvider` . Bu makalenin ilerleyen kısımlarında açıklanan [Authorizeview bileşenini](#authorizeview-component) veya [görev \< authenticationstate>](#expose-the-authentication-state-as-a-cascading-parameter) yaklaşımlarını kullanın. Doğrudan kullanmanın ana dezavantajı, `AuthenticationStateProvider` temeldeki kimlik doğrulama durumu verileri değişirse bileşen tarafından otomatik olarak bildirilmemektedir.

`AuthenticationStateProvider`Hizmet, <xref:System.Security.Claims.ClaimsPrincipal> Aşağıdaki örnekte gösterildiği gibi geçerli kullanıcının verilerini sağlayabilir:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

`user.Identity.IsAuthenticated`İse `true` ve Kullanıcı bir ise <xref:System.Security.Claims.ClaimsPrincipal> , talepler numaralandırılabilir ve değerlendirilen rollerde üyelik yapılabilir.

Bağımlılık ekleme (dı) ve hizmetleri hakkında daha fazla bilgi için bkz <xref:blazor/dependency-injection> <xref:fundamentals/dependency-injection> . ve.

## <a name="implement-a-custom-authenticationstateprovider"></a>Özel bir AuthenticationStateProvider uygulama

Uygulama için özel bir sağlayıcı gerekiyorsa, uygulayın `AuthenticationStateProvider` ve geçersiz kılın `GetAuthenticationStateAsync` :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

BlazorWebassembly uygulamasında, `CustomAuthStateProvider` hizmet program.cs ' de kaydedilir `Main` : *Program.cs*

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Bir Blazor sunucu uygulamasında `CustomAuthStateProvider` hizmet şu şekilde kaydedilir `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider`Önceki örnekte kullanarak, tüm kullanıcıların Kullanıcı adı ile kimliği doğrulanır `mrfibuli` .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Kimlik doğrulama durumunu basamaklı bir parametre olarak kullanıma sunma

Kullanıcı tarafından tetiklenen bir eylem gerçekleştirilirken gibi yordamsal mantık için kimlik doğrulama durumu verileri gerekliyse, türü geçişli bir parametre tanımlayarak kimlik doğrulama durumu verilerini alın `Task<AuthenticationState>` :

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

`user.Identity.IsAuthenticated`İse `true` , talepler, değerlendirilen roller içinde numaralandırılabilir ve üyelenebilir.

`Task<AuthenticationState>` `AuthorizeRouteView` Bileşen içindeki ve bileşenlerini kullanarak basamaklı parametreyi ayarlayın `CascadingAuthenticationState` `App` (*app. Razor*):

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

BlazorWebassembly uygulamasında, Seçenekler ve yetkilendirme için Hizmetleri şu şekilde ekleyin `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Sunucu uygulamasında Blazor , Seçenekler ve yetkilendirme hizmetleri zaten mevcuttur, bu nedenle başka bir eylem gerekmez.

## <a name="authorization"></a>Yetkilendirme

Bir kullanıcının kimliği doğrulandıktan sonra, kullanıcının neler yapabileceğini denetlemek için *Yetkilendirme* kuralları uygulanır.

Erişim, genellikle aşağıdakileri yapıp verilmeksizin verilir veya reddedilir:

* Bir kullanıcının kimliği doğrulanır (oturum açıldı).
* Bir Kullanıcı bir *roldür*.
* Bir kullanıcının *talebi*vardır.
* Bir *ilke* karşılandı.

Bu kavramların her biri, ASP.NET Core MVC veya Pages uygulamasındaki ile aynıdır Razor . ASP.NET Core güvenliği hakkında daha fazla bilgi için [ASP.NET Core güvenlik ve Identity ](xref:security/index)altındaki makalelere bakın.

## <a name="authorizeview-component"></a>AuthorizeView bileşeni

`AuthorizeView`Bileşen Kullanıcı tarafından görme yetkisine sahip olup olmadığına bağlı olarak Kullanıcı arabirimini seçmeli olarak görüntüler. Bu yaklaşım yalnızca Kullanıcı için veri *görüntülemesi* gerektiğinde ve Kullanıcı kimliğini yordamsal mantığda kullanmanıza gerek olmadığında yararlıdır.

Bileşeni, `context` `AuthenticationState` oturum açmış kullanıcıyla ilgili bilgilere erişmek için kullanabileceğiniz türünde bir değişken kullanıma sunar:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Kullanıcının kimliği doğrulanmadıysa, görüntülenmek üzere farklı içerikler de sağlayabilirsiniz:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

`AuthorizeView`Bileşen, `NavMenu` bir liste öğesi () göstermek için bileşende (*Shared/navmenu. Razor*) kullanılabilir `<li>...</li>` `NavLink` , ancak bu yaklaşımın yalnızca liste öğesini işlenen çıktıdan kaldırdığını unutmayın. Kullanıcının bileşene gitmesini engellemez.

`<Authorized>`Ve `<NotAuthorized>` etiketlerinin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.

Kullanıcı arabirimi seçeneklerini veya erişimini denetleyen roller veya ilkeler gibi yetkilendirme koşulları, [Yetkilendirme](#authorization) bölümünde ele alınmıştır.

Yetkilendirme koşulları belirtilmemişse, `AuthorizeView` varsayılan bir ilke kullanır ve şu şekilde davranır:

* Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.
* Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.

### <a name="role-based-and-policy-based-authorization"></a>Rol tabanlı ve ilke tabanlı yetkilendirme

`AuthorizeView`Bileşen *rol tabanlı* veya *ilke tabanlı* yetkilendirmeyi destekler.

Rol tabanlı yetkilendirme için `Roles` parametresini kullanın:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Daha fazla bilgi için bkz. <xref:security/authorization/roles>.

İlke tabanlı yetkilendirme için `Policy` parametresini kullanın:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Talep tabanlı yetkilendirme, ilke tabanlı yetkilendirme için özel bir durumdur. Örneğin, kullanıcıların belirli bir talebe sahip olmasını gerektiren bir ilke tanımlayabilirsiniz. Daha fazla bilgi için bkz. <xref:security/authorization/policies>.

Bu API 'Ler, Blazor sunucuda ya da Blazor webassembly uygulamalarında kullanılabilir.

Ne `Roles` de `Policy` belirtilmemişse, `AuthorizeView` varsayılan ilkeyi kullanır.

### <a name="content-displayed-during-asynchronous-authentication"></a>Zaman uyumsuz kimlik doğrulaması sırasında görünen içerik

Blazorkimlik doğrulaması durumunun *zaman uyumsuz*olarak belirlenmesine izin verir. Bu yaklaşım için birincil senaryo, Blazor kimlik doğrulaması için bir dış uç noktaya istek yapan webassembly uygulamalarında bulunur.

Kimlik doğrulama devam ederken, `AuthorizeView` Varsayılan olarak içerik görüntülemez. Kimlik doğrulaması sırasında içeriği göstermek için `<Authorizing>` öğesini kullanın:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Bu yaklaşım normalde sunucu uygulamaları için geçerli değildir Blazor . BlazorSunucu uygulamaları, durum belirlenir oluşturmaz kimlik doğrulama durumunu bilir. `Authorizing`içerik bir Blazor sunucu uygulamasının `AuthorizeView` bileşeninde bulunabilir, ancak içerik hiçbir şekilde gösterilmez.

## <a name="authorize-attribute"></a>[Yetkilendir] özniteliği

`[Authorize]`Özniteliği, Razor bileşenlerinde kullanılabilir:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Yalnızca `[Authorize]` `@page` yönlendirici üzerinden ulaşılan bileşenlerde kullanın Blazor . Yetkilendirme yalnızca, bir sayfada işlenen alt bileşenler için *değil* , yönlendirmenin bir yönü olarak gerçekleştirilir. Bir sayfa içindeki belirli parçaların görüntülenmesini yetkilendirmek için, `AuthorizeView` bunun yerine kullanın.

`[Authorize]`Özniteliği rol tabanlı veya ilke tabanlı yetkilendirmeyi de destekler. Rol tabanlı yetkilendirme için `Roles` parametresini kullanın:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

İlke tabanlı yetkilendirme için `Policy` parametresini kullanın:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

`Roles`Ya da `Policy` belirtilmemişse `[Authorize]` varsayılan ilkeyi kullanır, varsayılan olarak şu şekilde değerlendirilir:

* Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.
* Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Yönlendirici bileşeniyle yetkisiz içeriği özelleştirme

Bileşen `Router` ile birlikte bileşeni, uygulamanın şu `AuthorizeRouteView` durumlarda özel içerik belirtmesini sağlar:

* İçerik bulunamadı.
* Kullanıcı, `[Authorize]` bileşene uygulanan bir koşulla başarısız olur. Özniteliği, `[Authorize]` [ `[Authorize]` öznitelik](#authorize-attribute) bölümünde ele alınmıştır.
* Zaman uyumsuz kimlik doğrulama devam ediyor.

Varsayılan Blazor sunucu projesi şablonunda, `App` bileşen (*app. Razor*) özel içerik ayarlamayı gösterir:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

`<NotFound>`, `<NotAuthorized>` Ve etiketlerinin içeriği, `<Authorizing>` diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.

`<NotAuthorized>`Öğesi belirtilmemişse, `AuthorizeRouteView` aşağıdaki geri dönüş iletisini kullanır:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Kimlik doğrulama durumu değişiklikleri hakkında bildirim

Uygulama, temeldeki kimlik doğrulama durumu verilerinin değiştiğini belirlerse (örneğin, Kullanıcı oturumu kapattığından veya başka bir kullanıcı rollerini değiştirdiği için), [özel bir AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) isteğe bağlı olarak `NotifyAuthenticationStateChanged` `AuthenticationStateProvider` temel sınıfta yöntemi çağırabilir. Bu, yeni verileri kullanarak yeniden kimlik doğrulama durumu verilerini (örneğin,) tüketicilere bildirir `AuthorizeView` .

## <a name="procedural-logic"></a>Yordamsal mantık

Uygulama, yordamsal mantığın bir parçası olarak yetkilendirme kurallarını denetmek için gerekliyse, `Task<AuthenticationState>` Kullanıcı tarafından elde etmek için türünde basamaklı bir parametre kullanın <xref:System.Security.Claims.ClaimsPrincipal> . `Task<AuthenticationState>``IAuthorizationService`, ilkeleri değerlendirmek için gibi diğer hizmetlerle birleştirilebilir.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> BlazorWebassembly uygulama bileşeninde, `Microsoft.AspNetCore.Authorization` ve `Microsoft.AspNetCore.Components.Authorization` ad alanlarını ekleyin:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Bu ad alanları, uygulamanın *_Imports. Razor* dosyasına eklenerek Global olarak sağlanıyor.

## <a name="authorization-in-blazor-webassembly-apps"></a>BlazorWebassembly uygulamalarında yetkilendirme

BlazorWebassembly uygulamalarında, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için yetkilendirme denetimleri atlanabilir. Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.

**İstemci tarafı uygulamanız tarafından erişilen tüm API uç noktalarında sunucuda her zaman yetkilendirme denetimleri gerçekleştirin.**

Daha fazla bilgi için, altındaki makalelere bakın <xref:security/blazor/webassembly/index> .

## <a name="troubleshoot-errors"></a>Sorun giderme hataları

Yaygın hatalar:

* **Yetkilendirme, görev authenticationstate> türünde bir geçişli parametre gerektirir \< . Bunu sağlamak için basamaklı Dingauthenticationstate kullanmayı göz önünde bulundurun.**

* **`null`için değer alındı`authenticationStateTask`**

Projenin Blazor kimlik doğrulaması etkin bir sunucu şablonu kullanılarak oluşturulmamış olması olasıdır. `<CascadingAuthenticationState>`Kullanıcı arabirimi ağacının bir bölümünü (örneğin, `App` bileşeni (*app. Razor*) aşağıdaki şekilde sarmalayın:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

, `CascadingAuthenticationState` `Task<AuthenticationState>` Temel alınan dı hizmetinden aldığı geçişli parametreyi sağlar `AuthenticationStateProvider` .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Başar Blazor : kimlik doğrulama](https://github.com/AdrienTorris/awesome-blazor#authentication) topluluğu örnek bağlantıları
