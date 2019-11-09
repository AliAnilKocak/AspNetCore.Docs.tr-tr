---
title: ASP.NET Core tek sayfalı uygulamalar için kimlik doğrulamaya giriş
author: javiercn
description: ASP.NET Core uygulamasının içinde barındırılan tek sayfalı bir uygulamayla kimlik kullanın.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
uid: security/authentication/identity/spa
ms.openlocfilehash: f58d92634ce1ef6110533d56c40b7520dda90514
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73897046"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="f85d3-103">Maça kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="f85d3-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="f85d3-104">ASP.NET Core 3,0 veya üzeri, API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="f85d3-105">Kimlik doğrulama ve depolama için ASP.NET Core kimliği, açık KIMLIK Connect uygulama için [IdentityServer](https://identityserver.io/) ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="f85d3-106">Web uygulamasındaki kimlik doğrulama parametresine benzer bir kimlik doğrulama parametresi ( **Model-View-Controller)** (MVC **) ve** **Web uygulaması** (Razor Pages) Proje şablonları.</span><span class="sxs-lookup"><span data-stu-id="f85d3-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="f85d3-107">İzin verilen parametre değerleri **none** ve **bireysel**.</span><span class="sxs-lookup"><span data-stu-id="f85d3-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="f85d3-108">**Tepki. js ve Redux** proje şablonu şu anda kimlik doğrulama parametresini desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="f85d3-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="f85d3-109">API yetkilendirme desteğiyle uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="f85d3-109">Create an app with API authorization support</span></span>

<span data-ttu-id="f85d3-110">Kullanıcı kimlik doğrulaması ve yetkilendirme, hem angular ile hem de maça 'Ları ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="f85d3-111">Bir komut kabuğu açın ve şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f85d3-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="f85d3-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="f85d3-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="f85d3-113">**Tepki**verme:</span><span class="sxs-lookup"><span data-stu-id="f85d3-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="f85d3-114">Yukarıdaki komut, SPA 'yı içeren *clientapp* dizinine sahip bir ASP.NET Core uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f85d3-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="f85d3-115">Uygulamanın ASP.NET Core bileşenlerinin genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="f85d3-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="f85d3-116">Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır:</span><span class="sxs-lookup"><span data-stu-id="f85d3-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="f85d3-117">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="f85d3-117">Startup class</span></span>

<span data-ttu-id="f85d3-118">`Startup` sınıfı aşağıdaki eklemelere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="f85d3-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="f85d3-119">`Startup.ConfigureServices` yönteminin içinde:</span><span class="sxs-lookup"><span data-stu-id="f85d3-119">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="f85d3-120">Varsayılan Kullanıcı arabirimine sahip kimlik:</span><span class="sxs-lookup"><span data-stu-id="f85d3-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="f85d3-121">IdentityServer 'ın en üstünde yer alan bazı varsayılan ASP.NET Core kurallarını yükleyen ek bir `AddApiAuthorization` Yardımcısı yöntemi olan IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="f85d3-121">IdentityServer with an additional `AddApiAuthorization` helper method that setups some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="f85d3-122">Kimliği, IdentityServer tarafından üretilen JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir `AddIdentityServerJwt` Yardımcısı yöntemiyle kimlik doğrulaması:</span><span class="sxs-lookup"><span data-stu-id="f85d3-122">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="f85d3-123">`Startup.Configure` yönteminin içinde:</span><span class="sxs-lookup"><span data-stu-id="f85d3-123">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="f85d3-124">İstek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumlu kimlik doğrulama ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="f85d3-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="f85d3-125">Açık KIMLIK bağlantı noktalarını kullanıma sunan IdentityServer ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="f85d3-125">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="f85d3-126">Addadpiauthorization</span><span class="sxs-lookup"><span data-stu-id="f85d3-126">AddApiAuthorization</span></span>

<span data-ttu-id="f85d3-127">Bu yardımcı yöntemi, IdentityServer 'ı desteklenen yapılandırmamızı kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-127">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="f85d3-128">IdentityServer, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="f85d3-129">Aynı zamanda, en yaygın senaryolar için gereksiz karmaşıklık sunan.</span><span class="sxs-lookup"><span data-stu-id="f85d3-129">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="f85d3-130">Sonuç olarak, size iyi bir başlangıç noktası olarak kabul edilen bir dizi kural ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-130">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="f85d3-131">Kimlik doğrulama gereksinimleriniz değiştikçe, IdentityServer 'ın tam gücü, kimlik doğrulamasını gereksinimlerinize uyacak şekilde özelleştirmek için hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="f85d3-132">Addentityserverjwt</span><span class="sxs-lookup"><span data-stu-id="f85d3-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="f85d3-133">Bu yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-133">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="f85d3-134">İlke, kimlik URL 'SI alanı "/Identity" içindeki herhangi bir alt yol için tüm isteklerin kimlik işlemesini sağlamak üzere yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-134">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="f85d3-135">`JwtBearerHandler` diğer tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="f85d3-135">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="f85d3-136">Ayrıca, bu yöntem, IdentityServer 'a `<<ApplicationName>>API` varsayılan kapsamına sahip bir `<<ApplicationName>>API` API kaynağı kaydeder ve JWT taşıyıcı belirteç ara yazılımını, bu uygulama için IdentityServer tarafından verilen belirteçleri doğrulamak için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-136">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="f85d3-137">Dalgalı bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="f85d3-137">WeatherForecastController</span></span>

<span data-ttu-id="f85d3-138">*Controllers\dalgalı therforeroı Controller.cs* dosyasında, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirileceğini belirten sınıfa uygulanan `[Authorize]` özniteliğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f85d3-138">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="f85d3-139">Varsayılan yetkilendirme ilkesi, yukarıda belirtilen ilke şemasına `AddIdentityServerJwt` tarafından ayarlanan varsayılan kimlik doğrulama şemasını kullanacak şekilde yapılandırılmıştır. Bu, bu tür yardımcı yöntemi tarafından yapılandırılan `JwtBearerHandler` istekleri için varsayılan işleyicidir uygulama.</span><span class="sxs-lookup"><span data-stu-id="f85d3-139">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="f85d3-140">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="f85d3-140">ApplicationDbContext</span></span>

<span data-ttu-id="f85d3-141">*Data\applicationdbcontext.cs* dosyasında, `DbContext` (`IdentityDbContext`' den daha fazla türetilmiş bir sınıf), IdentityServer şemasını dahil etmek için `ApiAuthorizationDbContext` genişlettiği özel durum ile kimliğin aynı kullanıldığını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="f85d3-141">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="f85d3-142">Veritabanı şeması üzerinde tam denetim elde etmek için, kullanılabilir kimlik `DbContext` sınıflarından birinden devralma ve bağlamı `OnModelCreating` yöntemine `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` çağırarak kimlik şemasını içerecek şekilde yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="f85d3-142">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="f85d3-143">Oıdcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="f85d3-143">OidcConfigurationController</span></span>

<span data-ttu-id="f85d3-144">*Controllers\oıdcconfigurationcontroller.cs* dosyasında, istemcinin kullanması gereken OIDC parametrelerine hizmeti sağlaması için sağlanan uç noktaya dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f85d3-144">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="f85d3-145">appSettings. JSON</span><span class="sxs-lookup"><span data-stu-id="f85d3-145">appsettings.json</span></span>

<span data-ttu-id="f85d3-146">Proje kökünün *appSettings. JSON* dosyasında, yapılandırılmış istemciler listesini açıklayan yeni bir `IdentityServer` bölümü vardır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-146">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="f85d3-147">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-147">In the following example, there's a single client.</span></span> <span data-ttu-id="f85d3-148">İstemci adı, uygulama adına karşılık gelir ve bir kural tarafından OAuth `ClientId` parametresine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-148">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="f85d3-149">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-149">The profile indicates the app type being configured.</span></span> <span data-ttu-id="f85d3-150">Sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-150">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="f85d3-151">[Uygulama profilleri](#application-profiles) bölümünde açıklandığı gibi çeşitli profiller mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="f85d3-151">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="f85d3-152">appSettings. Development. JSON</span><span class="sxs-lookup"><span data-stu-id="f85d3-152">appsettings.Development.json</span></span>

<span data-ttu-id="f85d3-153">, *AppSettings 'de. Proje kökünün geliştirme. JSON* dosyası, belirteçleri imzalamak için kullanılan anahtarı açıklayan bir `IdentityServer` bölümü vardır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-153">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="f85d3-154">Üretime dağıtım yaparken, [üretime dağıtma](#deploy-to-production) bölümünde açıklandığı gibi bir anahtarın uygulamayla birlikte sağlanması ve dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-154">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="f85d3-155">Angular uygulamasının genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="f85d3-155">General description of the Angular app</span></span>

<span data-ttu-id="f85d3-156">Angular şablonundaki kimlik doğrulama ve API yetkilendirme desteği *Clientapp\src\api-Authorization* dizinindeki kendi angular modülünde yer alır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-156">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="f85d3-157">Modül aşağıdaki öğelerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="f85d3-157">The module is composed of the following elements:</span></span>

* <span data-ttu-id="f85d3-158">3 bileşen:</span><span class="sxs-lookup"><span data-stu-id="f85d3-158">3 components:</span></span>
  * <span data-ttu-id="f85d3-159">*login. Component. TS*: uygulamanın oturum açma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="f85d3-159">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="f85d3-160">*Logout. Component. TS*: uygulamanın oturum kapatma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="f85d3-160">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="f85d3-161">*login-Menu. Component. TS*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:</span><span class="sxs-lookup"><span data-stu-id="f85d3-161">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="f85d3-162">Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="f85d3-162">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="f85d3-163">Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="f85d3-163">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="f85d3-164">Rotalara eklenebilen ve yolu ziyaret etmeden önce kullanıcının kimliğinin doğrulanmasını gerektiren bir Route Guard `AuthorizeGuard`.</span><span class="sxs-lookup"><span data-stu-id="f85d3-164">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="f85d3-165">Kullanıcının kimlik doğrulaması yapıldığında API 'YI hedefleyen giden HTTP isteklerine erişim belirtecini ekleyen bir HTTP yakalayıcısı `AuthorizeInterceptor`.</span><span class="sxs-lookup"><span data-stu-id="f85d3-165">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="f85d3-166">Kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren bir hizmet `AuthorizeService`.</span><span class="sxs-lookup"><span data-stu-id="f85d3-166">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="f85d3-167">Uygulamanın kimlik doğrulama bölümleriyle ilişkili yolları tanımlayan angular modülü.</span><span class="sxs-lookup"><span data-stu-id="f85d3-167">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="f85d3-168">Oturum açma menü bileşeni, yakalayıcısı, koruyucu ve uygulamanın geri kalanından tüketim için hizmeti sunar.</span><span class="sxs-lookup"><span data-stu-id="f85d3-168">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="f85d3-169">Tepki verme uygulamasının genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="f85d3-169">General description of the React app</span></span>

<span data-ttu-id="f85d3-170">Yanıt verme şablonunda kimlik doğrulama ve API yetkilendirmesi desteği *Clientapp\src\\disk api-Authorization* dizininde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f85d3-170">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="f85d3-171">Şu öğelerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="f85d3-171">It's composed of the following elements:</span></span>

* <span data-ttu-id="f85d3-172">4 bileşen:</span><span class="sxs-lookup"><span data-stu-id="f85d3-172">4 components:</span></span>
  * <span data-ttu-id="f85d3-173">*Login. js*: uygulamanın oturum açma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="f85d3-173">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="f85d3-174">*Logout. js*: uygulamanın oturum kapatma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="f85d3-174">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="f85d3-175">*Loginmenu. js*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:</span><span class="sxs-lookup"><span data-stu-id="f85d3-175">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="f85d3-176">Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="f85d3-176">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="f85d3-177">Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="f85d3-177">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="f85d3-178">*Authorizeroute. js*: `Component` parametresinde belirtilen bileşeni işlemeden önce kullanıcının kimliğinin doğrulanmasını gerektiren bir rota bileşeni.</span><span class="sxs-lookup"><span data-stu-id="f85d3-178">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="f85d3-179">Bir `AuthorizeService` sınıfının dışa, kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkında bilgileri, tüketim için uygulamanın geri kalanına getiren bir `authService` örneği.</span><span class="sxs-lookup"><span data-stu-id="f85d3-179">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="f85d3-180">Artık çözümün ana bileşenlerini gördüğünüze göre, uygulama için ayrı senaryolara daha ayrıntılı bir şekilde göz atabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f85d3-180">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="f85d3-181">Yeni bir API 'de yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="f85d3-181">Require authorization on a new API</span></span>

<span data-ttu-id="f85d3-182">Varsayılan olarak, sistem yeni API 'Ler için kolayca yetkilendirme gerektirecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-182">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="f85d3-183">Bunu yapmak için yeni bir denetleyici oluşturun ve denetleyici sınıfına veya denetleyici içindeki herhangi bir eyleme `[Authorize]` özniteliğini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f85d3-183">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="f85d3-184">API kimlik doğrulama işleyicisini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="f85d3-184">Customize the API authentication handler</span></span>

<span data-ttu-id="f85d3-185">API 'nin JWT işleyicisinin yapılandırmasını özelleştirmek için <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> örneğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f85d3-185">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="f85d3-186">API 'nin JWT işleyicisi, `JwtBearerEvents`kullanarak kimlik doğrulama işlemi üzerinde denetimi etkinleştiren olaylar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f85d3-186">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="f85d3-187">API yetkilendirmesi için destek sağlamak üzere `AddIdentityServerJwt` kendi olay işleyicilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-187">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="f85d3-188">Bir olayın işlenmesini özelleştirmek için, var olan olay işleyicisini gereken ek mantığla sarın.</span><span class="sxs-lookup"><span data-stu-id="f85d3-188">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="f85d3-189">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f85d3-189">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="f85d3-190">Önceki kodda, `OnTokenValidated` olay işleyicisi özel bir uygulamayla değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-190">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="f85d3-191">Bu uygulama:</span><span class="sxs-lookup"><span data-stu-id="f85d3-191">This implementation:</span></span>

1. <span data-ttu-id="f85d3-192">API yetkilendirme desteği tarafından sunulan özgün uygulamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-192">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="f85d3-193">Kendi özel mantığını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f85d3-193">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="f85d3-194">İstemci tarafı yolunu koruma (angular)</span><span class="sxs-lookup"><span data-stu-id="f85d3-194">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="f85d3-195">İstemci tarafı bir yolu korumak, yetkilendirme koruyucusu bir rota yapılandırılırken çalıştırılacak korumalara listesine eklenerek yapılır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-195">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="f85d3-196">Örnek olarak, `fetch-data` yolun ana uygulama angular modülünde nasıl yapılandırıldığını görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f85d3-196">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="f85d3-197">Bir yolu korumanın gerçek uç noktayı korumadığını (yine de buna uygulanan bir `[Authorize]` özniteliği gerektirdiğini), ancak kullanıcının kimlik doğrulaması olmadığında yalnızca belirtilen istemci tarafı rotasında gezinmelerini önlediği bahsetmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-197">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="f85d3-198">API isteklerinin kimliğini doğrulama (angular)</span><span class="sxs-lookup"><span data-stu-id="f85d3-198">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="f85d3-199">Uygulama ile barındırılan API 'lere yönelik kimlik doğrulama istekleri, uygulama tarafından tanımlanan HTTP istemci yakalayıcısı kullanılarak otomatik olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-199">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="f85d3-200">İstemci tarafı yolunu koruma (tepki verme)</span><span class="sxs-lookup"><span data-stu-id="f85d3-200">Protect a client-side route (React)</span></span>

<span data-ttu-id="f85d3-201">Düz `Route` bileşeni yerine `AuthorizeRoute` bileşenini kullanarak bir istemci tarafı yolunu koruyun.</span><span class="sxs-lookup"><span data-stu-id="f85d3-201">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="f85d3-202">Örneğin, `fetch-data` yolunun `App` bileşeni içinde nasıl yapılandırıldığı hakkında dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="f85d3-202">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="f85d3-203">Bir yolu koruma:</span><span class="sxs-lookup"><span data-stu-id="f85d3-203">Protecting a route:</span></span>

* <span data-ttu-id="f85d3-204">Gerçek uç noktayı korumaz (hala buna uygulanan bir `[Authorize]` özniteliği gerektirir).</span><span class="sxs-lookup"><span data-stu-id="f85d3-204">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="f85d3-205">Yalnızca kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini engeller.</span><span class="sxs-lookup"><span data-stu-id="f85d3-205">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="f85d3-206">API isteklerinin kimliğini doğrulama (tepki)</span><span class="sxs-lookup"><span data-stu-id="f85d3-206">Authenticate API requests (React)</span></span>

<span data-ttu-id="f85d3-207">Yanıt vererek istekleri doğrulamak, öncelikle `authService` örneği `AuthorizeService`içeri aktararak yapılır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-207">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="f85d3-208">Erişim belirteci `authService` alınır ve aşağıda gösterildiği gibi isteğe iliştirilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-208">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="f85d3-209">Yanıt verme bileşenlerinde, bu iş genellikle `componentDidMount` yaşam döngüsü yönteminde veya bazı Kullanıcı etkileşiminden elde edilen sonuç olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-209">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="f85d3-210">AuthService 'i bileşeninizdeki içeri aktarın</span><span class="sxs-lookup"><span data-stu-id="f85d3-210">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="f85d3-211">Erişim belirtecini alma ve yanıta iliştirme</span><span class="sxs-lookup"><span data-stu-id="f85d3-211">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="f85d3-212">Üretime dağıtma</span><span class="sxs-lookup"><span data-stu-id="f85d3-212">Deploy to production</span></span>

<span data-ttu-id="f85d3-213">Uygulamayı üretime dağıtmak için aşağıdaki kaynakların sağlanması gerekir:</span><span class="sxs-lookup"><span data-stu-id="f85d3-213">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="f85d3-214">Kimliği kullanıcı hesaplarının ve IdentityServer 'ın verdiği bir veritabanı.</span><span class="sxs-lookup"><span data-stu-id="f85d3-214">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="f85d3-215">Belirteçleri imzalamak için kullanılacak bir üretim sertifikası.</span><span class="sxs-lookup"><span data-stu-id="f85d3-215">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="f85d3-216">Bu sertifika için belirli bir gereksinim yoktur; otomatik olarak imzalanan bir sertifika veya bir CA yetkilisi tarafından sağlanan bir sertifika olabilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-216">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="f85d3-217">PowerShell veya OpenSSL gibi standart araçlarla oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-217">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="f85d3-218">Hedef makinelerdeki sertifika deposuna yüklenebilir veya güçlü bir parolayla bir *. pfx* dosyası olarak dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-218">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-websites"></a><span data-ttu-id="f85d3-219">Örnek: Azure Web sitelerine dağıtma</span><span class="sxs-lookup"><span data-stu-id="f85d3-219">Example: Deploy to Azure Websites</span></span>

<span data-ttu-id="f85d3-220">Bu bölümde, sertifika deposunda depolanan bir sertifika kullanılarak uygulamanın Azure Web sitelerine dağıtımı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-220">This section describes deploying the app to Azure websites using a certificate stored in the certificate store.</span></span> <span data-ttu-id="f85d3-221">Uygulamayı sertifika deposundan bir sertifika yükleyecek şekilde değiştirmek için, daha sonraki bir adımda yapılandırdığınızda App Service planının en azından Standart katmanda olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-221">To modify the app to load a certificate from the certificate store, the App Service plan needs to be on at least the Standard tier when you configure in a later step.</span></span> <span data-ttu-id="f85d3-222">Uygulamanın *appSettings. JSON* dosyasında, `IdentityServer` bölümünü, anahtar ayrıntılarını içerecek şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f85d3-222">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="f85d3-223">Sertifikadaki ad özelliği, sertifikanın ayırt edici konusuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-223">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>
* <span data-ttu-id="f85d3-224">Depolama konumu, sertifikanın nereden yükleneceğini (`CurrentUser` veya `LocalMachine`) temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-224">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="f85d3-225">Mağaza adı, sertifikanın depolandığı sertifika deposunun adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-225">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="f85d3-226">Bu durumda, kişisel Kullanıcı deposuna işaret eder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-226">In this case, it points to the personal user store.</span></span>

<span data-ttu-id="f85d3-227">Azure Web siteleri 'ne dağıtmak için, gerekli Azure kaynaklarını oluşturmak ve uygulamayı üretime dağıtmak üzere uygulamayı [Azure 'A dağıtma](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) bölümündeki adımları izleyerek uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="f85d3-227">To deploy to Azure Websites, deploy the app following the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="f85d3-228">Yukarıdaki yönergeleri uyguladıktan sonra, uygulama Azure 'a dağıtılır ancak henüz işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-228">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="f85d3-229">Uygulama tarafından kullanılan sertifikanın hala ayarlanması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="f85d3-229">The certificate used by the app still needs to be set up.</span></span> <span data-ttu-id="f85d3-230">Kullanılacak sertifika için parmak izini bulun ve [sertifikalarınızı yükleme](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)bölümünde açıklanan adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="f85d3-230">Locate the thumbprint for the certificate to be used, and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="f85d3-231">Bu adımlar SSL 'den bahsetirken portalda, uygulama ile kullanmak üzere sağlanan sertifikayı karşıya yükleyebileceğiniz **özel sertifikalar** bölümü vardır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-231">While these steps mention SSL, there's a **Private certificates** section on the portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="f85d3-232">Bu adımdan sonra uygulamayı yeniden başlatın ve çalışır olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-232">After this step, restart the app and it should be functional.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="f85d3-233">Diğer yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="f85d3-233">Other configuration options</span></span>

<span data-ttu-id="f85d3-234">API yetkilendirmesi desteği, IdentityServer 'ın en üstünde bir dizi kural, varsayılan değer ve, maça deneyimini basitleştirecek geliştirmeler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f85d3-234">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="f85d3-235">Daha az ki, ASP.NET Core tümleştirmeler senaryonuzu kapsamadıysanız, IdentityServer 'ın tam gücü arka planda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-235">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="f85d3-236">ASP.NET Core desteği, tüm uygulamaların kuruluşumuza göre oluşturulduğu ve dağıtıldığı "birinci taraf" uygulamalara odaklanır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-236">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="f85d3-237">Bu nedenle, izin veya Federasyon gibi şeyler için destek sunulmaz.</span><span class="sxs-lookup"><span data-stu-id="f85d3-237">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="f85d3-238">Bu senaryolar için IdentityServer kullanın ve belgelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="f85d3-238">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="f85d3-239">Uygulama profilleri</span><span class="sxs-lookup"><span data-stu-id="f85d3-239">Application profiles</span></span>

<span data-ttu-id="f85d3-240">Uygulama profilleri, parametrelerini daha fazla tanımlayan uygulamalar için önceden tanımlanmış yapılandırlardır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-240">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="f85d3-241">Şu anda, aşağıdaki profiller desteklenir:</span><span class="sxs-lookup"><span data-stu-id="f85d3-241">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="f85d3-242">`IdentityServerSPA`: IdentityServer 'ın yanı sıra tek bir birim olarak barındırılan bir SPA 'yı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-242">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="f85d3-243">`redirect_uri` varsayılan olarak `/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="f85d3-243">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="f85d3-244">`post_logout_redirect_uri` varsayılan olarak `/authentication/logout-callback`.</span><span class="sxs-lookup"><span data-stu-id="f85d3-244">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="f85d3-245">Kapsam kümesi, uygulamadaki API 'Ler için tanımlanan `openid`, `profile`ve tüm kapsamları içerir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-245">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="f85d3-246">İzin verilen OıDC yanıt türleri kümesi tek tek `id_token token` veya her biri (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="f85d3-246">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="f85d3-247">İzin verilen yanıt modu `fragment`.</span><span class="sxs-lookup"><span data-stu-id="f85d3-247">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="f85d3-248">`SPA`: IdentityServer ile barındırılmayan bir SPA 'yı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-248">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="f85d3-249">Kapsam kümesi, uygulamadaki API 'Ler için tanımlanan `openid`, `profile`ve tüm kapsamları içerir.</span><span class="sxs-lookup"><span data-stu-id="f85d3-249">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="f85d3-250">İzin verilen OıDC yanıt türleri kümesi tek tek `id_token token` veya her biri (`id_token`, `token`).</span><span class="sxs-lookup"><span data-stu-id="f85d3-250">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="f85d3-251">İzin verilen yanıt modu `fragment`.</span><span class="sxs-lookup"><span data-stu-id="f85d3-251">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="f85d3-252">`IdentityServerJwt`: IdentityServer ile birlikte barındırılan bir API 'YI temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-252">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="f85d3-253">Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-253">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="f85d3-254">`API`: IdentityServer ile barındırılmayan bir API 'YI temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f85d3-254">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="f85d3-255">Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f85d3-255">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="f85d3-256">AppSettings aracılığıyla yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f85d3-256">Configuration through AppSettings</span></span>

<span data-ttu-id="f85d3-257">Uygulamaları `Clients` veya `Resources`listesine ekleyerek yapılandırma sistemi aracılığıyla yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f85d3-257">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="f85d3-258">Aşağıdaki örnekte gösterildiği gibi, her bir istemcinin `redirect_uri` ve `post_logout_redirect_uri` özelliğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f85d3-258">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="f85d3-259">Kaynakları yapılandırırken, kaynak için kapsamları aşağıda gösterildiği gibi yapılandırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f85d3-259">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="f85d3-260">Kod aracılığıyla yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f85d3-260">Configuration through code</span></span>

<span data-ttu-id="f85d3-261">Ayrıca, seçenekleri yapılandırmak için bir eylem alan `AddApiAuthorization` aşırı yüklemesini kullanarak, istemcileri ve kaynakları kod aracılığıyla da yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f85d3-261">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="f85d3-262">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f85d3-262">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
