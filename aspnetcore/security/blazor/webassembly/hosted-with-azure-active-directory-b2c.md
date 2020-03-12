---
title: Azure Active Directory B2C ile bir ASP.NET Core Blazor Weelsembly barındırılan uygulaması güvenli hale getirme
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 232a4247f8bea23eec3dc35cba4659c88887124d
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083883"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="88dce-102">Azure Active Directory B2C ile bir ASP.NET Core Blazor Weelsembly barındırılan uygulaması güvenli hale getirme</span><span class="sxs-lookup"><span data-stu-id="88dce-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="88dce-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="88dce-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="88dce-104">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir Blazor WebAssembly tek başına uygulamasının nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="88dce-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="88dce-105">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="88dce-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="88dce-106">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="88dce-106">Create a tenant</span></span>

<span data-ttu-id="88dce-107">Öğreticideki yönergeleri izleyin: bir AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturun](/azure/active-directory-b2c/tutorial-create-tenant) ve aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="88dce-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="88dce-108">AAD B2C örneği (örneğin, sonunda eğik çizgi içeren `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="88dce-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="88dce-109">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="88dce-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="88dce-110">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="88dce-110">Register a server API app</span></span>

<span data-ttu-id="88dce-111">Eğitim bölümünde yer alan yönergeleri izleyin: *sunucu API 'si uygulamasına* yönelik AAD uygulamasını Azure portal **Azure Active Directory** > **uygulama kayıtları** alanına kaydetmek için [Azure Active Directory B2C bir uygulamayı kaydetme](/azure/active-directory-b2c/tutorial-register-applications) .</span><span class="sxs-lookup"><span data-stu-id="88dce-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="88dce-112">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="88dce-112">Select **New registration**.</span></span>
1. <span data-ttu-id="88dce-113">Uygulama için bir **ad** sağlayın (örneğin, **Blazor sunucusu AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="88dce-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="88dce-114">**Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="88dce-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="88dce-115">Bu deneyim için (çok kiracılı).</span><span class="sxs-lookup"><span data-stu-id="88dce-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="88dce-116">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="88dce-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="88dce-117">\*\* > ,\*\* yönetici tarafından **OpenID 'ye uyum verildiğini ve offline_access izinlerinin** etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="88dce-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="88dce-118">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-118">Select **Register**.</span></span>

<span data-ttu-id="88dce-119">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="88dce-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="88dce-120">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="88dce-121">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="88dce-122">Bir **kapsam adı** sağlayın (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="88dce-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="88dce-123">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="88dce-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="88dce-124">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="88dce-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="88dce-125">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="88dce-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="88dce-126">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-126">Select **Add scope**.</span></span>

<span data-ttu-id="88dce-127">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="88dce-127">Record the following information:</span></span>

* <span data-ttu-id="88dce-128">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="88dce-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="88dce-129">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="88dce-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="88dce-130">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI URI 'SI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure portal Istemci KIMLIĞI için varsayılan değer olarak değişebilir)</span><span class="sxs-lookup"><span data-stu-id="88dce-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="88dce-131">Varsayılan kapsam (örneğin, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="88dce-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="88dce-132">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="88dce-132">Register a client app</span></span>

<span data-ttu-id="88dce-133">Eğitim bölümünde yer alan yönergeleri izleyin: **Azure Active Directory** > **uygulama kayıtları** Azure Portal ALANıNA *istemci uygulaması* için AAD uygulaması kaydetmek üzere [bir uygulamayı yeniden Azure Active Directory B2C kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :</span><span class="sxs-lookup"><span data-stu-id="88dce-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="88dce-134">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="88dce-134">Select **New registration**.</span></span>
1. <span data-ttu-id="88dce-135">Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="88dce-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="88dce-136">**Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="88dce-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="88dce-137">Bu deneyim için (çok kiracılı).</span><span class="sxs-lookup"><span data-stu-id="88dce-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="88dce-138">**Yeniden yönlendirme URI 'si** açılan listesini **Web**olarak ayarlayın ve `https://localhost:5001/authentication/login-callback`yeniden yönlendirme URI 'si sağlayın.</span><span class="sxs-lookup"><span data-stu-id="88dce-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="88dce-139">\*\* > ,\*\* yönetici tarafından **OpenID 'ye uyum verildiğini ve offline_access izinlerinin** etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="88dce-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="88dce-140">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-140">Select **Register**.</span></span>

<span data-ttu-id="88dce-141">**Kimlik doğrulama** > **Platform yapılandırmalarında** **Web** > :</span><span class="sxs-lookup"><span data-stu-id="88dce-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="88dce-142">`https://localhost:5001/authentication/login-callback` **yeniden yönlendirme URI 'sinin** mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="88dce-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="88dce-143">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="88dce-144">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="88dce-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="88dce-145">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-145">Select the **Save** button.</span></span>

<span data-ttu-id="88dce-146">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="88dce-146">In **API permissions**:</span></span>

1. <span data-ttu-id="88dce-147">Uygulamanın **Microsoft Graph** > **User. Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="88dce-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="88dce-148">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="88dce-149">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* (örneğin, **Blazor sunucu AAD B2C**) seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="88dce-150">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="88dce-150">Open the **API** list.</span></span>
1. <span data-ttu-id="88dce-151">API 'ye erişimi etkinleştirin (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="88dce-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="88dce-152">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="88dce-153">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="88dce-154">Onaylamak için **Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="88dce-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="88dce-155">**Giriş** > **Azure AD B2C** > **Kullanıcı akışları**:</span><span class="sxs-lookup"><span data-stu-id="88dce-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="88dce-156">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="88dce-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="88dce-157">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="88dce-157">Record the following information:</span></span>

* <span data-ttu-id="88dce-158">*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="88dce-158">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="88dce-159">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="88dce-159">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="88dce-160">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="88dce-160">Create the app</span></span>

<span data-ttu-id="88dce-161">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="88dce-161">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="88dce-162">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, çıkış seçeneğini komuta bir yol (örneğin, `-o BlazorSample`) ile birlikte ekleyin.</span><span class="sxs-lookup"><span data-stu-id="88dce-162">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="88dce-163">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="88dce-163">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="88dce-164">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="88dce-164">Server app configuration</span></span>

<span data-ttu-id="88dce-165">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="88dce-165">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="88dce-166">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="88dce-166">Authentication package</span></span>

<span data-ttu-id="88dce-167">ASP.NET Core Web API 'Lerine yapılan kimlik doğrulama ve yetkilendirme desteği `Microsoft.AspNetCore.Authentication.AzureAD.UI`tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="88dce-167">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="88dce-168">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="88dce-168">Authentication service support</span></span>

<span data-ttu-id="88dce-169">`AddAuthentication` yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="88dce-169">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="88dce-170">`AddAzureADBearer` yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="88dce-170">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="88dce-171">`UseAuthentication` ve `UseAuthorization` şunları doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="88dce-171">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="88dce-172">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="88dce-172">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="88dce-173">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="88dce-173">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="88dce-174">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="88dce-174">App settings</span></span>

<span data-ttu-id="88dce-175">*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="88dce-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="88dce-176">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="88dce-176">WeatherForecast controller</span></span>

<span data-ttu-id="88dce-177">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı) denetleyici. cs*), denetleyiciye uygulanan `[Authorize]` özniteliği ile korunan bir API sunar.</span><span class="sxs-lookup"><span data-stu-id="88dce-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="88dce-178">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="88dce-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="88dce-179">Bu API denetleyicisindeki `[Authorize]` özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="88dce-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="88dce-180">Blazor WebAssembly uygulamasında kullanılan `[Authorize]` özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiği konusunda bir ipucu işlevi görür.</span><span class="sxs-lookup"><span data-stu-id="88dce-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="88dce-181">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="88dce-181">Client app configuration</span></span>

<span data-ttu-id="88dce-182">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="88dce-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="88dce-183">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="88dce-183">Authentication package</span></span>

<span data-ttu-id="88dce-184">Tek bir B2C hesabı (`IndividualB2C`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="88dce-184">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="88dce-185">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="88dce-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="88dce-186">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="88dce-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="88dce-187">Önceki paket başvurusunda `{VERSION}`, <xref:blazor/get-started> makalesinde gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketinin sürümü ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="88dce-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="88dce-188">`Microsoft.Authentication.WebAssembly.Msal` paketi, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketini uygulamaya göre geçişli olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="88dce-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="88dce-189">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="88dce-189">Authentication service support</span></span>

<span data-ttu-id="88dce-190">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına `Microsoft.Authentication.WebAssembly.Msal` paketi tarafından sağlanmış `AddMsalAuthentication` uzantısı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="88dce-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="88dce-191">Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="88dce-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="88dce-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="88dce-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="88dce-193">`AddMsalAuthentication` yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="88dce-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="88dce-194">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="88dce-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="88dce-195">Blazor WebAssembly şablonu, uygulamayı `dotnet new` komutuna (`{APP ID URI}/{DEFAULT SCOPE}`) sunulan varsayılan kapsam için güvenli bir API için bir erişim belirteci isteyecek şekilde otomatik olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="88dce-195">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="88dce-196">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="88dce-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="88dce-197">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="88dce-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="88dce-198">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88dce-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="88dce-199">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88dce-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="88dce-200">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="88dce-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="88dce-201">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="88dce-201">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="88dce-202">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="88dce-202">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="88dce-203">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="88dce-203">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="88dce-204">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="88dce-204">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="88dce-205">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="88dce-205">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="88dce-206">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="88dce-206">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="88dce-207">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="88dce-207">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="88dce-208">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="88dce-208">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
