---
title: ASP.NET Core hesap onaylama ve parola kurtarma
author: rick-anderson
description: E-posta onayı ve parola sıfırlama ile ASP.NET Core bir uygulama oluşturmayı öğrenin.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 8a515990be584aa1233fc3bf77811ae3784d9b1c
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081556"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="27aeb-103">ASP.NET Core hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="27aeb-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="27aeb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="27aeb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="27aeb-105">Bu öğreticide, e-posta onayı ve parola sıfırlama ile bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="27aeb-106">Bu öğretici bir başlangıç konusu **değildir** .</span><span class="sxs-lookup"><span data-stu-id="27aeb-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="27aeb-107">Sahibi olmalısınız:</span><span class="sxs-lookup"><span data-stu-id="27aeb-107">You should be familiar with:</span></span>

* [<span data-ttu-id="27aeb-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="27aeb-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="27aeb-109">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="27aeb-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="27aeb-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="27aeb-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="27aeb-111">ASP.NET Core 1,1 sürümü için [Bu PDF dosyasına](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) bakın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="27aeb-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="27aeb-112">Prerequisites</span></span>

[<span data-ttu-id="27aeb-113">.NET Core 3,0 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="27aeb-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="27aeb-114">Kimlik doğrulamasıyla bir Web uygulaması oluşturma ve test etme</span><span class="sxs-lookup"><span data-stu-id="27aeb-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="27aeb-115">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="27aeb-116">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="27aeb-117">Kaydolduktan sonra, e-posta onayı benzetimi `/Identity/Account/RegisterConfirmation` için bir bağlantı içeren to sayfasına yönlendirilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="27aeb-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="27aeb-118">`Click here to confirm your account` Bağlantıyı seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="27aeb-119">**Oturum açma** bağlantısını seçin ve aynı kimlik bilgileriyle oturum açın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="27aeb-120">`Hello YourEmail@provider.com!` Sizi`/Identity/Account/Manage/PersonalData` sayfaya yönlendiren bağlantıyı seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="27aeb-121">Sol taraftaki **kişisel veri** sekmesini seçin ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="27aeb-122">E-posta sağlayıcısı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="27aeb-122">Configure an email provider</span></span>

<span data-ttu-id="27aeb-123">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="27aeb-124">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="27aeb-125">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-125">You can use other email providers.</span></span> <span data-ttu-id="27aeb-126">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="27aeb-127">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="27aeb-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="27aeb-128">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="27aeb-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="27aeb-129">Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="27aeb-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="27aeb-130">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="27aeb-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="27aeb-131">Ve öğesini [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın. `SendGridKey` `SendGridUser`</span><span class="sxs-lookup"><span data-stu-id="27aeb-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="27aeb-132">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="27aeb-133">Windows 'da, gizli dizi Anahtarlar/değer çiftlerini `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` dizindeki bir *gizlilikler. JSON* dosyasında depolar.</span><span class="sxs-lookup"><span data-stu-id="27aeb-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="27aeb-134">*Gizli dizileri. JSON* dosyasının içeriği şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="27aeb-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="27aeb-135">Aşağıdaki biçimlendirme, *gizlilikler. JSON* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="27aeb-136">`SendGridKey` Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="27aeb-137">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="27aeb-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="27aeb-138">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="27aeb-138">Install SendGrid</span></span>

<span data-ttu-id="27aeb-139">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="27aeb-140">`SendGrid` NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="27aeb-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27aeb-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27aeb-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="27aeb-142">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="27aeb-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="27aeb-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="27aeb-144">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="27aeb-145">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="27aeb-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="27aeb-146">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="27aeb-146">Implement IEmailSender</span></span>

<span data-ttu-id="27aeb-147">Uygulamak `IEmailSender`için, aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="27aeb-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="27aeb-148">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="27aeb-148">Configure startup to support email</span></span>

<span data-ttu-id="27aeb-149">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="27aeb-150">Geçici `EmailSender` hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="27aeb-151">`AuthMessageSenderOptions` Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="27aeb-152">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="27aeb-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="27aeb-153">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="27aeb-154">Uygulamayı çalıştırın ve yeni bir kullanıcı kaydı</span><span class="sxs-lookup"><span data-stu-id="27aeb-154">Run the app and register a new user</span></span>
* <span data-ttu-id="27aeb-155">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="27aeb-156">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="27aeb-157">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="27aeb-158">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="27aeb-159">Oturumu kapatın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="27aeb-160">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="27aeb-160">Test password reset</span></span>

* <span data-ttu-id="27aeb-161">Oturum açtıysanız **Oturumu Kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="27aeb-162">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="27aeb-163">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="27aeb-164">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="27aeb-165">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="27aeb-166">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="27aeb-167">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="27aeb-167">Change email and activity timeout</span></span>

<span data-ttu-id="27aeb-168">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="27aeb-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="27aeb-169">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="27aeb-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="27aeb-170">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="27aeb-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="27aeb-171">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="27aeb-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="27aeb-172">Yerleşik kimlik Kullanıcı belirteçleri (bkz [. Aspnetcore/src/Identity/Extensions. Core/src/TokenOptions. cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="27aeb-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="27aeb-173">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="27aeb-173">Change the email token lifespan</span></span>

<span data-ttu-id="27aeb-174">[Kimlik Kullanıcı belirteçlerinin](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="27aeb-174">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="27aeb-175">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="27aeb-176">Özel bir [veri korunabilir\<>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>ekleyin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="27aeb-177">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="27aeb-178">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="27aeb-178">Resend email confirmation</span></span>

<span data-ttu-id="27aeb-179">[Bu GitHub sorununa](https://github.com/aspnet/AspNetCore/issues/5410)bakın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-179">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="27aeb-180">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="27aeb-180">Debug email</span></span>

<span data-ttu-id="27aeb-181">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="27aeb-181">If you can't get email working:</span></span>

* <span data-ttu-id="27aeb-182">`EmailSender.Execute` Doğrulamak`SendGridClient.SendEmailAsync` için içinde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="27aeb-183">Benzer kod `EmailSender.Execute`kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="27aeb-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="27aeb-184">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="27aeb-185">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-185">Check your spam folder.</span></span>
* <span data-ttu-id="27aeb-186">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="27aeb-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="27aeb-187">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="27aeb-188">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="27aeb-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="27aeb-189">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="27aeb-190">Yapılandırma sistemi ortam değişkenlerinden anahtarları okumak için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="27aeb-191">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="27aeb-191">Combine social and local login accounts</span></span>

<span data-ttu-id="27aeb-192">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="27aeb-193">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="27aeb-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="27aeb-194">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="27aeb-195">Aşağıdaki dizide, "RickAndMSFT@gmail.com" ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="27aeb-197">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-197">Click on the **Manage** link.</span></span> <span data-ttu-id="27aeb-198">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-198">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="27aeb-200">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="27aeb-201">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="27aeb-201">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="27aeb-203">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="27aeb-203">The two accounts have been combined.</span></span> <span data-ttu-id="27aeb-204">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-204">You are able to sign in with either account.</span></span> <span data-ttu-id="27aeb-205">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="27aeb-206">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="27aeb-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="27aeb-207">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="27aeb-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="27aeb-208">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="27aeb-209">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="27aeb-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="27aeb-210">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="27aeb-211">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-211">Confirm existing users.</span></span> <span data-ttu-id="27aeb-212">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="27aeb-213">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="27aeb-213">Prerequisites</span></span>

[<span data-ttu-id="27aeb-214">.NET Core 2,2 SDK veya üzeri</span><span class="sxs-lookup"><span data-stu-id="27aeb-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="27aeb-215">Web uygulaması ve yapı iskelesi kimliği oluşturma</span><span class="sxs-lookup"><span data-stu-id="27aeb-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="27aeb-216">Kimlik doğrulamasıyla bir Web uygulaması oluşturmak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-216">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="27aeb-217">Yeni Kullanıcı kaydını sına</span><span class="sxs-lookup"><span data-stu-id="27aeb-217">Test new user registration</span></span>

<span data-ttu-id="27aeb-218">Uygulamayı çalıştırın, **Kaydet** bağlantısını seçin ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="27aeb-219">Bu noktada, e-postadaki tek doğrulama [[Emapostaadresi]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) özniteliğiyle aynıdır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-219">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="27aeb-220">Kayıt gönderildikten sonra uygulamada oturum açarsınız.</span><span class="sxs-lookup"><span data-stu-id="27aeb-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="27aeb-221">Daha sonra öğreticide, yeni kullanıcıların e-postaları doğrulanmadan oturum açması için kod güncellenir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="27aeb-222">Tablonun `EmailConfirmed` alanının olduğunu `False`aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="27aeb-223">Uygulama bir onay e-postası gönderdiğinde bir sonraki adımda bu e-postayı yeniden kullanmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="27aeb-224">Satıra sağ tıklayın ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="27aeb-225">E-posta diğer adını silmek aşağıdaki adımlarda daha kolay hale gelir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="27aeb-226">E-posta onayı gerektir</span><span class="sxs-lookup"><span data-stu-id="27aeb-226">Require email confirmation</span></span>

<span data-ttu-id="27aeb-227">Yeni bir Kullanıcı kaydının e-postasını onaylamak en iyi uygulamadır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="27aeb-228">E-posta onayı, başkalarının taklit etmeyeceğinden emin olmaya yardımcı olur (diğer bir deyişle, başka birinin e-postasına kaydolmamaları).</span><span class="sxs-lookup"><span data-stu-id="27aeb-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="27aeb-229">Bir tartışma forumuna sahip olduğunuzu ve "" öğesini "yli@example.comnolivetto@contoso.com" olarak kaydolmasını engellemek istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="27aeb-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="27aeb-230">E-posta onayı olmadannolivetto@contoso.com, "" uygulamanızdan istenmeyen e-posta alabilir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="27aeb-231">Kullanıcının yanlışlıkla "ylo@example.com" olarak kaydolduğunu ve "yıllı" hatası olduğunu fark edelim.</span><span class="sxs-lookup"><span data-stu-id="27aeb-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="27aeb-232">Uygulamanın doğru e-postası olmadığından parola kurtarma kullanamayacak.</span><span class="sxs-lookup"><span data-stu-id="27aeb-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="27aeb-233">E-posta onayı, robotlardan sınırlı koruma sağlar.</span><span class="sxs-lookup"><span data-stu-id="27aeb-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="27aeb-234">E-posta onayı çok sayıda e-posta hesabına sahip kötü amaçlı kullanıcılardan koruma sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="27aeb-235">Genellikle yeni kullanıcıların, onaylanan bir e-posta almadan önce Web sitenize veri almasını engellemek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="27aeb-236">Onaylanan `Startup.ConfigureServices` bir e-posta gerektirecek şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="27aeb-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="27aeb-237">`config.SignIn.RequireConfirmedEmail = true;`kayıtlı kullanıcıların e-postaları onaylanana kadar oturum açmasını önler.</span><span class="sxs-lookup"><span data-stu-id="27aeb-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="27aeb-238">E-posta sağlayıcısını Yapılandır</span><span class="sxs-lookup"><span data-stu-id="27aeb-238">Configure email provider</span></span>

<span data-ttu-id="27aeb-239">Bu öğreticide, [SendGrid](https://sendgrid.com) e-posta göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="27aeb-240">E-posta göndermek için bir SendGrid hesabına ve anahtarına ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="27aeb-241">Diğer e-posta sağlayıcılarını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-241">You can use other email providers.</span></span> <span data-ttu-id="27aeb-242">ASP.NET Core 2. x içerir `System.Net.Mail`. Bu, uygulamanızdan e-posta göndermenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="27aeb-243">E-posta göndermek için SendGrid veya başka bir e-posta hizmeti kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="27aeb-244">Güvenli hale getirmek ve düzgün şekilde ayarlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="27aeb-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="27aeb-245">Güvenli e-posta anahtarını getirmek için bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="27aeb-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="27aeb-246">Bu örnek için *Hizmetler/Authiletienderoptions. cs*oluşturun:</span><span class="sxs-lookup"><span data-stu-id="27aeb-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="27aeb-247">SendGrid Kullanıcı gizli dizilerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="27aeb-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="27aeb-248">Ve öğesini [gizli-Manager aracı](xref:security/app-secrets)ile ayarlayın. `SendGridKey` `SendGridUser`</span><span class="sxs-lookup"><span data-stu-id="27aeb-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="27aeb-249">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="27aeb-250">Windows 'da, gizli dizi Anahtarlar/değer çiftlerini `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` dizindeki bir *gizlilikler. JSON* dosyasında depolar.</span><span class="sxs-lookup"><span data-stu-id="27aeb-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="27aeb-251">*Gizli dizileri. JSON* dosyasının içeriği şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="27aeb-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="27aeb-252">Aşağıdaki biçimlendirme, *gizlilikler. JSON* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="27aeb-253">`SendGridKey` Değer kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="27aeb-254">Daha fazla bilgi için bkz. [Options model](xref:fundamentals/configuration/options) ve [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="27aeb-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="27aeb-255">SendGrid 'i yükler</span><span class="sxs-lookup"><span data-stu-id="27aeb-255">Install SendGrid</span></span>

<span data-ttu-id="27aeb-256">Bu öğretici, [SendGrid](https://sendgrid.com/)aracılığıyla e-posta bildirimlerinin nasıl ekleneceğini gösterir, ancak SMTP ve diğer mekanizmaları kullanarak e-posta gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="27aeb-257">`SendGrid` NuGet paketini yükler:</span><span class="sxs-lookup"><span data-stu-id="27aeb-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27aeb-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27aeb-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="27aeb-259">Paket Yöneticisi konsolundan, aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="27aeb-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="27aeb-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="27aeb-261">Konsolundan aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="27aeb-262">Ücretsiz SendGrid hesabına kaydolmak için bkz. [SendGrid Ile çalışmaya başlayın](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="27aeb-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="27aeb-263">Iemailsender uygulama</span><span class="sxs-lookup"><span data-stu-id="27aeb-263">Implement IEmailSender</span></span>

<span data-ttu-id="27aeb-264">Uygulamak `IEmailSender`için, aşağıdakine benzer bir kodla *Hizmetler/emailsender. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="27aeb-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="27aeb-265">Başlat 'ı e-postayı destekleyecek şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="27aeb-265">Configure startup to support email</span></span>

<span data-ttu-id="27aeb-266">Aşağıdaki kodu `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="27aeb-267">Geçici `EmailSender` hizmet olarak ekleyin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="27aeb-268">`AuthMessageSenderOptions` Yapılandırma örneğini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="27aeb-269">Hesap onaylama ve parola kurtarmayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="27aeb-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="27aeb-270">Şablonda hesap onaylama ve parola kurtarma için kod bulunur.</span><span class="sxs-lookup"><span data-stu-id="27aeb-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="27aeb-271">Yöntemi/ *kimlik/sayfa/hesap/Register. cshtml. cs*içindeki yöntemi bulun. `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="27aeb-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="27aeb-272">Yeni kayıtlı kullanıcıların aşağıdaki satırı açıklama ekleyerek otomatik olarak oturum açmasını engelleyin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="27aeb-273">Tüm Yöntem vurgulanmış olan değiştirilen çizgi ile gösterilir:</span><span class="sxs-lookup"><span data-stu-id="27aeb-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="27aeb-274">Kaydolun, e-postayı onaylayın ve parolayı sıfırlayın</span><span class="sxs-lookup"><span data-stu-id="27aeb-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="27aeb-275">Web uygulamasını çalıştırın ve hesap onaylama ve parola kurtarma akışını test edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="27aeb-276">Uygulamayı çalıştırın ve yeni bir kullanıcı kaydı</span><span class="sxs-lookup"><span data-stu-id="27aeb-276">Run the app and register a new user</span></span>
* <span data-ttu-id="27aeb-277">Hesap onay bağlantısı için e-postanızı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="27aeb-278">E-postayı alamazsanız [hata ayıklama e-postasına](#debug) bakın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="27aeb-279">E-postanızı onaylamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="27aeb-280">E-postanız ve parolanızla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="27aeb-281">Oturumu kapatın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="27aeb-282">Yönet sayfasını görüntüle</span><span class="sxs-lookup"><span data-stu-id="27aeb-282">View the manage page</span></span>

<span data-ttu-id="27aeb-283">Kullanıcı adınızı tarayıcıda seçin: ![tarayıcı penceresinde Kullanıcı adı](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="27aeb-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="27aeb-284">Yönet sayfası, **profil** sekmesi seçili olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="27aeb-285">**E** -postada, e-postanın onaylandığını belirten bir onay kutusu gösterilir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="27aeb-286">Sınama parolası sıfırlama</span><span class="sxs-lookup"><span data-stu-id="27aeb-286">Test password reset</span></span>

* <span data-ttu-id="27aeb-287">Oturum açtıysanız **Oturumu Kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="27aeb-288">**Oturum aç** bağlantısını seçin ve **parolanızı unuttum?** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="27aeb-289">Hesabı kaydettirmek için kullandığınız e-postayı girin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="27aeb-290">Parolanızı sıfırlamaya yönelik bağlantı içeren bir e-posta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="27aeb-291">E-postanızı kontrol edin ve parolanızı sıfırlamak için bağlantıya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="27aeb-292">Parolanız başarıyla sıfırlandıktan sonra, e-posta ve yeni parolanızla oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="27aeb-293">E-posta ve etkinlik zaman aşımını değiştirme</span><span class="sxs-lookup"><span data-stu-id="27aeb-293">Change email and activity timeout</span></span>

<span data-ttu-id="27aeb-294">Varsayılan eylemsizlik zaman aşımı 14 gündür.</span><span class="sxs-lookup"><span data-stu-id="27aeb-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="27aeb-295">Aşağıdaki kod, etkinlik dışı zaman aşımını 5 güne ayarlar:</span><span class="sxs-lookup"><span data-stu-id="27aeb-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="27aeb-296">Tüm veri koruma belirteci kullanım alanlarını Değiştir</span><span class="sxs-lookup"><span data-stu-id="27aeb-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="27aeb-297">Aşağıdaki kod tüm veri koruma belirteçleri zaman aşımı süresini 3 saate değiştirir:</span><span class="sxs-lookup"><span data-stu-id="27aeb-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="27aeb-298">Yerleşik kimlik Kullanıcı belirteçleri (bkz [. Aspnetcore/src/Identity/Extensions. Core/src/TokenOptions. cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) bir [gün zaman aşımı](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="27aeb-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="27aeb-299">E-posta belirtecini değiştir kullanım ömrü</span><span class="sxs-lookup"><span data-stu-id="27aeb-299">Change the email token lifespan</span></span>

<span data-ttu-id="27aeb-300">[Kimlik Kullanıcı belirteçlerinin](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) varsayılan belirteç kullanım ömrü [bir gündür](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="27aeb-300">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="27aeb-301">Bu bölümde, eespan e-posta belirtecinin nasıl değiştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="27aeb-302">Özel bir [veri korunabilir\<>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) ve <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>ekleyin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="27aeb-303">Özel sağlayıcıyı hizmet kapsayıcısına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="27aeb-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="27aeb-304">E-posta onayını yeniden gönder</span><span class="sxs-lookup"><span data-stu-id="27aeb-304">Resend email confirmation</span></span>

<span data-ttu-id="27aeb-305">[Bu GitHub sorununa](https://github.com/aspnet/AspNetCore/issues/5410)bakın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="27aeb-306">Hata ayıklama e-postası</span><span class="sxs-lookup"><span data-stu-id="27aeb-306">Debug email</span></span>

<span data-ttu-id="27aeb-307">E-posta ile çalışmayı alamazsanız:</span><span class="sxs-lookup"><span data-stu-id="27aeb-307">If you can't get email working:</span></span>

* <span data-ttu-id="27aeb-308">`EmailSender.Execute` Doğrulamak`SendGridClient.SendEmailAsync` için içinde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="27aeb-309">Benzer kod `EmailSender.Execute`kullanarak [e-posta göndermek için bir konsol uygulaması](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="27aeb-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="27aeb-310">[E-posta etkinlik](https://sendgrid.com/docs/User_Guide/email_activity.html) sayfasını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="27aeb-311">İstenmeyen posta klasörünüzü kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-311">Check your spam folder.</span></span>
* <span data-ttu-id="27aeb-312">Farklı bir e-posta sağlayıcısında (Microsoft, Yahoo, Gmail vb.) başka bir e-posta diğer adı deneyin</span><span class="sxs-lookup"><span data-stu-id="27aeb-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="27aeb-313">Farklı e-posta hesaplarına göndermeyi deneyin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="27aeb-314">**En iyi güvenlik uygulaması** , test ve geliştirmede üretim sırlarını **kullanmamalıdır** .</span><span class="sxs-lookup"><span data-stu-id="27aeb-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="27aeb-315">Uygulamayı Azure 'a yayımlarsanız, SendGrid gizli dizilerini Azure Web App Portal 'da uygulama ayarları olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="27aeb-316">Yapılandırma sistemi ortam değişkenlerinden anahtarları okumak için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="27aeb-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="27aeb-317">Sosyal ve yerel oturum açma hesaplarını birleştirme</span><span class="sxs-lookup"><span data-stu-id="27aeb-317">Combine social and local login accounts</span></span>

<span data-ttu-id="27aeb-318">Bu bölümü gerçekleştirmek için önce bir dış kimlik doğrulama sağlayıcısı etkinleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="27aeb-319">Bkz. [Facebook, Google ve dış sağlayıcı kimlik doğrulaması](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="27aeb-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="27aeb-320">E-posta bağlantısına tıklayarak Yerel ve sosyal hesapları birleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="27aeb-321">Aşağıdaki dizide, "RickAndMSFT@gmail.com" ilk olarak yerel bir oturum açma olarak oluşturulur; ancak, önce hesabı önce sosyal oturum açma olarak oluşturabilir, ardından yerel bir oturum açma ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web uygulaması: RickAndMSFT@gmail.com kullanıcının kimliği doğrulandı](accconfirm/_static/rick.png)

<span data-ttu-id="27aeb-323">**Yönet** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-323">Click on the **Manage** link.</span></span> <span data-ttu-id="27aeb-324">Bu hesapla ilişkili 0 harici (sosyal oturumlar) ' a göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-324">Note the 0 external (social logins) associated with this account.</span></span>

![Görünümü Yönet](accconfirm/_static/manage.png)

<span data-ttu-id="27aeb-326">Başka bir oturum açma hizmeti bağlantısına tıklayın ve uygulama isteklerini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="27aeb-327">Aşağıdaki görüntüde Facebook dış kimlik doğrulama sağlayıcısıdır:</span><span class="sxs-lookup"><span data-stu-id="27aeb-327">In the following image, Facebook is the external authentication provider:</span></span>

![Dış oturum açma görünümlerinizi yönetin Facebook listeleme](accconfirm/_static/fb.png)

<span data-ttu-id="27aeb-329">İki hesap birleştirildi.</span><span class="sxs-lookup"><span data-stu-id="27aeb-329">The two accounts have been combined.</span></span> <span data-ttu-id="27aeb-330">Her iki hesapla oturum açabiliyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-330">You are able to sign in with either account.</span></span> <span data-ttu-id="27aeb-331">Kullanıcılarınızın sosyal oturum açma kimlik doğrulama hizmeti kapatılmış veya sosyal hesaplarına erişimi kaybettikleri olasılığına karşı yerel hesaplar eklemesini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="27aeb-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="27aeb-332">Bir sitede kullanıcılar varsa hesap onayını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="27aeb-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="27aeb-333">Kullanıcılara bir sitede hesap onayını etkinleştirmek, mevcut tüm kullanıcıları kilitler.</span><span class="sxs-lookup"><span data-stu-id="27aeb-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="27aeb-334">Mevcut kullanıcılar, hesapları onaylanmadığı için kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="27aeb-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="27aeb-335">Mevcut Kullanıcı kilitlemesini geçici olarak çözmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="27aeb-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="27aeb-336">Tüm mevcut kullanıcıları onaylanmış olarak işaretlemek için veritabanını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="27aeb-337">Mevcut kullanıcıları onaylayın.</span><span class="sxs-lookup"><span data-stu-id="27aeb-337">Confirm existing users.</span></span> <span data-ttu-id="27aeb-338">Örneğin, Batch-e-postaları onay bağlantılarıyla gönderin.</span><span class="sxs-lookup"><span data-stu-id="27aeb-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
