---
title: ASP.NET Core dış sağlayıcılardan ek talepler ve belirteçler kalıcı hale getirme
author: guardrex
description: Dış sağlayıcılardan ek talepler ve belirteçler oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 44b3e72085e6265319b53b548f7f7ddde2adbd14
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828587"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="f4913-103">ASP.NET Core dış sağlayıcılardan ek talepler ve belirteçler kalıcı hale getirme</span><span class="sxs-lookup"><span data-stu-id="f4913-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="f4913-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f4913-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4913-105">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="f4913-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f4913-106">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="f4913-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f4913-107">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4913-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f4913-108">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="f4913-108">Prerequisites</span></span>

<span data-ttu-id="f4913-109">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="f4913-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f4913-110">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="f4913-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f4913-111">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f4913-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f4913-112">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="f4913-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f4913-113">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="f4913-113">Set the client ID and client secret</span></span>

<span data-ttu-id="f4913-114">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="f4913-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f4913-115">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f4913-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f4913-116">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f4913-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f4913-117">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="f4913-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f4913-118">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f4913-119">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f4913-120">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f4913-121">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f4913-122">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="f4913-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f4913-123">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="f4913-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f4913-124">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="f4913-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f4913-125">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f4913-125">Establish the authentication scope</span></span>

<span data-ttu-id="f4913-126"><xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>belirterek sağlayıcıdan alma izinlerinin listesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="f4913-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f4913-127">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f4913-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f4913-128">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="f4913-128">Provider</span></span>  | <span data-ttu-id="f4913-129">Kapsam</span><span class="sxs-lookup"><span data-stu-id="f4913-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="f4913-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="f4913-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="f4913-131">{1&gt;{2&gt;Google&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="f4913-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="f4913-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f4913-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="f4913-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="f4913-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="f4913-134">Örnek uygulamada, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder><xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> çağrıldığında, Google 'ın `userinfo.profile` kapsamı Framework tarafından otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f4913-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f4913-135">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f4913-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f4913-136">Aşağıdaki örnekte, bir kullanıcının Doğum gününü almak için Google `https://www.googleapis.com/auth/user.birthday.read` kapsamı eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="f4913-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f4913-137">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="f4913-137">Map user data keys and create claims</span></span>

<span data-ttu-id="f4913-138">Sağlayıcının seçeneklerinde, oturum açma sırasında okunacak uygulama kimliği için dış sağlayıcının JSON Kullanıcı verilerinde her anahtar/alt anahtar için bir <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> veya <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> belirtin.</span><span class="sxs-lookup"><span data-stu-id="f4913-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f4913-139">Talep türleri hakkında daha fazla bilgi için bkz. <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="f4913-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f4913-140">Örnek uygulama, Google user Data 'daki `locale` ve `picture` anahtarlarından yerel ayar (`urn:google:locale`) ve resim (`urn:google:picture`) talepleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f4913-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f4913-141">`Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, bir <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>ile uygulamada oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="f4913-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f4913-142">Oturum açma işlemi sırasında <xref:Microsoft.AspNetCore.Identity.UserManager%601>, <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>Kullanıcı verileri için `ApplicationUser` taleplerini depolayabilirler.</span><span class="sxs-lookup"><span data-stu-id="f4913-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f4913-143">Örnek uygulamada `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*), <xref:System.Security.Claims.ClaimTypes.GivenName>için bir talep da dahil olmak üzere oturum açmış `ApplicationUser`için yerel ayar (`urn:google:locale`) ve resim (`urn:google:picture`) taleplerini belirler:</span><span class="sxs-lookup"><span data-stu-id="f4913-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f4913-144">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulama tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="f4913-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f4913-145">Kimlik doğrulama tanımlama bilgisi çok büyükse, bunun nedeni uygulamanın başarısız olmasına neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="f4913-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f4913-146">Tarayıcı, tanımlama bilgisi üstbilgisinin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="f4913-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f4913-147">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="f4913-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="f4913-148">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="f4913-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f4913-149">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="f4913-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f4913-150">Kimlik bilgisi kimlik doğrulaması ara yazılımı için özel bir <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> kullanın, kimlik istekleri arasında depolama <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore></span><span class="sxs-lookup"><span data-stu-id="f4913-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f4913-151">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="f4913-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f4913-152">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="f4913-152">Save the access token</span></span>

<span data-ttu-id="f4913-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>, başarılı bir yetkilendirmeden sonra erişim ve yenileme belirteçlerinin <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> depolanması gerekip gerekmediğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f4913-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f4913-154">Son kimlik doğrulama tanımlama bilgisinin boyutunu azaltmak için `SaveTokens` varsayılan olarak `false` ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f4913-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f4913-155">Örnek uygulama, `SaveTokens` değerini <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>`true` olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f4913-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f4913-156">`OnPostConfirmationAsync` yürütüldüğünde, erişim belirtecini ([Externalloginınfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) `ApplicationUser``AuthenticationProperties`dış sağlayıcıdan depolayın.</span><span class="sxs-lookup"><span data-stu-id="f4913-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f4913-157">Örnek uygulama, erişim belirtecini *Hesap/ExternalLogin. cshtml. cs*içinde `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) olarak kaydeder:</span><span class="sxs-lookup"><span data-stu-id="f4913-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f4913-158">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="f4913-158">How to add additional custom tokens</span></span>

<span data-ttu-id="f4913-159">`SaveTokens`bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini göstermek için, örnek uygulama, `TicketCreated`[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) geçerli <xref:System.DateTime> ile bir <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> ekler:</span><span class="sxs-lookup"><span data-stu-id="f4913-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f4913-160">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="f4913-160">Creating and adding claims</span></span>

<span data-ttu-id="f4913-161">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f4913-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f4913-162">Daha fazla bilgi için <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>bakın.</span><span class="sxs-lookup"><span data-stu-id="f4913-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f4913-163">Kullanıcılar, <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> türeterek ve soyut <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metodunu uygulayarak özel eylemleri tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f4913-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f4913-164">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f4913-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f4913-165">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="f4913-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="f4913-166">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> için tüm talep eylemlerini koleksiyondan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f4913-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f4913-167">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> talebini siler.</span><span class="sxs-lookup"><span data-stu-id="f4913-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f4913-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>, öncelikle protokol tarafından oluşturulan talepleri kaldırmak için [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f4913-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f4913-169">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="f4913-169">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f4913-170">ASP.NET Core bir uygulama, Facebook, Google, Microsoft ve Twitter gibi dış kimlik doğrulama sağlayıcılarından ek talepler ve belirteçler oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="f4913-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="f4913-171">Her sağlayıcı, platformdaki kullanıcılar hakkında farklı bilgiler ortaya koyar, ancak kullanıcı verilerini alma ve ek taleplere dönüştürme için olan model aynı olur.</span><span class="sxs-lookup"><span data-stu-id="f4913-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="f4913-172">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4913-172">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f4913-173">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="f4913-173">Prerequisites</span></span>

<span data-ttu-id="f4913-174">Uygulamada hangi dış kimlik doğrulama sağlayıcılarının destekileceğine karar verin.</span><span class="sxs-lookup"><span data-stu-id="f4913-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="f4913-175">Her sağlayıcı için, uygulamayı kaydedin ve bir istemci KIMLIĞI ve istemci parolası alın.</span><span class="sxs-lookup"><span data-stu-id="f4913-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="f4913-176">Daha fazla bilgi için bkz. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="f4913-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="f4913-177">Örnek uygulama [Google kimlik doğrulama sağlayıcısını](xref:security/authentication/google-logins)kullanır.</span><span class="sxs-lookup"><span data-stu-id="f4913-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="f4913-178">İstemci KIMLIĞINI ve gizli anahtarı ayarlama</span><span class="sxs-lookup"><span data-stu-id="f4913-178">Set the client ID and client secret</span></span>

<span data-ttu-id="f4913-179">OAuth kimlik doğrulama sağlayıcısı, istemci KIMLIĞI ve istemci parolası kullanarak bir uygulamayla güven ilişkisi kurar.</span><span class="sxs-lookup"><span data-stu-id="f4913-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="f4913-180">Uygulama sağlayıcıya kaydedildiğinde dış kimlik doğrulama sağlayıcısı tarafından uygulama için istemci KIMLIĞI ve istemci gizli anahtarı değerleri oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f4913-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="f4913-181">Uygulamanın kullandığı her bir dış sağlayıcı, sağlayıcının istemci KIMLIĞI ve istemci parolası ile bağımsız olarak yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f4913-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="f4913-182">Daha fazla bilgi için, senaryonuza uygulanan dış kimlik doğrulama sağlayıcısı konularına bakın:</span><span class="sxs-lookup"><span data-stu-id="f4913-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="f4913-183">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f4913-184">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="f4913-185">Microsoft kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f4913-186">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f4913-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f4913-187">Diğer kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="f4913-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="f4913-188">Openıdconnect</span><span class="sxs-lookup"><span data-stu-id="f4913-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="f4913-189">Örnek uygulama Google kimlik doğrulama sağlayıcısını Google tarafından sağlanmış istemci KIMLIĞI ve istemci gizli anahtarı ile yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="f4913-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="f4913-190">Kimlik doğrulama kapsamını oluşturma</span><span class="sxs-lookup"><span data-stu-id="f4913-190">Establish the authentication scope</span></span>

<span data-ttu-id="f4913-191"><xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>belirterek sağlayıcıdan alma izinlerinin listesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="f4913-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="f4913-192">Ortak dış sağlayıcılar için kimlik doğrulama kapsamları aşağıdaki tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f4913-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="f4913-193">Sağlayıcı</span><span class="sxs-lookup"><span data-stu-id="f4913-193">Provider</span></span>  | <span data-ttu-id="f4913-194">Kapsam</span><span class="sxs-lookup"><span data-stu-id="f4913-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="f4913-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="f4913-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="f4913-196">{1&gt;{2&gt;Google&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="f4913-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="f4913-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="f4913-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="f4913-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="f4913-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="f4913-199">Örnek uygulamada, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder><xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> çağrıldığında, Google 'ın `userinfo.profile` kapsamı Framework tarafından otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="f4913-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="f4913-200">Uygulama ek kapsamlar gerektiriyorsa, bunları seçeneklere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f4913-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="f4913-201">Aşağıdaki örnekte, bir kullanıcının Doğum gününü almak için Google `https://www.googleapis.com/auth/user.birthday.read` kapsamı eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="f4913-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="f4913-202">Kullanıcı veri anahtarlarını eşleme ve talepler oluşturma</span><span class="sxs-lookup"><span data-stu-id="f4913-202">Map user data keys and create claims</span></span>

<span data-ttu-id="f4913-203">Sağlayıcının seçeneklerinde, oturum açma sırasında okunacak uygulama kimliği için dış sağlayıcının JSON Kullanıcı verilerinde her anahtar/alt anahtar için bir <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> veya <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> belirtin.</span><span class="sxs-lookup"><span data-stu-id="f4913-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="f4913-204">Talep türleri hakkında daha fazla bilgi için bkz. <xref:System.Security.Claims.ClaimTypes>.</span><span class="sxs-lookup"><span data-stu-id="f4913-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="f4913-205">Örnek uygulama, Google user Data 'daki `locale` ve `picture` anahtarlarından yerel ayar (`urn:google:locale`) ve resim (`urn:google:picture`) talepleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f4913-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="f4913-206">`Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, bir <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>ile uygulamada oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="f4913-206">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="f4913-207">Oturum açma işlemi sırasında <xref:Microsoft.AspNetCore.Identity.UserManager%601>, <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>Kullanıcı verileri için `ApplicationUser` taleplerini depolayabilirler.</span><span class="sxs-lookup"><span data-stu-id="f4913-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="f4913-208">Örnek uygulamada `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*), <xref:System.Security.Claims.ClaimTypes.GivenName>için bir talep da dahil olmak üzere oturum açmış `ApplicationUser`için yerel ayar (`urn:google:locale`) ve resim (`urn:google:picture`) taleplerini belirler:</span><span class="sxs-lookup"><span data-stu-id="f4913-208">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="f4913-209">Varsayılan olarak, bir kullanıcının talepleri kimlik doğrulama tanımlama bilgisinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="f4913-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="f4913-210">Kimlik doğrulama tanımlama bilgisi çok büyükse, bunun nedeni uygulamanın başarısız olmasına neden olabilir:</span><span class="sxs-lookup"><span data-stu-id="f4913-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="f4913-211">Tarayıcı, tanımlama bilgisi üstbilgisinin çok uzun olduğunu algılar.</span><span class="sxs-lookup"><span data-stu-id="f4913-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="f4913-212">İsteğin genel boyutu çok büyük.</span><span class="sxs-lookup"><span data-stu-id="f4913-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="f4913-213">Kullanıcı isteklerini işlemek için büyük miktarda Kullanıcı verisi gerekliyse:</span><span class="sxs-lookup"><span data-stu-id="f4913-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="f4913-214">İstek işleme için Kullanıcı taleplerinin sayısını ve boyutunu yalnızca uygulamanın gerektirdiği şekilde sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="f4913-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="f4913-215">Kimlik bilgisi kimlik doğrulaması ara yazılımı için özel bir <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> kullanın, kimlik istekleri arasında depolama <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore></span><span class="sxs-lookup"><span data-stu-id="f4913-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="f4913-216">Yalnızca istemciye küçük bir oturum tanımlayıcı anahtarı gönderilirken sunucuda büyük miktarlarda kimlik bilgilerini koruyun.</span><span class="sxs-lookup"><span data-stu-id="f4913-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="f4913-217">Erişim belirtecini Kaydet</span><span class="sxs-lookup"><span data-stu-id="f4913-217">Save the access token</span></span>

<span data-ttu-id="f4913-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>, başarılı bir yetkilendirmeden sonra erişim ve yenileme belirteçlerinin <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> depolanması gerekip gerekmediğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f4913-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="f4913-219">Son kimlik doğrulama tanımlama bilgisinin boyutunu azaltmak için `SaveTokens` varsayılan olarak `false` ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f4913-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="f4913-220">Örnek uygulama, `SaveTokens` değerini <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>`true` olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f4913-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="f4913-221">`OnPostConfirmationAsync` yürütüldüğünde, erişim belirtecini ([Externalloginınfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) `ApplicationUser``AuthenticationProperties`dış sağlayıcıdan depolayın.</span><span class="sxs-lookup"><span data-stu-id="f4913-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="f4913-222">Örnek uygulama, erişim belirtecini *Hesap/ExternalLogin. cshtml. cs*içinde `OnPostConfirmationAsync` (Yeni Kullanıcı kaydı) ve `OnGetCallbackAsync` (önceden kaydedilmiş Kullanıcı) olarak kaydeder:</span><span class="sxs-lookup"><span data-stu-id="f4913-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="f4913-223">Ek özel belirteçler ekleme</span><span class="sxs-lookup"><span data-stu-id="f4913-223">How to add additional custom tokens</span></span>

<span data-ttu-id="f4913-224">`SaveTokens`bir parçası olarak depolanan özel bir belirtecin nasıl ekleneceğini göstermek için, örnek uygulama, `TicketCreated`[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) geçerli <xref:System.DateTime> ile bir <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> ekler:</span><span class="sxs-lookup"><span data-stu-id="f4913-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="f4913-225">Talepler oluşturma ve ekleme</span><span class="sxs-lookup"><span data-stu-id="f4913-225">Creating and adding claims</span></span>

<span data-ttu-id="f4913-226">Framework, koleksiyona talepler oluşturmak ve eklemek için ortak eylemler ve genişletme yöntemleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="f4913-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="f4913-227">Daha fazla bilgi için <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> ve <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>bakın.</span><span class="sxs-lookup"><span data-stu-id="f4913-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="f4913-228">Kullanıcılar, <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> türeterek ve soyut <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metodunu uygulayarak özel eylemleri tanımlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f4913-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="f4913-229">Daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="f4913-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="f4913-230">Talep eylemlerinin ve taleplerin kaldırılması</span><span class="sxs-lookup"><span data-stu-id="f4913-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="f4913-231">[Claimactioncollection. Remove (dize)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) , verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> için tüm talep eylemlerini koleksiyondan kaldırır.</span><span class="sxs-lookup"><span data-stu-id="f4913-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="f4913-232">[Claimactioncollectionmapextensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) , kimliğin verilen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> talebini siler.</span><span class="sxs-lookup"><span data-stu-id="f4913-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="f4913-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>, öncelikle protokol tarafından oluşturulan talepleri kaldırmak için [OpenID Connect (OıDC)](/azure/active-directory/develop/v2-protocols-oidc) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f4913-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="f4913-234">Örnek uygulama çıkışı</span><span class="sxs-lookup"><span data-stu-id="f4913-234">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f4913-235">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f4913-235">Additional resources</span></span>

* <span data-ttu-id="f4913-236">[DotNet/aspnetcore mühendislik SocialSample uygulaması](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; bağlantılı örnek uygulama [DotNet/aspnetcore GitHub deposunun](https://github.com/dotnet/AspNetCore) `master` mühendislik dalında bulunur.</span><span class="sxs-lookup"><span data-stu-id="f4913-236">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="f4913-237">`master` dalı, ASP.NET Core sonraki sürümü için etkin geliştirme kapsamındaki kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="f4913-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="f4913-238">Yayınlanmış bir ASP.NET Core sürümü için örnek uygulamanın bir sürümünü görmek için, **dal** açılan listesini kullanarak bir yayın dalı seçin (örneğin `release/{X.Y}`).</span><span class="sxs-lookup"><span data-stu-id="f4913-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
