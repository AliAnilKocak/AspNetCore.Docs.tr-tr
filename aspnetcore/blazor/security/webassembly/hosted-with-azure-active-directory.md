---
title: Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 9332eddd3d428e8a25910d387f95b870926d5ae5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85104001"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="7fa82-102">Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="7fa82-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="7fa82-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="7fa82-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7fa82-104">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir [ Blazor webassembly barındırılan uygulamasının](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="7fa82-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="7fa82-105">AAD 'de uygulama kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="7fa82-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="7fa82-106">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="7fa82-106">Create a tenant</span></span>

<span data-ttu-id="7fa82-107">Hızlı başlangıç: AAD 'de kiracı oluşturmak için [bir kiracı ayarlama](/azure/active-directory/develop/quickstart-create-new-tenant) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="7fa82-108">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="7fa82-108">Register a server API app</span></span>

<span data-ttu-id="7fa82-109">Hızlı Başlangıç bölümündeki yönergeleri izleyin: *sunucu API uygulaması* IÇIN bir AAD uygulaması kaydetmek üzere Microsoft Identity platformu ve sonrakı Azure AAD konularıyla [bir uygulama kaydetme](/azure/active-directory/develop/quickstart-register-app) ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="7fa82-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="7fa82-110">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7fa82-111">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor sunucu AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="7fa82-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="7fa82-112">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="7fa82-113">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7fa82-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="7fa82-114">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="7fa82-115">**Permissions**  >  **OpenID ve offline_access izinleri için yönetici onayı izni ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7fa82-116">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-116">Select **Register**.</span></span>

<span data-ttu-id="7fa82-117">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="7fa82-117">Record the following information:</span></span>

* <span data-ttu-id="7fa82-118">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="7fa82-118">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="7fa82-119">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="7fa82-119">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="7fa82-120">AAD kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-120">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="7fa82-121">**API izinlerinde**, **Microsoft Graph**  >  uygulama oturum açma veya Kullanıcı profili erişimi gerektirmediğinden Microsoft Graph**User. Read** iznini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="7fa82-122">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="7fa82-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="7fa82-123">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="7fa82-124">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="7fa82-125">Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7fa82-126">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="7fa82-127">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="7fa82-128">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="7fa82-129">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-129">Select **Add scope**.</span></span>

<span data-ttu-id="7fa82-130">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="7fa82-130">Record the following information:</span></span>

* <span data-ttu-id="7fa82-131">Uygulama KIMLIĞI URI 'SI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="7fa82-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="7fa82-132">Varsayılan kapsam (örneğin, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="7fa82-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="7fa82-133">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="7fa82-133">Register a client app</span></span>

<span data-ttu-id="7fa82-134">[Hızlı başlangıç: bir uygulamayı Microsoft Identity platformu Ile kaydetme](/azure/active-directory/develop/quickstart-register-app) ve sonrakı Azure AAD konularındaki yönergeleri izleyerek *istemci UYGULAMASı* için bir AAD uygulaması kaydedin ve aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="7fa82-134">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="7fa82-135">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7fa82-136">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="7fa82-136">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="7fa82-137">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-137">Choose a **Supported account types**.</span></span> <span data-ttu-id="7fa82-138">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7fa82-138">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="7fa82-139">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7fa82-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7fa82-140">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7fa82-141">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7fa82-142">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde sunucu uygulamasının özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7fa82-143">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7fa82-144">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7fa82-145">**Permissions**  >  **OpenID ve offline_access izinleri için yönetici onayı izni ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-145">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7fa82-146">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-146">Select **Register**.</span></span>

<span data-ttu-id="7fa82-147">*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-147">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="7fa82-148">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="7fa82-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7fa82-149">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7fa82-150">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7fa82-151">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7fa82-152">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-152">Select the **Save** button.</span></span>

<span data-ttu-id="7fa82-153">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="7fa82-153">In **API permissions**:</span></span>

1. <span data-ttu-id="7fa82-154">Uygulamanın **Microsoft Graph**  >  **User. Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-154">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="7fa82-155">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-155">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="7fa82-156">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* (örneğin, \*\* Blazor sunucu AAD\*\*) seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-156">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="7fa82-157">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-157">Open the **API** list.</span></span>
1. <span data-ttu-id="7fa82-158">API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-158">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7fa82-159">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-159">Select **Add permissions**.</span></span>
1. <span data-ttu-id="7fa82-160">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-160">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="7fa82-161">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-161">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="7fa82-162">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="7fa82-162">Create the app</span></span>

<span data-ttu-id="7fa82-163">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="7fa82-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="7fa82-164">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7fa82-165">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7fa82-166">Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="7fa82-167">Azure Portal, *istemci uygulamasının* **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="7fa82-167">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="7fa82-168">*İstemci uygulaması* rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde *sunucu uygulamasının* özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-168">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="7fa82-169">Bağlantı noktası, *istemci uygulamasının* bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure Portal *istemci uygulamanın* kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-169">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="7fa82-170">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7fa82-170">Server app configuration</span></span>

<span data-ttu-id="7fa82-171">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="7fa82-171">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7fa82-172">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="7fa82-172">Authentication package</span></span>

<span data-ttu-id="7fa82-173">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği [Microsoft. AspNetCore. Authentication. AzureAD. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) paketi tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="7fa82-173">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureAD.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="7fa82-174">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="7fa82-174">Authentication service support</span></span>

<span data-ttu-id="7fa82-175">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7fa82-175">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="7fa82-176"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="7fa82-176">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="7fa82-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>aşağıdakileri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="7fa82-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="7fa82-178">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="7fa82-178">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="7fa82-179">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="7fa82-179">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="7fa82-180">Kullanıcı. Identity . Ada</span><span class="sxs-lookup"><span data-stu-id="7fa82-180">User.Identity.Name</span></span>

<span data-ttu-id="7fa82-181">Varsayılan olarak, sunucu uygulaması API 'SI `User.Identity.Name` talep türünden değer ile doldurulur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (örneğin, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-181">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="7fa82-182">Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` Içindeki [Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7fa82-182">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="7fa82-183">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="7fa82-183">App settings</span></span>

<span data-ttu-id="7fa82-184">Dosyadaki *appsettings.js* , erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="7fa82-184">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="7fa82-185">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7fa82-185">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="7fa82-186">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="7fa82-186">WeatherForecast controller</span></span>

<span data-ttu-id="7fa82-187">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API 'yi [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-187">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="7fa82-188">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="7fa82-188">It's **important** to understand that:</span></span>

* <span data-ttu-id="7fa82-189">Bu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-189">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="7fa82-190">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Blazor Webassembly uygulamasında kullanılan özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="7fa82-190">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="7fa82-191">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7fa82-191">Client app configuration</span></span>

<span data-ttu-id="7fa82-192">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="7fa82-192">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7fa82-193">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="7fa82-193">Authentication package</span></span>

<span data-ttu-id="7fa82-194">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) için otomatik olarak bir paket başvurusu alır ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="7fa82-194">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="7fa82-195">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="7fa82-195">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7fa82-196">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7fa82-196">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="7fa82-197">[Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi, [Microsoft. Aspnetcore. components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketini uygulamaya göre geçişli olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="7fa82-197">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="7fa82-198">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="7fa82-198">Authentication service support</span></span>

<span data-ttu-id="7fa82-199"><xref:System.Net.Http.HttpClient>Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-199">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="7fa82-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7fa82-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="7fa82-201">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="7fa82-201">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="7fa82-202">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-202">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="7fa82-203">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7fa82-203">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7fa82-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7fa82-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="7fa82-205"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="7fa82-205">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7fa82-206">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-206">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="7fa82-207">Yapılandırma, dosya *üzerinde Wwwroot/appsettings.js* tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="7fa82-207">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="7fa82-208">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7fa82-208">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="7fa82-209">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="7fa82-209">Access token scopes</span></span>

<span data-ttu-id="7fa82-210">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="7fa82-210">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="7fa82-211">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="7fa82-211">Included by default in the sign in request.</span></span>
* <span data-ttu-id="7fa82-212">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7fa82-212">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="7fa82-213">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7fa82-213">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="7fa82-214">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="7fa82-214">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="7fa82-215">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="7fa82-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7fa82-216">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="7fa82-216">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7fa82-217">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="7fa82-217">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="7fa82-218">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="7fa82-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7fa82-219">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="7fa82-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="7fa82-220">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="7fa82-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7fa82-221">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="7fa82-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7fa82-222">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="7fa82-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="7fa82-223">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="7fa82-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7fa82-224">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="7fa82-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7fa82-225">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="7fa82-225">Run the app</span></span>

<span data-ttu-id="7fa82-226">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-226">Run the app from the Server project.</span></span> <span data-ttu-id="7fa82-227">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="7fa82-227">When using Visual Studio, either:</span></span>

* <span data-ttu-id="7fa82-228">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7fa82-228">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="7fa82-229">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="7fa82-229">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7fa82-230">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7fa82-230">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7fa82-231">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="7fa82-231">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="7fa82-232">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="7fa82-232">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
