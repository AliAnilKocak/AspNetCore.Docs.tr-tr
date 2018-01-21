---
title: "Geçirme Auth ve ASP.NET Core 2.0 kimliğine"
author: scottaddie
description: "Bu makalede, ASP.NET Core 2.0 geçirme ASP.NET Core 1.x kimlik doğrulama ve kimlik için en yaygın adımlara özetlenmektedir."
ms.author: scaddie
manager: wpickett
ms.date: 10/26/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 72ad31438a344fb5fa2b357c709b923b8077e742
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/19/2018
---
# <a name="migrating-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="7507c-103">Geçirme kimlik doğrulaması ve ASP.NET Core 2.0 için kimliği</span><span class="sxs-lookup"><span data-stu-id="7507c-103">Migrating Authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="7507c-104">Tarafından [Scott Addie](https://github.com/scottaddie) ve [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="7507c-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="7507c-105">ASP.NET Core 2.0 kimlik doğrulaması için yeni bir modeli vardır ve [kimlik](xref:security/authentication/identity) hangi basitleştirir yapılandırma Hizmetleri kullanarak.</span><span class="sxs-lookup"><span data-stu-id="7507c-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="7507c-106">Aşağıda özetlendiği gibi yeni model kullanılacak kimlik doğrulama veya kimliği kullanan ASP.NET Core 1.x uygulamaları güncelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7507c-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="7507c-107">Kimlik doğrulaması ara yazılımı ve Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="7507c-107">Authentication Middleware and Services</span></span>
<span data-ttu-id="7507c-108">1.x projelerinde kimlik doğrulaması ara yazılımı üzerinden yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="7507c-108">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="7507c-109">Desteklemek istediğiniz her kimlik doğrulama şeması için bir ara yazılım yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="7507c-109">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="7507c-110">Facebook kimlik doğrulaması aşağıdaki 1.x örnek kimliğinde yapılandırır *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-110">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="7507c-111">2.0 projelerinde Hizmetleri aracılığıyla kimlik doğrulaması yapılandırılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7507c-111">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="7507c-112">Her kimlik doğrulama şeması kaydedilir `ConfigureServices` yöntemi *haline*.</span><span class="sxs-lookup"><span data-stu-id="7507c-112">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="7507c-113">`UseIdentity` Yöntemi ile değiştirilir `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="7507c-113">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="7507c-114">Facebook kimlik doğrulaması aşağıdaki 2.0 örnek kimliğinde yapılandırır *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-114">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="7507c-115">`UseAuthentication` Yöntemi, otomatik kimlik doğrulama ve Uzaktan kimlik doğrulama isteklerini işleme için sorumlu olan tek bir kimlik doğrulaması ara yazılım bileşeni ekler.</span><span class="sxs-lookup"><span data-stu-id="7507c-115">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="7507c-116">Tek tek ara yazılım bileşenlerinin tümünü tek, ortak ara yazılım bileşeni ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="7507c-116">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="7507c-117">Her ana kimlik doğrulaması düzeni için 2.0 geçiş yönergeleri aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7507c-117">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="7507c-118">Tanımlama bilgisi tabanlı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7507c-118">Cookie-based Authentication</span></span>
<span data-ttu-id="7507c-119">Aşağıdaki iki seçenekten birini belirleyin ve gerekli değişiklikleri yapın *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-119">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="7507c-120">Kimliğine sahip tanımlama bilgileri kullanma</span><span class="sxs-lookup"><span data-stu-id="7507c-120">Use cookies with Identity</span></span>
    - <span data-ttu-id="7507c-121">Değiştir `UseIdentity` ile `UseAuthentication` içinde `Configure` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-121">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="7507c-122">Çağırma `AddIdentity` yönteminde `ConfigureServices` tanımlama bilgisi kimlik doğrulama hizmetleri ekleme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="7507c-122">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="7507c-123">İsteğe bağlı olarak, çağırma `ConfigureApplicationCookie` veya `ConfigureExternalCookie` yönteminde `ConfigureServices` kimlik tanımlama bilgisi ayarları ince ayar yapma yöntemi.</span><span class="sxs-lookup"><span data-stu-id="7507c-123">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();
    
        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="7507c-124">Tanımlama bilgileri kimlik olmadan kullanma</span><span class="sxs-lookup"><span data-stu-id="7507c-124">Use cookies without Identity</span></span>
    - <span data-ttu-id="7507c-125">Değiştir `UseCookieAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-125">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
  
        ```csharp
        app.UseAuthentication();
        ```
 
    - <span data-ttu-id="7507c-126">Çağırma `AddAuthentication` ve `AddCookie` yöntemleri `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-126">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="7507c-127">JWT taşıyıcı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7507c-127">JWT Bearer Authentication</span></span>
<span data-ttu-id="7507c-128">Aşağıdaki değişiklikleri yapın *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-128">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="7507c-129">Değiştir `UseJwtBearerAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-129">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="7507c-130">Çağırma `AddJwtBearer` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-130">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options => 
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="7507c-131">Varsayılan düzenini geçirerek ayarlamanız gerekir böylece bu kod parçacığını kimliği kullanmayan `JwtBearerDefaults.AuthenticationScheme` için `AddAuthentication` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="7507c-131">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="7507c-132">Openıd Connect (OIDC) kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7507c-132">OpenID Connect (OIDC) Authentication</span></span>
<span data-ttu-id="7507c-133">Aşağıdaki değişiklikleri yapın *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-133">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="7507c-134">Değiştir `UseOpenIdConnectAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-134">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="7507c-135">Çağırma `AddOpenIdConnect` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-135">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

### <a name="facebook-authentication"></a><span data-ttu-id="7507c-136">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7507c-136">Facebook Authentication</span></span>
<span data-ttu-id="7507c-137">Aşağıdaki değişiklikleri yapın *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-137">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="7507c-138">Değiştir `UseFacebookAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-138">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="7507c-139">Çağırma `AddFacebook` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-139">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>
    
    ```csharp
    services.AddAuthentication()
            .AddFacebook(options => 
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="7507c-140">Google kimlik doğrulama</span><span class="sxs-lookup"><span data-stu-id="7507c-140">Google Authentication</span></span>
<span data-ttu-id="7507c-141">Aşağıdaki değişiklikleri yapın *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-141">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="7507c-142">Değiştir `UseGoogleAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-142">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="7507c-143">Çağırma `AddGoogle` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-143">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options => 
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });    
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="7507c-144">Microsoft hesabı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7507c-144">Microsoft Account Authentication</span></span>
<span data-ttu-id="7507c-145">Aşağıdaki değişiklikleri yapın *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-145">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="7507c-146">Değiştir `UseMicrosoftAccountAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-146">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="7507c-147">Çağırma `AddMicrosoftAccount` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-147">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options => 
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ``` 

### <a name="twitter-authentication"></a><span data-ttu-id="7507c-148">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7507c-148">Twitter Authentication</span></span>
<span data-ttu-id="7507c-149">Aşağıdaki değişiklikleri yapın *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-149">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="7507c-150">Değiştir `UseTwitterAuthentication` yöntem çağrısı `Configure` yöntemiyle `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-150">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>
 
    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="7507c-151">Çağırma `AddTwitter` yönteminde `ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-151">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options => 
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="7507c-152">Varsayılan kimlik doğrulama şemasını ayarlama</span><span class="sxs-lookup"><span data-stu-id="7507c-152">Setting Default Authentication Schemes</span></span>
<span data-ttu-id="7507c-153">1.x içinde `AutomaticAuthenticate` ve `AutomaticChallenge` özelliklerini [AuthenticationOptions](https://docs.microsoft.com/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) temel sınıfı hedeflenen bir tek bir kimlik doğrulaması düzeni için ayarlanacak.</span><span class="sxs-lookup"><span data-stu-id="7507c-153">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](https://docs.microsoft.com/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="7507c-154">Bu zorlamak için iyi hiçbir yolu yoktu.</span><span class="sxs-lookup"><span data-stu-id="7507c-154">There was no good way to enforce this.</span></span>

<span data-ttu-id="7507c-155">2. 0 ', bu iki özellik tek tek özellikleri olarak kaldırılan `AuthenticationOptions` örneği.</span><span class="sxs-lookup"><span data-stu-id="7507c-155">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="7507c-156">İçinde yapılandırılabilir `AddAuthentication` yöntem çağrısı içinde `ConfigureServices` yöntemi *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-156">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="7507c-157">Yukarıdaki kod parçacığında, varsayılan düzenini ayarlamak `CookieAuthenticationDefaults.AuthenticationScheme` ("tanımlama bilgileri").</span><span class="sxs-lookup"><span data-stu-id="7507c-157">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="7507c-158">Alternatif olarak, aşırı yüklenmiş bir sürümünü kullanmanız `AddAuthentication` birden çok özelliği ayarlamak için yöntem.</span><span class="sxs-lookup"><span data-stu-id="7507c-158">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="7507c-159">Aşağıdaki aşırı yüklenmiş yöntemin örnekte varsayılan düzenini ayarlamak `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="7507c-159">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="7507c-160">Kimlik doğrulama şeması alternatif olarak, tek tek içinde belirtilen `[Authorize]` öznitelikleri veya yetkilendirme ilkeleri.</span><span class="sxs-lookup"><span data-stu-id="7507c-160">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options => 
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="7507c-161">Aşağıdaki koşullardan biri doğruysa varsayılan düzeni 2.0 tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="7507c-161">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="7507c-162">Otomatik olarak oturum açmanız kullanıcının istediğiniz</span><span class="sxs-lookup"><span data-stu-id="7507c-162">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="7507c-163">Kullandığınız `[Authorize]` düzenleri belirtmeden özniteliği veya Yetkilendirme İlkeleri</span><span class="sxs-lookup"><span data-stu-id="7507c-163">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="7507c-164">Bu kural için bir istisna `AddIdentity` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="7507c-164">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="7507c-165">Bu yöntem ve varsayılan kimlik doğrulaması ve uygulama tanımlama bilgisine düzenleri sınama kümeleri için tanımlama bilgileri ekler `IdentityConstants.ApplicationScheme`.</span><span class="sxs-lookup"><span data-stu-id="7507c-165">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="7507c-166">Ayrıca, oturum açma varsayılan düzeni için dış tanımlama bilgisi ayarlar `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="7507c-166">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="7507c-167">HttpContext kimlik doğrulama uzantıları kullanma</span><span class="sxs-lookup"><span data-stu-id="7507c-167">Use HttpContext Authentication Extensions</span></span>
<span data-ttu-id="7507c-168">`IAuthenticationManager` Arabirimi 1.x kimlik doğrulaması sistemine ana giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="7507c-168">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="7507c-169">Yeni bir kümesi ile değiştirilen `HttpContext` uzantı yöntemleri `Microsoft.AspNetCore.Authentication` ad alanı.</span><span class="sxs-lookup"><span data-stu-id="7507c-169">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="7507c-170">Örneğin, başvuru 1.x projeleri bir `Authentication` özelliği:</span><span class="sxs-lookup"><span data-stu-id="7507c-170">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="7507c-171">2.0 projelerinde alma `Microsoft.AspNetCore.Authentication` ad alanı ve silme `Authentication` özelliği başvuruları:</span><span class="sxs-lookup"><span data-stu-id="7507c-171">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="7507c-172">Windows kimlik doğrulaması (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="7507c-172">Windows Authentication (HTTP.sys / IISIntegration)</span></span>
<span data-ttu-id="7507c-173">Windows kimlik doğrulaması iki çeşidi vardır:</span><span class="sxs-lookup"><span data-stu-id="7507c-173">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="7507c-174">Ana bilgisayar kimliği doğrulanmış kullanıcılar yalnızca izin verir</span><span class="sxs-lookup"><span data-stu-id="7507c-174">The host only allows authenticated users</span></span>
2. <span data-ttu-id="7507c-175">Ana bilgisayar hem anonim verir ve kullanıcıların kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="7507c-175">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="7507c-176">Yukarıda açıklanan Birincisi 2.0 değişikliklerden etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="7507c-176">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="7507c-177">Yukarıda açıklanan ikinci değişim 2.0 değişikliklerden etkilenir.</span><span class="sxs-lookup"><span data-stu-id="7507c-177">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="7507c-178">Örnek olarak, anonim kullanıcılar uygulamanıza IIS sağlayarak veya [HTTP.sys](xref:fundamentals/servers/weblistener) katman denetleyici düzeyinde ancak yetki vererek kullanıcılar.</span><span class="sxs-lookup"><span data-stu-id="7507c-178">As an example, you may be allowing anonymous users into your application at the IIS or [HTTP.sys](xref:fundamentals/servers/weblistener) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="7507c-179">Bu senaryoda, varsayılan düzenini ayarlamak `IISDefaults.AuthenticationScheme` içinde `ConfigureServices` yöntemi *haline*:</span><span class="sxs-lookup"><span data-stu-id="7507c-179">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="7507c-180">Varsayılan düzen ayarlanamadı uygun şekilde çalışmasını sınama için authorize isteğinin engeller.</span><span class="sxs-lookup"><span data-stu-id="7507c-180">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="7507c-181">IdentityCookieOptions Instances</span><span class="sxs-lookup"><span data-stu-id="7507c-181">IdentityCookieOptions Instances</span></span>
<span data-ttu-id="7507c-182">2.0 değişiklikleri yan etkisi seçenekleri tanımlama bilgisi seçenekleri örnek yerine adlandırılmış kullanmanın anahtarıdır.</span><span class="sxs-lookup"><span data-stu-id="7507c-182">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="7507c-183">Kimlik tanımlama bilgisi düzeni adları özelleştirme yeteneği kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="7507c-183">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="7507c-184">Örneğin, 1.x projelerin [Oluşturucu ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) geçirmek için bir `IdentityCookieOptions` parametresine *AccountController.cs*.</span><span class="sxs-lookup"><span data-stu-id="7507c-184">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="7507c-185">Dış tanımlama bilgisi kimlik doğrulama şeması sağlanan örneğinden erişilir:</span><span class="sxs-lookup"><span data-stu-id="7507c-185">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="7507c-186">Daha önce bahsedilen Oluşturucu ekleme 2.0 projelerinde gereksiz olur ve `_externalCookieScheme` alan silinebilir:</span><span class="sxs-lookup"><span data-stu-id="7507c-186">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="7507c-187">`IdentityConstants.ExternalScheme` Sabiti doğrudan kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7507c-187">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="7507c-188">IdentityUser POCO Gezinti özellikleri ekleyin</span><span class="sxs-lookup"><span data-stu-id="7507c-188">Add IdentityUser POCO Navigation Properties</span></span>
<span data-ttu-id="7507c-189">Entity Framework (EF) çekirdek Gezinti özellikleri taban `IdentityUser` POCO (düz eski CLR nesnesi) kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="7507c-189">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="7507c-190">El ile 1.x projenizi bu özellikleri kullandıysanız, bunları 2.0 projeye ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7507c-190">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="7507c-191">Yinelenen yabancı anahtarlar EF çekirdek geçişler çalıştırırken önlemek için aşağıdakileri ekleyin, `IdentityDbContext` sınıfı `OnModelCreating` yöntemi (sonra `base.OnModelCreating();` çağrısı):</span><span class="sxs-lookup"><span data-stu-id="7507c-191">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Identity table names and more.
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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="7507c-192">Replace GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="7507c-192">Replace GetExternalAuthenticationSchemes</span></span>
<span data-ttu-id="7507c-193">Zaman uyumlu yöntemi `GetExternalAuthenticationSchemes` lehinde zaman uyumsuz bir sürümünü kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="7507c-193">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="7507c-194">1.x projeleri olmayan aşağıdaki kodu *ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="7507c-194">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="7507c-195">Bu yöntem görünür *Login.cshtml* çok:</span><span class="sxs-lookup"><span data-stu-id="7507c-195">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="7507c-196">2.0 projelerinde kullanma `GetExternalAuthenticationSchemesAsync` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="7507c-196">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="7507c-197">İçinde *Login.cshtml*, `AuthenticationScheme` erişilebilir özelliği `foreach` döngü değişiklikler `Name`:</span><span class="sxs-lookup"><span data-stu-id="7507c-197">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="7507c-198">ManageLoginsViewModel özellik değişikliği</span><span class="sxs-lookup"><span data-stu-id="7507c-198">ManageLoginsViewModel Property Change</span></span>
<span data-ttu-id="7507c-199">A `ManageLoginsViewModel` nesne kullanılıyor `ManageLogins` eylemi *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="7507c-199">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="7507c-200">1.x projelerinde nesne 's `OtherLogins` özelliği döndürme türü `IList<AuthenticationDescription>`.</span><span class="sxs-lookup"><span data-stu-id="7507c-200">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="7507c-201">Bu dönüş türü alma gerektirir `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="7507c-201">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="7507c-202">Dönüş türü değişikliklerini 2.0 projelerinde `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="7507c-202">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="7507c-203">Bu yeni dönüş türü değiştirme gerektirir `Microsoft.AspNetCore.Http.Authentication` ile içeri aktarma bir `Microsoft.AspNetCore.Authentication` içeri aktarın.</span><span class="sxs-lookup"><span data-stu-id="7507c-203">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[Main](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="7507c-204">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7507c-204">Additional Resources</span></span>
<span data-ttu-id="7507c-205">Ek bilgi ve tartışma için bkz: [Auth 2.0 tartışma](https://github.com/aspnet/Security/issues/1338) github'da sorun.</span><span class="sxs-lookup"><span data-stu-id="7507c-205">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
