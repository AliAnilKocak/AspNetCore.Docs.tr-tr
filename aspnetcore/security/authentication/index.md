---
title: ASP.NET Core kimlik doğrulamasına genel bakış
author: mjrousos
description: ASP.NET Core kimlik doğrulaması hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/index
ms.openlocfilehash: 4e47bd91ce15836035d3e8f0a8ceed264f308b22
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768643"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="572e3-103">ASP.NET Core kimlik doğrulamasına genel bakış</span><span class="sxs-lookup"><span data-stu-id="572e3-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="572e3-104">, [Mike Rousos](https://github.com/mjrousos) tarafından</span><span class="sxs-lookup"><span data-stu-id="572e3-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="572e3-105">Kimlik doğrulaması, bir kullanıcının kimliğini belirleme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="572e3-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="572e3-106">[Yetkilendirme](xref:security/authorization/introduction) , bir kullanıcının bir kaynağa erişip erişemeyeceğini belirleme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="572e3-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="572e3-107">ASP.NET Core, kimlik doğrulaması, kimlik doğrulama `IAuthenticationService` [ara yazılımı](xref:fundamentals/middleware/index)tarafından kullanılan tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="572e3-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="572e3-108">Kimlik doğrulama hizmeti, kimlik doğrulama ile ilgili eylemleri tamamlamaya yönelik kayıtlı kimlik doğrulama işleyicilerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="572e3-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="572e3-109">Kimlik doğrulaması ile ilgili eylemlere örnek olarak şunlar verilebilir:</span><span class="sxs-lookup"><span data-stu-id="572e3-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="572e3-110">Kullanıcı kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="572e3-110">Authenticating a user.</span></span>
* <span data-ttu-id="572e3-111">Kimliği doğrulanmamış bir Kullanıcı kısıtlı bir kaynağa erişmeyi denediğinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="572e3-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="572e3-112">Kayıtlı kimlik doğrulama işleyicileri ve yapılandırma seçenekleri "şemalar" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="572e3-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="572e3-113">Kimlik doğrulama düzenleri, kimlik doğrulama hizmetleri ' de `Startup.ConfigureServices`kaydedilerek belirtilir:</span><span class="sxs-lookup"><span data-stu-id="572e3-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="572e3-114">Bir çağrısından sonra şemaya özgü genişletme yöntemini çağırarak `services.AddAuthentication` (örneğin, `AddJwtBearer` veya `AddCookie`gibi).</span><span class="sxs-lookup"><span data-stu-id="572e3-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="572e3-115">Bu uzantı yöntemleri, uygun ayarlarla şemaları kaydetmek için [Authenticationbuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) kullanır.</span><span class="sxs-lookup"><span data-stu-id="572e3-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="572e3-116">Daha seyrek, [Authenticationbuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) doğrudan çağırarak.</span><span class="sxs-lookup"><span data-stu-id="572e3-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="572e3-117">Örneğin, aşağıdaki kod, tanımlama bilgisi ve JWT taşıyıcı kimlik doğrulama şemaları için kimlik doğrulama hizmetlerini ve işleyicileri kaydeder:</span><span class="sxs-lookup"><span data-stu-id="572e3-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="572e3-118">`AddAuthentication` Parametresi `JwtBearerDefaults.AuthenticationScheme` , belirli bir şema istenmediğinde varsayılan olarak kullanılacak düzenin adıdır.</span><span class="sxs-lookup"><span data-stu-id="572e3-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="572e3-119">Birden çok düzen kullanılırsa, yetkilendirme ilkeleri (veya yetkilendirme öznitelikleri) kullanıcının kimliğini doğrulamak için bağımlı oldukları [kimlik doğrulama düzenini (veya düzenleri) belirtebilir](xref:security/authorization/limitingidentitybyscheme) .</span><span class="sxs-lookup"><span data-stu-id="572e3-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="572e3-120">Yukarıdaki örnekte, tanımlama bilgisi kimlik doğrulama düzeni adı belirtilerek kullanılabilir (`CookieAuthenticationDefaults.AuthenticationScheme` varsayılan olarak, çağrılırken `AddCookie`farklı bir ad sağlansa da).</span><span class="sxs-lookup"><span data-stu-id="572e3-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="572e3-121">Bazı durumlarda, çağrısı `AddAuthentication` diğer uzantı yöntemleri tarafından otomatik olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="572e3-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="572e3-122">Örneğin, [ASP.NET Core Identity ](xref:security/authentication/identity) `AddAuthentication` kullanırken dahili olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="572e3-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="572e3-123">Kimlik doğrulama ara yazılımı, uygulamasının `Startup.Configure` ' de <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> uzantı yöntemi çağırarak öğesine eklenir `IApplicationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="572e3-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="572e3-124">Çağıran `UseAuthentication` , daha önce kaydedilen kimlik doğrulama düzenlerini kullanan ara yazılımı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="572e3-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="572e3-125">Kimliği `UseAuthentication` doğrulanan kullanıcılara bağlı olan herhangi bir ara yazılımın önüne çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="572e3-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="572e3-126">Endpoint Routing kullanılırken, çağrısının gitmesi `UseAuthentication` gerekir:</span><span class="sxs-lookup"><span data-stu-id="572e3-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="572e3-127">Ardından `UseRouting`, kimlik doğrulama kararlarında yol bilgileri kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="572e3-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="572e3-128">Önce `UseEndpoints`, kullanıcıların, uç noktalara erişmeden önce kimlik doğrulaması yapılır.</span><span class="sxs-lookup"><span data-stu-id="572e3-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="572e3-129">Kimlik doğrulama kavramları</span><span class="sxs-lookup"><span data-stu-id="572e3-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="572e3-130">Kimlik doğrulama düzeni</span><span class="sxs-lookup"><span data-stu-id="572e3-130">Authentication scheme</span></span>

<span data-ttu-id="572e3-131">Kimlik doğrulama düzeni, öğesine karşılık gelen bir addır:</span><span class="sxs-lookup"><span data-stu-id="572e3-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="572e3-132">Bir kimlik doğrulama işleyicisi.</span><span class="sxs-lookup"><span data-stu-id="572e3-132">An authentication handler.</span></span>
* <span data-ttu-id="572e3-133">İşleyicinin belirli bir örneğini yapılandırma seçenekleri.</span><span class="sxs-lookup"><span data-stu-id="572e3-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="572e3-134">Şemaları, ilişkili işleyicinin kimlik doğrulama, sınama ve fordeklarasyonu davranışlarına başvurma mekanizması olarak faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="572e3-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="572e3-135">Örneğin, bir yetkilendirme ilkesi, kullanıcının kimliğini doğrulamak için hangi kimlik doğrulama şemasının (veya düzenlerinin) kullanılması gerektiğini belirtmek için şema adlarını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="572e3-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="572e3-136">Kimlik doğrulamasını yapılandırırken, varsayılan kimlik doğrulama düzenini belirlemek yaygın bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="572e3-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="572e3-137">Varsayılan şema, bir kaynak belirli bir düzeni istemediği takdirde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="572e3-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="572e3-138">Şunları da mümkündür:</span><span class="sxs-lookup"><span data-stu-id="572e3-138">It's also possible to:</span></span>

* <span data-ttu-id="572e3-139">Kimlik doğrulama, sınama ve fordeklarasyon eylemleri için kullanılacak farklı varsayılan şemaları belirtin.</span><span class="sxs-lookup"><span data-stu-id="572e3-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="572e3-140">Birden çok düzeni [ilke düzenlerini](xref:security/authentication/policyschemes)kullanarak bir tek birleştirme.</span><span class="sxs-lookup"><span data-stu-id="572e3-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="572e3-141">Kimlik doğrulama işleyicisi</span><span class="sxs-lookup"><span data-stu-id="572e3-141">Authentication handler</span></span>

<span data-ttu-id="572e3-142">Kimlik doğrulama işleyicisi:</span><span class="sxs-lookup"><span data-stu-id="572e3-142">An authentication handler:</span></span>

* <span data-ttu-id="572e3-143">, Bir düzenin davranışını uygulayan bir türdür.</span><span class="sxs-lookup"><span data-stu-id="572e3-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="572e3-144">Veya <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>' den <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> türetilir.</span><span class="sxs-lookup"><span data-stu-id="572e3-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="572e3-145">, Kullanıcıların kimliğini doğrulamak için Birincil sorumluluğa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="572e3-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="572e3-146">Kimlik doğrulama düzeninin yapılandırmasına ve gelen istek bağlamına göre, kimlik doğrulama işleyicileri:</span><span class="sxs-lookup"><span data-stu-id="572e3-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="572e3-147">Kimlik <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> doğrulaması başarılı olursa kullanıcının kimliğini temsil eden nesneleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="572e3-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="572e3-148">Kimlik doğrulaması başarısız olursa ' No Result ' veya ' Failure ' döndürün.</span><span class="sxs-lookup"><span data-stu-id="572e3-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="572e3-149">Kullanıcıların kaynaklara erişmeyi deneyeceği işlemler ve yasaklamaya yönelik yöntemlere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="572e3-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="572e3-150">Bunlara erişim yetkisi yoktur (fordeklarasyon).</span><span class="sxs-lookup"><span data-stu-id="572e3-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="572e3-151">Kimliği doğrulanmamış olduğunda (zorluk).</span><span class="sxs-lookup"><span data-stu-id="572e3-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="572e3-152">Kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="572e3-152">Authenticate</span></span>

<span data-ttu-id="572e3-153">Kimlik doğrulama düzeninin kimlik doğrulama eylemi, kullanıcının kimliğini istek bağlamına göre oluşturmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="572e3-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="572e3-154">Kimlik doğrulamanın başarılı <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> olup olmadığını ve bu durumda kullanıcının kimlik doğrulama biletinde kimliğini belirten bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="572e3-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="572e3-155">Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="572e3-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="572e3-156">Kimlik doğrulama örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="572e3-156">Authenticate examples include:</span></span>

* <span data-ttu-id="572e3-157">Tanımlama bilgisi kimlik doğrulama şeması kullanıcının kimlik bilgilerinden kimliğini oluşturan.</span><span class="sxs-lookup"><span data-stu-id="572e3-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="572e3-158">Bir JWT taşıyıcı şeması, kullanıcının kimliğini oluşturmak için bir JWT taşıyıcı belirtecini seri durumdan çıkarma ve doğrulama.</span><span class="sxs-lookup"><span data-stu-id="572e3-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="572e3-159">Sınama</span><span class="sxs-lookup"><span data-stu-id="572e3-159">Challenge</span></span>

<span data-ttu-id="572e3-160">Kimliği doğrulanmamış bir kullanıcı kimlik doğrulaması gerektiren bir uç nokta istediğinde yetkilendirme ile kimlik doğrulama sınaması çağrılır.</span><span class="sxs-lookup"><span data-stu-id="572e3-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="572e3-161">Örneğin, anonim bir Kullanıcı kısıtlı bir kaynak istediğinde veya bir oturum açma bağlantısına tıkladığı zaman bir kimlik doğrulama sınaması verilir.</span><span class="sxs-lookup"><span data-stu-id="572e3-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="572e3-162">Yetkilendirme, belirtilen kimlik doğrulama düzenlerini kullanarak bir zorluk çağırır ya da hiçbiri belirtilmemişse varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="572e3-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="572e3-163">Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="572e3-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="572e3-164">Kimlik doğrulama sınaması örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="572e3-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="572e3-165">Bir tanımlama bilgisi kimlik doğrulama şeması kullanıcıyı bir oturum açma sayfasına yönlendiriyor.</span><span class="sxs-lookup"><span data-stu-id="572e3-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="572e3-166">`www-authenticate: bearer` Üst bilgiyle 401 sonucu döndüren bir JWT taşıyıcı şeması.</span><span class="sxs-lookup"><span data-stu-id="572e3-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="572e3-167">Bir sınama eylemi, kullanıcının istenen kaynağa erişmek için hangi kimlik doğrulama mekanizmasını kullanacağınızı bilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="572e3-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="572e3-168">Yasaklamaz</span><span class="sxs-lookup"><span data-stu-id="572e3-168">Forbid</span></span>

<span data-ttu-id="572e3-169">Kimliği doğrulanmış bir kullanıcı erişimine izin verilmeyen bir kaynağa erişmeyi denediğinde kimlik doğrulama düzeninin fordeklarasyon eylemi yetkilendirme tarafından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="572e3-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="572e3-170">Bkz. <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="572e3-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="572e3-171">Kimlik doğrulama fordeklarasyon örnekleri şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="572e3-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="572e3-172">Bir tanımlama bilgisi kimlik doğrulama şeması, kullanıcıyı erişim yasak bir sayfaya yönlendirdi.</span><span class="sxs-lookup"><span data-stu-id="572e3-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="572e3-173">403 sonucunu döndüren bir JWT taşıyıcı şeması.</span><span class="sxs-lookup"><span data-stu-id="572e3-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="572e3-174">Özel bir kimlik doğrulama şeması, kullanıcının kaynağa erişim isteyebileceği bir sayfaya yeniden yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="572e3-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="572e3-175">Bir fordeklarasyon eylemi, kullanıcının şunları bilmesini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="572e3-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="572e3-176">Bunlar doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="572e3-176">They are authenticated.</span></span>
* <span data-ttu-id="572e3-177">İstenen kaynağa erişmelerine izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="572e3-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="572e3-178">Challenge ve fordeklarasyon arasındaki farklılıklar için aşağıdaki bağlantılara bakın:</span><span class="sxs-lookup"><span data-stu-id="572e3-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="572e3-179">[İşletimsel kaynak Işleyicisiyle Challenge ve fordeklarasyonu](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="572e3-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="572e3-180">[Çekişme ve fordeklarasyon arasındaki farklar](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="572e3-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="572e3-181">Kiracı başına kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="572e3-181">Authentication providers per tenant</span></span>

<span data-ttu-id="572e3-182">ASP.NET Core Framework 'te çok kiracılı kimlik doğrulaması için yerleşik bir çözüm yoktur.</span><span class="sxs-lookup"><span data-stu-id="572e3-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="572e3-183">Müşterilerin, yerleşik özellikleri kullanarak bir tane yazabilmesi kesinlikle mümkün olsa da, müşterilerin bu amaçla daha fazla [çekirdeğe](https://www.orchardcore.net/) bakmalarını öneririz.</span><span class="sxs-lookup"><span data-stu-id="572e3-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="572e3-184">Orchard Core:</span><span class="sxs-lookup"><span data-stu-id="572e3-184">Orchard Core is:</span></span>

* <span data-ttu-id="572e3-185">ASP.NET Core ile oluşturulan açık kaynaklı modüler ve çok kiracılı bir uygulama çerçevesi.</span><span class="sxs-lookup"><span data-stu-id="572e3-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="572e3-186">Bu uygulama çerçevesinin üzerine inşa edilen bir içerik yönetim sistemi (CMS).</span><span class="sxs-lookup"><span data-stu-id="572e3-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="572e3-187">Kiracı başına kimlik doğrulama sağlayıcılarının bir örneği için bkz. [Orchard Core](https://github.com/OrchardCMS/OrchardCore) kaynağı.</span><span class="sxs-lookup"><span data-stu-id="572e3-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="572e3-188">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="572e3-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
