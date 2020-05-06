---
title: ASP.NET Core olmadan Facebook, Google ve dış sağlayıcı kimlik doğrulamasıIdentity
author: rick-anderson
description: Facebook, Google, Twitter vb. hesap Kullanıcı kimlik doğrulamasını ASP.NET Core Identityolmadan kullanma açıklaması.
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775745"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="af241-103">ASP.NET Core olmadan sosyal oturum açma sağlayıcısı kimlik doğrulamasını kullanmaIdentity</span><span class="sxs-lookup"><span data-stu-id="af241-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="af241-104">, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af241-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af241-105"><xref:security/authentication/social/index>Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar.</span><span class="sxs-lookup"><span data-stu-id="af241-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="af241-106">Bu konu başlığı altında açıklanan yaklaşım, kimlik Identity doğrulama sağlayıcısı olarak ASP.NET Core içerir.</span><span class="sxs-lookup"><span data-stu-id="af241-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="af241-107">Bu örnek, ASP.NET Core Identity **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="af241-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="af241-108">Bu, tüm ASP.NET Core Identityözelliklerinin gerekli olmadığı, ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısıyla tümleştirme gerektirdiğinden uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="af241-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="af241-109">Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır.</span><span class="sxs-lookup"><span data-stu-id="af241-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="af241-110">Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır.</span><span class="sxs-lookup"><span data-stu-id="af241-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="af241-111">Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="af241-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="af241-112">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="af241-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="af241-113">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="af241-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="af241-114">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="af241-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="af241-115">Diğer sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="af241-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="af241-116">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="af241-116">Configuration</span></span>

<span data-ttu-id="af241-117">`ConfigureServices` Yönteminde,, ve <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> yöntemleriyle uygulamanın kimlik doğrulama düzenlerini <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="af241-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="af241-118">Uygulamanın ' i <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ayarlayan çağrı <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span><span class="sxs-lookup"><span data-stu-id="af241-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="af241-119">, `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:</span><span class="sxs-lookup"><span data-stu-id="af241-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="af241-120">Uygulamanın `DefaultScheme` , tanımlama bilgisi olan [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") olarak ayarlanması, uygulamayı bu uzantı yöntemlerinin varsayılan şeması olarak tanımlama bilgilerini kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="af241-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="af241-121">Uygulamanın [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") `ChallengeAsync` <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="af241-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="af241-122">`DefaultChallengeScheme`Geçersiz `DefaultScheme`kılmalar.</span><span class="sxs-lookup"><span data-stu-id="af241-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="af241-123">Ayarlandığında <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> geçersiz kılan `DefaultScheme` ek özellikler için bkz..</span><span class="sxs-lookup"><span data-stu-id="af241-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="af241-124">' `Startup.Configure`De, `UseAuthentication` çağırma `UseAuthorization` `UseRouting` ve `UseEndpoints`arasında çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="af241-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="af241-125">Bu, `HttpContext.User` özelliği ayarlar ve Istekler Için yetkilendirme ara yazılımını çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="af241-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="af241-126">Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="af241-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="af241-127">Tanımlama bilgisi kimlik doğrulaması hakkında daha fazla bilgi <xref:security/authentication/cookie>için bkz..</span><span class="sxs-lookup"><span data-stu-id="af241-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="af241-128">Yetkilendirmeyi uygula</span><span class="sxs-lookup"><span data-stu-id="af241-128">Apply authorization</span></span>

<span data-ttu-id="af241-129">`AuthorizeAttribute` Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin.</span><span class="sxs-lookup"><span data-stu-id="af241-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="af241-130">Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="af241-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="af241-131">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="af241-131">Sign out</span></span>

<span data-ttu-id="af241-132">Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağırın.</span><span class="sxs-lookup"><span data-stu-id="af241-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="af241-133">Aşağıdaki kod, *Dizin* sayfasına `Logout` bir sayfa işleyicisi ekler:</span><span class="sxs-lookup"><span data-stu-id="af241-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="af241-134">Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="af241-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="af241-135">Uygulama `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` , geri dönüş olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="af241-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af241-136">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="af241-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="af241-137"><xref:security/authentication/social/index>Kullanıcıların, OAuth 2,0 kullanarak dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle oturum açmasını açıklar.</span><span class="sxs-lookup"><span data-stu-id="af241-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="af241-138">Bu konu başlığı altında açıklanan yaklaşım, kimlik Identity doğrulama sağlayıcısı olarak ASP.NET Core içerir.</span><span class="sxs-lookup"><span data-stu-id="af241-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="af241-139">Bu örnek, ASP.NET Core Identity **olmadan** bir dış kimlik doğrulama sağlayıcısının nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="af241-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="af241-140">Bu, tüm ASP.NET Core Identityözelliklerinin gerekli olmadığı, ancak yine de güvenilen bir dış kimlik doğrulama sağlayıcısıyla tümleştirme gerektirdiğinden uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="af241-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="af241-141">Bu örnek, kullanıcıların kimliğini doğrulamak için [Google kimlik doğrulamasını](xref:security/authentication/google-logins) kullanır.</span><span class="sxs-lookup"><span data-stu-id="af241-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="af241-142">Google kimlik doğrulamasını kullanmak, oturum açma işlemini Google 'a yönetmenin karmaşıklıklarından çoğunu kaydırır.</span><span class="sxs-lookup"><span data-stu-id="af241-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="af241-143">Farklı bir dış kimlik doğrulama sağlayıcısıyla tümleştirme için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="af241-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="af241-144">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="af241-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="af241-145">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="af241-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="af241-146">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="af241-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="af241-147">Diğer sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="af241-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="af241-148">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="af241-148">Configuration</span></span>

<span data-ttu-id="af241-149">`ConfigureServices` Yönteminde,, ve `AddGoogle` yöntemleriyle uygulamanın kimlik doğrulama düzenlerini `AddAuthentication` `AddCookie`yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="af241-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="af241-150">[Addaduthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) çağrısı, uygulamanın [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="af241-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="af241-151">, `DefaultScheme` Aşağıdaki `HttpContext` kimlik doğrulama uzantısı yöntemleri tarafından kullanılan varsayılan şemadır:</span><span class="sxs-lookup"><span data-stu-id="af241-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="af241-152">Uygulamanın `DefaultScheme` , tanımlama bilgisi olan [ıeauthenticationdefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") olarak ayarlanması, uygulamayı bu uzantı yöntemlerinin varsayılan şeması olarak tanımlama bilgilerini kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="af241-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="af241-153">Uygulamanın [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") `ChallengeAsync` <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> olarak ayarlanması, uygulamayı Google 'ın öğesine yapılan çağrılar için varsayılan düzen olarak kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="af241-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="af241-154">`DefaultChallengeScheme`Geçersiz `DefaultScheme`kılmalar.</span><span class="sxs-lookup"><span data-stu-id="af241-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="af241-155">Ayarlandığında <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> geçersiz kılan `DefaultScheme` ek özellikler için bkz..</span><span class="sxs-lookup"><span data-stu-id="af241-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="af241-156">`Configure` Yönteminde, `HttpContext.User` özelliğini ayarlayan kimlik doğrulama ara yazılımını çağırmak için `UseAuthentication` yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="af241-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="af241-157">Veya `UseMvc`çağrılmadan `UseAuthentication` `UseMvcWithDefaultRoute` önce yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="af241-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="af241-158">Kimlik doğrulama şemaları hakkında daha fazla bilgi için bkz. [kimlik doğrulama kavramları](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="af241-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="af241-159">Tanımlama bilgisi kimlik doğrulaması hakkında daha fazla bilgi <xref:security/authentication/cookie>için bkz..</span><span class="sxs-lookup"><span data-stu-id="af241-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="af241-160">Yetkilendirmeyi uygula</span><span class="sxs-lookup"><span data-stu-id="af241-160">Apply authorization</span></span>

<span data-ttu-id="af241-161">`AuthorizeAttribute` Özniteliği bir denetleyiciye, eyleme veya sayfaya uygulayarak uygulamanın kimlik doğrulama yapılandırmasını test edin.</span><span class="sxs-lookup"><span data-stu-id="af241-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="af241-162">Aşağıdaki kod, *Gizlilik* sayfasına erişimi doğrulanan kullanıcılarla sınırlandırır:</span><span class="sxs-lookup"><span data-stu-id="af241-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="af241-163">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="af241-163">Sign out</span></span>

<span data-ttu-id="af241-164">Geçerli kullanıcının oturumunu kapatmak ve tanımlama bilgilerini silmek için [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)çağırın.</span><span class="sxs-lookup"><span data-stu-id="af241-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="af241-165">Aşağıdaki kod, *Dizin* sayfasına `Logout` bir sayfa işleyicisi ekler:</span><span class="sxs-lookup"><span data-stu-id="af241-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="af241-166">Çağrısının `SignOutAsync` bir kimlik doğrulama düzeni belirtmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="af241-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="af241-167">Uygulama `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` , geri dönüş olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="af241-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af241-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="af241-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
