---
title: ASP.NET Core projelerinde iskele kimlik
author: rick-anderson
description: Bir ASP.NET Core projesi içinde kimlik iskele oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 5/16/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 07163941d0bd1fea6f9b3d9867536580d8a9e9d8
ms.sourcegitcommit: e12f45ddcbe99102a74d4077df27d6c0ebba49c1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2018
ms.locfileid: "39063279"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="71af0-103">ASP.NET Core projelerinde iskele kimlik</span><span class="sxs-lookup"><span data-stu-id="71af0-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="71af0-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71af0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71af0-105">ASP.NET Core 2.1 ve üzeri sağlar [ASP.NET Core kimliği](xref:security/authentication/identity) olarak bir [Razor sınıf kitaplığı](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="71af0-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="71af0-106">Kimlik içeren uygulamaları seçmeli olarak kimlik Razor sınıf kitaplığı (RCL) yer alan kaynak kodu eklemek için iskele kurucu uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="71af0-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="71af0-107">Kodu değiştirin ve davranışını değiştirmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="71af0-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="71af0-108">Örneğin, kayıt için kullanılan kod üretmek için iskele kurucu toplamasını.</span><span class="sxs-lookup"><span data-stu-id="71af0-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="71af0-109">Oluşturulan kod aynı kimlik RCL kodda daha önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="71af0-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="71af0-110">Kullanıcı arabirimi tam denetimini ve varsayılan RCL kullanmamak için bölümüne bakın. [tam kimlik UI Kaynağı Oluştur](#full).</span><span class="sxs-lookup"><span data-stu-id="71af0-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="71af0-111">Yapan uygulamalar **değil** dahil kimlik doğrulaması RCL kimlik paketini eklemek için iskele kurucu uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="71af0-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="71af0-112">Oluşturulacak kimlik kodu seçme seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="71af0-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="71af0-113">Çoğu gerekli kodu iskele kurucu oluşturur ancak işlemi tamamlamak için projeyi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="71af0-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="71af0-114">Bu belge, bir kimlik yapı iskelesi güncelleştirmeyi tamamlamak için gereken adımları açıklar.</span><span class="sxs-lookup"><span data-stu-id="71af0-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="71af0-115">Kimlik iskele kurucu çalıştırdığınızda bir *ScaffoldingReadme.txt* dosyası proje dizininde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="71af0-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="71af0-116">*ScaffoldingReadme.txt* dosyası kimlik yapı iskelesi güncelleştirmeyi tamamlamak gerekli genel yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="71af0-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="71af0-117">Bu belgede daha daha eksiksiz yönergeler içeren *ScaffoldingReadme.txt* dosya.</span><span class="sxs-lookup"><span data-stu-id="71af0-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="71af0-118">Dosya farklılıklarını gösterir ve dışında değişiklikleri geri sağlar bir kaynak denetim sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="71af0-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="71af0-119">Kimlik iskele kurucu çalıştırdıktan sonra değişiklikleri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="71af0-119">Inspect the changes after running the Identity scaffolder.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="71af0-120">Boş bir projeye iskele kimlik</span><span class="sxs-lookup"><span data-stu-id="71af0-120">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="71af0-121">Aşağıdaki vurgulanmış çağrıları ekleyin `Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="71af0-121">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="71af0-122">Mevcut bir yetkilendirme olmadan bir Razor projeye iskele kimlik</span><span class="sxs-lookup"><span data-stu-id="71af0-122">Scaffold identity into a Razor project without existing authorization</span></span>

<!--
set projNam=RPnoAuth
set projType=razor
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

<span data-ttu-id="71af0-123">Kimlik yapılandırılmıştır *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="71af0-123">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71af0-124">Daha fazla bilgi için [Ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="71af0-124">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="71af0-125">Geçişler, UseAuthentication ve düzeni</span><span class="sxs-lookup"><span data-stu-id="71af0-125">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="71af0-126">Kimlik doğrulamasını etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="71af0-126">Enable authentication</span></span>

<span data-ttu-id="71af0-127">İçinde `Configure` yöntemi `Startup` sınıfı, çağrı [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) sonra `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="71af0-127">In the `Configure` method of the `Startup` class, call [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="71af0-128">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="71af0-128">Layout changes</span></span>

<span data-ttu-id="71af0-129">İsteğe bağlı: Kısmi oturum açma ekleme (`_LoginPartial`) için Düzen dosyası:</span><span class="sxs-lookup"><span data-stu-id="71af0-129">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="71af0-130">Razor projesine yetkilendirmesiyle iskele kimlik</span><span class="sxs-lookup"><span data-stu-id="71af0-130">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register

[!INCLUDE[](~/includes/webapp-alias-notice.md)]
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]<span data-ttu-id="71af0-131"> Bazı kimlik seçeneklerini yapılandırılan *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="71af0-131"> Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71af0-132">Daha fazla bilgi için [Ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="71af0-132">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="71af0-133">Var olan bir yetkilendirme olmadan bir MVC projeye iskele kimlik</span><span class="sxs-lookup"><span data-stu-id="71af0-133">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="71af0-134">İsteğe bağlı: Kısmi oturum açma ekleme (`_LoginPartial`) için *Views/Shared/_Layout.cshtml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="71af0-134">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="71af0-135">Taşıma *Pages/Shared/_LoginPartial.cshtml* dosyasını *Views/Shared/_LoginPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="71af0-135">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="71af0-136">Kimlik yapılandırılmıştır *Areas/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="71af0-136">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="71af0-137">Ihostingstartup daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="71af0-137">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="71af0-138">Çağrı [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) sonra `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="71af0-138">Call [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="71af0-139">Bir yetkilendirme ile MVC projeye iskele kimlik</span><span class="sxs-lookup"><span data-stu-id="71af0-139">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="71af0-140">Silme *sayfaları/paylaşılan* klasörünü ve klasördeki dosyaları.</span><span class="sxs-lookup"><span data-stu-id="71af0-140">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="71af0-141">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="71af0-141">Create full identity UI source</span></span>

<span data-ttu-id="71af0-142">Kimlik UI tam denetimi korumak için kimlik iskele kurucu çalıştırın ve seçin **tüm dosyaları geçersiz kılma**.</span><span class="sxs-lookup"><span data-stu-id="71af0-142">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="71af0-143">Aşağıdaki vurgulanmış kodu varsayılan kimlik UI kimliği ile bir ASP.NET Core 2.1 web uygulamasında değiştirin. değişiklik gösterir.</span><span class="sxs-lookup"><span data-stu-id="71af0-143">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="71af0-144">Kimlik UI tam denetim sahibi olmak için bunu yapmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="71af0-144">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="71af0-145">Varsayılan kimlik, aşağıdaki kodda değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="71af0-145">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="71af0-146">Aşağıdaki kod kümeleri [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="71af0-146">The following the code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="71af0-147">Kayıt bir `IEmailSender` uygulaması örneği için:</span><span class="sxs-lookup"><span data-stu-id="71af0-147">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]
