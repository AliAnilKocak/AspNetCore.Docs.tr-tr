---
title: Kimlik doğrulaması ve kimlik için ASP.NET Core 2.0 geçirme
author: scottaddie
description: Bu makalede, ASP.NET Core 2.0 için geçirme ASP.NET Core 1.x kimlik doğrulaması ve kimlik için en yaygın adımlar özetlenmektedir.
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 086deac51af186012315d5b6a1236c92c8980037
ms.sourcegitcommit: 5d384db2fa9373a93b5d15e985fb34430e49ad7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66039237"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="bbbb3-103">Kimlik doğrulaması ve kimlik için ASP.NET Core 2.0 geçirme</span><span class="sxs-lookup"><span data-stu-id="bbbb3-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="bbbb3-104">Tarafından [Scott Addie](https://github.com/scottaddie) ve [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="bbbb3-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="bbbb3-105">ASP.NET Core 2.0 kimlik doğrulaması için yeni bir modeli vardır ve [kimlik](xref:security/authentication/identity) sadeleştiren yapılandırma hizmetlerini kullanarak.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="bbbb3-106">Kimlik doğrulaması veya kimlik kullanan ASP.NET Core 1.x uygulamaları, aşağıda belirtildiği gibi yeni modeli kullanmak için güncelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="bbbb3-107">Kimlik doğrulaması ara yazılımı ve Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="bbbb3-107">Authentication Middleware and services</span></span>

<span data-ttu-id="bbbb3-108">1.x projelerinde, kimlik doğrulaması ara yazılımı üzerinden yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-108">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="bbbb3-109">Desteklemek istediğiniz her bir kimlik doğrulama düzeni için bir ara yazılım yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-109">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="bbbb3-110">Aşağıdaki 1.x örnek kimliği ile Facebook kimlik doğrulamasını yapılandırır *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-110">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="bbbb3-111">2.0 projelerinde, kimlik doğrulama hizmetleri aracılığıyla yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-111">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="bbbb3-112">Her kimlik doğrulama düzeni kaydedilmiştir `ConfigureServices` yöntemi *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-112">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="bbbb3-113">`UseIdentity` Yöntemi ile değiştirilir `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-113">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="bbbb3-114">Aşağıdaki 2.0 örnek kimliği ile Facebook kimlik doğrulamasını yapılandırır *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-114">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="bbbb3-115">`UseAuthentication` Yöntemi otomatik kimlik doğrulama ve Uzaktan kimlik doğrulama isteklerinin işlenmesi için sorumlu tek bir kimlik doğrulaması ara yazılım bileşeni ekler.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-115">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="bbbb3-116">Tek bir ara yazılım bileşenlerinin tümünü tek, ortak bir ara yazılım bileşeni ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-116">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="bbbb3-117">Her temel kimlik doğrulaması düzeni için 2.0 geçiş yönergeleri aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-117">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="bbbb3-118">Tanımlama bilgisi tabanlı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="bbbb3-118">Cookie-based authentication</span></span>

<span data-ttu-id="bbbb3-119">Aşağıdaki iki seçenekten birini seçin ve gerekli değişiklikleri yapın *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-119">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="bbbb3-120">Tanımlama bilgileri kimlik ile kullanma</span><span class="sxs-lookup"><span data-stu-id="bbbb3-120">Use cookies with Identity</span></span>
    - <span data-ttu-id="bbbb3-121">Değiştirin `UseIdentity` ile `UseAuthentication` içinde `Configure` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-121">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="bbbb3-122">Çağırma `AddIdentity` yönteminde `ConfigureServices` tanımlama bilgisi kimlik doğrulaması hizmetlerini eklemek için yöntemi.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-122">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="bbbb3-123">İsteğe bağlı olarak, çağırma `ConfigureApplicationCookie` veya `ConfigureExternalCookie` yönteminde `ConfigureServices` kimlik tanımlama bilgisi ayarları ince ayar için yöntemi.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-123">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="bbbb3-124">Kimlik olmadan tanımlama bilgileri kullanma</span><span class="sxs-lookup"><span data-stu-id="bbbb3-124">Use cookies without Identity</span></span>
    - <span data-ttu-id="bbbb3-125">Değiştirin `UseCookieAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-125">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="bbbb3-126">Çağırma `AddAuthentication` ve `AddCookie` yöntemleri `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-126">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="bbbb3-127">JWT taşıyıcı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="bbbb3-127">JWT Bearer Authentication</span></span>

<span data-ttu-id="bbbb3-128">Aşağıdaki değişiklikleri yapın *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-128">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="bbbb3-129">Değiştirin `UseJwtBearerAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-129">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="bbbb3-130">Çağırma `AddJwtBearer` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-130">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="bbbb3-131">Varsayılan düzenini geçirerek ayarlanmalıdır. Bu nedenle bu kod parçacığı kimliğini kullanmaz `JwtBearerDefaults.AuthenticationScheme` için `AddAuthentication` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-131">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="bbbb3-132">Openıd Connect (OIDC) kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="bbbb3-132">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="bbbb3-133">Aşağıdaki değişiklikleri yapın *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-133">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="bbbb3-134">Değiştirin `UseOpenIdConnectAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-134">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="bbbb3-135">Çağırma `AddOpenIdConnect` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-135">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="bbbb3-136">Değiştirin `PostLogoutRedirectUri` özelliğinde `OpenIdConnectOptions` eylemiyle `SignedOutRedirectUri`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-136">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="bbbb3-137">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="bbbb3-137">Facebook authentication</span></span>

<span data-ttu-id="bbbb3-138">Aşağıdaki değişiklikleri yapın *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-138">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="bbbb3-139">Değiştirin `UseFacebookAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-139">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="bbbb3-140">Çağırma `AddFacebook` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-140">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="bbbb3-141">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="bbbb3-141">Google authentication</span></span>

<span data-ttu-id="bbbb3-142">Aşağıdaki değişiklikleri yapın *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-142">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="bbbb3-143">Değiştirin `UseGoogleAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-143">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="bbbb3-144">Çağırma `AddGoogle` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-144">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="bbbb3-145">Microsoft Account kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="bbbb3-145">Microsoft Account authentication</span></span>

<span data-ttu-id="bbbb3-146">Aşağıdaki değişiklikleri yapın *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-146">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="bbbb3-147">Değiştirin `UseMicrosoftAccountAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-147">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="bbbb3-148">Çağırma `AddMicrosoftAccount` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-148">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="bbbb3-149">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="bbbb3-149">Twitter authentication</span></span>

<span data-ttu-id="bbbb3-150">Aşağıdaki değişiklikleri yapın *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-150">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="bbbb3-151">Değiştirin `UseTwitterAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-151">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="bbbb3-152">Çağırma `AddTwitter` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-152">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="bbbb3-153">Varsayılan kimlik doğrulama düzenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="bbbb3-153">Setting default authentication schemes</span></span>

<span data-ttu-id="bbbb3-154">1.x içinde `AutomaticAuthenticate` ve `AutomaticChallenge` özelliklerini [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) temel sınıfı yönelik tek bir kimlik doğrulama şemasını temel ayarlanacak.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-154">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="bbbb3-155">Bunu zorunlu kılmak için iyi bir yolu yoktu.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-155">There was no good way to enforce this.</span></span>

<span data-ttu-id="bbbb3-156">2. 0 ', bu iki özellik özellikleri ayrı ayrı olarak kaldırılan `AuthenticationOptions` örneği.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-156">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="bbbb3-157">İçinde yapılandırılabilir `AddAuthentication` yöntem çağrısı içinde `ConfigureServices` yöntemi *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-157">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="bbbb3-158">Yukarıdaki kod parçacığında, varsayılan düzenini ayarlamak `CookieAuthenticationDefaults.AuthenticationScheme` ("tanımlama bilgileri").</span><span class="sxs-lookup"><span data-stu-id="bbbb3-158">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="bbbb3-159">Alternatif olarak, aşırı yüklenmiş bir sürümünü kullanmak `AddAuthentication` birden fazla özelliği ayarlamak için yöntemi.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-159">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="bbbb3-160">Aşırı yüklenmiş yöntem aşağıdaki örnekte varsayılan düzenini ayarlamak `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-160">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="bbbb3-161">Kimlik doğrulama düzeni bunun yerine bireysel içinde belirtilen `[Authorize]` öznitelikleri veya yetkilendirme ilkeleri.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-161">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="bbbb3-162">Aşağıdaki koşullardan biri doğru ise, 2. 0'varsayılan düzenini tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-162">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="bbbb3-163">Kullanıcı otomatik olarak oturum açmanız istiyor</span><span class="sxs-lookup"><span data-stu-id="bbbb3-163">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="bbbb3-164">Kullandığınız `[Authorize]` düzenleri belirtmeden özniteliği veya Yetkilendirme İlkeleri</span><span class="sxs-lookup"><span data-stu-id="bbbb3-164">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="bbbb3-165">Bu kuralın istisnası `AddIdentity` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-165">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="bbbb3-166">Bu yöntem, siz ve varsayılan kimlik doğrulaması ve uygulama tanımlama bilgisinin düzenleri meydan kümeleri için tanımlama bilgileri ekler. `IdentityConstants.ApplicationScheme`.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-166">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="bbbb3-167">Ayrıca, varsayılan oturum açma düzeni için dış tanımlama bilgisi ayarlar `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-167">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="bbbb3-168">HttpContext kimlik doğrulama uzantıları kullanma</span><span class="sxs-lookup"><span data-stu-id="bbbb3-168">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="bbbb3-169">`IAuthenticationManager` Arabirimi 1.x kimlik doğrulama sisteminde ana giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-169">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="bbbb3-170">Yeni bir dizi ile değiştirilmiştir `HttpContext` alanında uzantı yöntemlerini `Microsoft.AspNetCore.Authentication` ad alanı.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-170">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="bbbb3-171">Örneğin, başvuru 1.x projeleri bir `Authentication` özelliği:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-171">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="bbbb3-172">2.0 projelerinde, içeri aktarma `Microsoft.AspNetCore.Authentication` ad alanını ve silme `Authentication` özelliği başvuruları:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-172">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="bbbb3-173">Windows kimlik doğrulaması (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="bbbb3-173">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="bbbb3-174">Windows kimlik doğrulamasının iki çeşidi vardır:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-174">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="bbbb3-175">Konak, yalnızca kimliği doğrulanmış kullanıcılara izin verir</span><span class="sxs-lookup"><span data-stu-id="bbbb3-175">The host only allows authenticated users</span></span>
2. <span data-ttu-id="bbbb3-176">Ana bilgisayar hem anonim verir ve kimliği doğrulanmış kullanıcılar</span><span class="sxs-lookup"><span data-stu-id="bbbb3-176">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="bbbb3-177">Yukarıda açıklanan Birincisi 2.0 değişikliklerden etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-177">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="bbbb3-178">Yukarıda açıklanan İkincisiyse 2.0 değişikliklerden etkilenir.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-178">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="bbbb3-179">Örneğin, anonim kullanıcılar uygulamanıza IIS sağlayan veya [HTTP.sys](xref:fundamentals/servers/httpsys) katman denetleyici düzeyinde ancak yetki verme kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-179">As an example, you may be allowing anonymous users into your app at the IIS or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="bbbb3-180">Bu senaryoda, varsayılan düzenini ayarlayın `IISDefaults.AuthenticationScheme` içinde `Startup.ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-180">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="bbbb3-181">Varsayılan düzen ayarlanamadı uygun şekilde çalışmasını eşleşip eşleşmediğini authorize isteğinin engeller.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-181">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="bbbb3-182">IdentityCookieOptions örnekleri</span><span class="sxs-lookup"><span data-stu-id="bbbb3-182">IdentityCookieOptions instances</span></span>

<span data-ttu-id="bbbb3-183">Bir yan etkisi 2.0 değişiklikleri seçeneklerini tanımlama bilgisi seçenekleri örnek yerine adlandırılmış kullanarak anahtardır.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-183">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="bbbb3-184">Kimlik tanımlama bilgisi düzeni adları özelleştirme yeteneği kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-184">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="bbbb3-185">Örneğin, 1.x projelerin [Oluşturucu ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) geçirilecek bir `IdentityCookieOptions` parametrede *AccountController.cs*.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-185">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="bbbb3-186">Dış tanımlama bilgisi kimlik doğrulaması düzeni sağlanan örneğinden erişilebilir:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-186">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="bbbb3-187">Yukarıda sözü edilen Oluşturucu ekleme 2.0 projelerinde, gereksiz olur ve `_externalCookieScheme` alan silinebilir:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-187">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="bbbb3-188">`IdentityConstants.ExternalScheme` Sabiti doğrudan kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-188">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="bbbb3-189">POCO IdentityUser Gezinti özellikleri ekleyin</span><span class="sxs-lookup"><span data-stu-id="bbbb3-189">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="bbbb3-190">Entity Framework (EF) çekirdek gezinme özelliklerini temel `IdentityUser` POCO (düz eski CLR nesnesi) kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-190">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="bbbb3-191">El ile 1.x projenizi bu özellikleri kullandıysanız, bunları 2.0 projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-191">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="bbbb3-192">EF Core geçişleri çalıştırırken yinelenen yabancı anahtarlar önlemek için aşağıdaki ekleyin, `IdentityDbContext` sınıfının `OnModelCreating` yöntemi (sonra `base.OnModelCreating();` çağrısı):</span><span class="sxs-lookup"><span data-stu-id="bbbb3-192">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="bbbb3-193">GetExternalAuthenticationSchemes değiştirin</span><span class="sxs-lookup"><span data-stu-id="bbbb3-193">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="bbbb3-194">Zaman uyumlu yöntem `GetExternalAuthenticationSchemes` yerine zaman uyumsuz bir sürümü kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-194">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="bbbb3-195">1.x projelerini olmayan aşağıdaki kodu *ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-195">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="bbbb3-196">Bu yöntem görünür *Login.cshtml* çok:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-196">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="bbbb3-197">2.0 projelerinde kullanma `GetExternalAuthenticationSchemesAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-197">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="bbbb3-198">İçinde *Login.cshtml*, `AuthenticationScheme` erişilebilir özellik `foreach` döngü değişiklikleri `Name`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-198">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="bbbb3-199">ManageLoginsViewModel özellik değişikliği</span><span class="sxs-lookup"><span data-stu-id="bbbb3-199">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="bbbb3-200">A `ManageLoginsViewModel` nesnesi kullanılır `ManageLogins` eylemi *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-200">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="bbbb3-201">1.x projelerinde, nesne 's `OtherLogins` özelliği döndürme türü `IList<AuthenticationDescription>`.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-201">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="bbbb3-202">Bu dönüş türü alma gerektirir `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="bbbb3-202">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="bbbb3-203">Dönüş türü değişikliklerini 2.0 projelerinde `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-203">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="bbbb3-204">Bu yeni dönüş türünü değiştirme gerektirir `Microsoft.AspNetCore.Http.Authentication` ile içeri aktarma bir `Microsoft.AspNetCore.Authentication` içeri aktarın.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-204">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="bbbb3-205">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bbbb3-205">Additional resources</span></span>

<span data-ttu-id="bbbb3-206">Ek ayrıntılar ve tartışma için bkz: [Auth 2.0 için tartışma](https://github.com/aspnet/Security/issues/1338) github'da sorun.</span><span class="sxs-lookup"><span data-stu-id="bbbb3-206">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
