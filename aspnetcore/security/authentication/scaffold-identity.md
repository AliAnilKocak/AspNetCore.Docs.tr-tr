---
title: ASP.NET Core projelerinde yapı iskelesi kimliği
author: rick-anderson
description: ASP.NET Core projesindeki kimliği nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ca2046563281efc3c1cd8f4fec73fe4f8d3fbdda
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816115"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="cb3a7-103">ASP.NET Core projelerinde yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="cb3a7-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="cb3a7-104">[Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="cb3a7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb3a7-105">ASP.NET Core 2,1 ve üzeri, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cb3a7-106">Kimlik içeren uygulamalar, Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği 'ı uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="cb3a7-107">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="cb3a7-108">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="cb3a7-109">Oluşturulan kod, RCL kimliği içindeki aynı koddan önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="cb3a7-110">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="cb3a7-111">Kimlik **doğrulaması içermeyen uygulamalar** , RCL kimlik paketini eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="cb3a7-112">Oluşturulacak kimlik kodunu seçme seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="cb3a7-113">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="cb3a7-114">Bu belgede, bir kimlik yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="cb3a7-115">Identity desteği çalıştırıldığında, proje dizininde bir *scaffoldingreadme. txt* dosyası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="cb3a7-116">*Scaffoldingreadme. txt* dosyası, kimlik yapı iskelesi güncelleştirmesinin tamamlanabilmesi için gerekli olan genel yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="cb3a7-117">Bu belge, *Scaffoldingreadme. txt* dosyasından daha eksiksiz yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="cb3a7-118">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="cb3a7-119">Kimlik scaffolder 'ı çalıştırdıktan sonra değişiklikleri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="cb3a7-120">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ve kimlik ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="cb3a7-121">Hizmet veya hizmet saplamaları, kimliğe iskele oluştururken oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="cb3a7-122">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="cb3a7-123">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="cb3a7-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="cb3a7-124">Boş bir projede yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="cb3a7-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cb3a7-125">Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="cb3a7-126">Mevcut yetkilendirme olmadan bir Razor projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="cb3a7-126">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cb3a7-127">Kimlik, */kimlik/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb3a7-128">daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb3a7-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="cb3a7-129">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="cb3a7-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="cb3a7-130">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="cb3a7-130">Enable authentication</span></span>

<span data-ttu-id="cb3a7-131">`Startup` sınıfının `Configure` yönteminde `UseStaticFiles`sonra [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ' ı çağırın:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="cb3a7-132">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="cb3a7-132">Layout changes</span></span>

<span data-ttu-id="cb3a7-133">İsteğe bağlı: (`_LoginPartial`) oturum açma bölümünü düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="cb3a7-134">Yetkilendirmeyi içeren bir Razor projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="cb3a7-134">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="cb3a7-135">Bazı kimlik seçenekleri */Identity/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb3a7-136">daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb3a7-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="cb3a7-137">Var olan yetkilendirme olmadan bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="cb3a7-137">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cb3a7-138">İsteğe bağlı: *görünümleri/paylaşılan/_Layout. cshtml* dosyasına (`_LoginPartial`) oturum açma kısmını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="cb3a7-139">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_loginpartial. cshtml* dosyasına taşıyın</span><span class="sxs-lookup"><span data-stu-id="cb3a7-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="cb3a7-140">Kimlik, */kimlik/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb3a7-141">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="cb3a7-142">`UseStaticFiles`sonra [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 'ı çağır:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="cb3a7-143">Yetkilendirmeyi içeren bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="cb3a7-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="cb3a7-144">*Sayfaları/paylaşılan* klasörünü ve bu klasördeki dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="cb3a7-145">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="cb3a7-145">Create full identity UI source</span></span>

<span data-ttu-id="cb3a7-146">Kimlik Kullanıcı arabirimine tam denetim sağlamak için, Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="cb3a7-147">Aşağıdaki Vurgulanan kodda, varsayılan kimlik Kullanıcı arabirimini ASP.NET Core 2,1 Web uygulamasındaki kimlikle değiştirme değişiklikleri gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="cb3a7-148">Bunu, kimlik Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="cb3a7-149">Varsayılan kimlik aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="cb3a7-150">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="cb3a7-151">Bir `IEmailSender` uygulamasını kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="cb3a7-152">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="cb3a7-152">Disable register page</span></span>

<span data-ttu-id="cb3a7-153">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-153">To disable user registration:</span></span>

* <span data-ttu-id="cb3a7-154">Yapı iskelesi kimliği.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-154">Scaffold Identity.</span></span> <span data-ttu-id="cb3a7-155">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-155">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="cb3a7-156">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-156">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="cb3a7-157">Kullanıcıları bu uç noktadan kaydedememesi için *alan/kimlik/sayfa/hesap/kayıt. cshtml. cs* 'yi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-157">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="cb3a7-158">Önceki değişikliklerle tutarlı olması için *alan/kimlik/sayfa/hesap/kayıt. cshtml* 'yi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-158">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="cb3a7-159">*Alan/kimlik/sayfa/hesap/Login. cshtml* 'den kayıt bağlantısını açıklama veya kaldırma</span><span class="sxs-lookup"><span data-stu-id="cb3a7-159">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="cb3a7-160">*Bölgeler/kimlik/sayfalar/hesap/RegisterConfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-160">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="cb3a7-161">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-161">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="cb3a7-162">`PageModel`onay kodunu kaldırın:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-162">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="cb3a7-163">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="cb3a7-163">Use another app to add users</span></span>

<span data-ttu-id="cb3a7-164">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-164">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="cb3a7-165">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-165">Options to add users include:</span></span>

* <span data-ttu-id="cb3a7-166">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-166">A dedicated admin web app.</span></span>
* <span data-ttu-id="cb3a7-167">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-167">A console app.</span></span>

<span data-ttu-id="cb3a7-168">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-168">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="cb3a7-169">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-169">A list of users is read into memory.</span></span>
* <span data-ttu-id="cb3a7-170">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-170">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="cb3a7-171">Kullanıcı, kimlik veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-171">The user is added to the Identity database.</span></span>
* <span data-ttu-id="cb3a7-172">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-172">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="cb3a7-173">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="cb3a7-173">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="cb3a7-174">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="cb3a7-174">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb3a7-175">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cb3a7-175">Additional resources</span></span>

* [<span data-ttu-id="cb3a7-176">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="cb3a7-176">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
