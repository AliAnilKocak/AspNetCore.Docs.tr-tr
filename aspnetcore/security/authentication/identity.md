---
title: ASP.NET Core kimliğe giriş
author: rick-anderson
description: ASP.NET Core bir uygulamayla kimlik kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 2e0723d34a09109a034f3375c4e94aedab2a5427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662346"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="85b1f-104">ASP.NET Core kimliğe giriş</span><span class="sxs-lookup"><span data-stu-id="85b1f-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85b1f-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="85b1f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="85b1f-106">ASP.NET Core kimliği:</span><span class="sxs-lookup"><span data-stu-id="85b1f-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="85b1f-107">, Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="85b1f-108">Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="85b1f-109">Kullanıcılar, kimlik içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="85b1f-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="85b1f-110">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="85b1f-111">[Kimlik kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="85b1f-112">Kimlik ile şablon etkileşimini gözden geçirmek için, [kimliği iskele](xref:security/authentication/scaffold-identity) geçirin ve oluşturulan dosyaları görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="85b1f-113">Kimlik genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="85b1f-114">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="85b1f-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="85b1f-115">Bu konu başlığında, bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kimlik kullanmayı öğrenirsiniz.</span><span class="sxs-lookup"><span data-stu-id="85b1f-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="85b1f-116">Kimlik kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için, bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="85b1f-117">[Microsoft Identity platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="85b1f-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="85b1f-118">Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.</span><span class="sxs-lookup"><span data-stu-id="85b1f-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="85b1f-119">ASP.NET Core kimlikle ilgisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="85b1f-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="85b1f-120">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-120">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="85b1f-121">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="85b1f-121">Create a Web app with authentication</span></span>

<span data-ttu-id="85b1f-122">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="85b1f-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="85b1f-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85b1f-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85b1f-124">**Dosya** > **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="85b1f-125">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="85b1f-126">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="85b1f-127">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-127">Click **OK**.</span></span>
* <span data-ttu-id="85b1f-128">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="85b1f-129">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="85b1f-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="85b1f-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="85b1f-131">Yukarıdaki komut, SQLite kullanarak bir Razor Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="85b1f-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="85b1f-132">LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="85b1f-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="85b1f-133">Oluşturulan proje, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="85b1f-134">Identity Razor sınıfı kitaplığı, `Identity` alanı ile uç noktaları kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="85b1f-135">Örnek:</span><span class="sxs-lookup"><span data-stu-id="85b1f-135">For example:</span></span>

* <span data-ttu-id="85b1f-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="85b1f-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="85b1f-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="85b1f-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="85b1f-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="85b1f-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="85b1f-139">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="85b1f-139">Apply migrations</span></span>

<span data-ttu-id="85b1f-140">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="85b1f-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85b1f-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85b1f-142">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="85b1f-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="85b1f-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="85b1f-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="85b1f-144">SQLite kullanılırken geçişler Bu adımda gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="85b1f-145">LocalDB için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="85b1f-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="85b1f-146">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="85b1f-146">Test Register and Login</span></span>

<span data-ttu-id="85b1f-147">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-147">Run the app and register a user.</span></span> <span data-ttu-id="85b1f-148">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="85b1f-149">Kimlik hizmetlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="85b1f-149">Configure Identity services</span></span>

<span data-ttu-id="85b1f-150">Hizmetler `ConfigureServices`eklenir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="85b1f-151">Tipik model, tüm `Add{Service}` yöntemlerini çağırmak ve sonra tüm `services.Configure{Service}` yöntemlerini çağırmalıdır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="85b1f-152">Önceki vurgulanan kod, varsayılan seçenek değerleriyle kimliği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="85b1f-153">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="85b1f-154">Kimlik, <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="85b1f-155">`UseAuthentication`, istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="85b1f-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="85b1f-156">Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="85b1f-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="85b1f-157">`app.UseAuthorization`, uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="85b1f-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`ve `UseEndpoints` önceki kodda gösterilen sırada çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="85b1f-159">`IdentityOptions` ve `Startup`hakkında daha fazla bilgi için bkz. <xref:Microsoft.AspNetCore.Identity.IdentityOptions> ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="85b1f-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="85b1f-160">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="85b1f-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="85b1f-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85b1f-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85b1f-162">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="85b1f-163">Bu bölümde gösterilen kodu oluşturmak için, yetkilendirme yönergeleriyle [birlikte bir Razor projesinde yapı iskelesi kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="85b1f-164">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="85b1f-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="85b1f-165">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="85b1f-166">Aksi takdirde, `ApplicationDbContext`için doğru ad alanını kullanın:</span><span class="sxs-lookup"><span data-stu-id="85b1f-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="85b1f-167">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="85b1f-168">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="85b1f-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="85b1f-169">Yapı iskelesi kimliği hakkında daha fazla bilgi için bkz. kimlik [doğrulama ile bir Razor projesinde yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="85b1f-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="85b1f-170">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="85b1f-170">Examine Register</span></span>

<span data-ttu-id="85b1f-171">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="85b1f-172">Kullanıcı, `_userManager` nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="85b1f-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="85b1f-173">`_userManager` bağımlılık ekleme tarafından sağlanır):</span><span class="sxs-lookup"><span data-stu-id="85b1f-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="85b1f-174">Kullanıcı başarıyla oluşturulduysa, Kullanıcı `_signInManager.SignInAsync`çağrısıyla oturum açar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="85b1f-175">Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="85b1f-176">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="85b1f-176">Log in</span></span>

<span data-ttu-id="85b1f-177">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="85b1f-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="85b1f-178">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="85b1f-179">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="85b1f-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="85b1f-180">Oturum açma sayfasındaki form gönderildiğinde `OnPostAsync` eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="85b1f-181">`PasswordSignInAsync`, `_signInManager` nesnesi üzerinde çağrılır (bağımlılık ekleme tarafından sağlanır).</span><span class="sxs-lookup"><span data-stu-id="85b1f-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="85b1f-182">Temel `Controller` sınıfı, denetleyici yöntemlerinden erişilebilen bir `User` özelliğini kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="85b1f-183">Örneğin, `User.Claims` numaralandırmanızı ve yetkilendirme kararları almanızı sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="85b1f-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="85b1f-184">Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="85b1f-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="85b1f-185">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="85b1f-185">Log out</span></span>

<span data-ttu-id="85b1f-186">**Oturum çıkış** bağlantısı `LogoutModel.OnPost` eylemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="85b1f-187">Önceki kodda, tarayıcının yeni bir istek yapması ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kod `return RedirectToPage();` yeniden yönlendirme olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="85b1f-188">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="85b1f-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="85b1f-189">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="85b1f-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="85b1f-190">Test kimliği</span><span class="sxs-lookup"><span data-stu-id="85b1f-190">Test Identity</span></span>

<span data-ttu-id="85b1f-191">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="85b1f-192">Kimliği test etmek için [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)ekleyin:</span><span class="sxs-lookup"><span data-stu-id="85b1f-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="85b1f-193">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="85b1f-194">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="85b1f-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="85b1f-195">Kimliği keşfet</span><span class="sxs-lookup"><span data-stu-id="85b1f-195">Explore Identity</span></span>

<span data-ttu-id="85b1f-196">Kimliği daha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="85b1f-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="85b1f-197">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="85b1f-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="85b1f-198">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="85b1f-199">Kimlik bileşenleri</span><span class="sxs-lookup"><span data-stu-id="85b1f-199">Identity Components</span></span>

<span data-ttu-id="85b1f-200">Tüm kimlik bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="85b1f-201">Kimliğin birincil paketi [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)' dır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="85b1f-202">Bu paket, ASP.NET Core kimliği için çekirdek arabirim kümesini içerir ve `Microsoft.AspNetCore.Identity.EntityFrameworkCore`tarafından dahildir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="85b1f-203">ASP.NET Core kimliğe geçiriliyor</span><span class="sxs-lookup"><span data-stu-id="85b1f-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="85b1f-204">Mevcut kimlik deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için bkz. [kimlik doğrulama ve kimlik geçişi](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="85b1f-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="85b1f-205">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="85b1f-205">Setting password strength</span></span>

<span data-ttu-id="85b1f-206">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="85b1f-207">Adddefaultıdentity ve AddEntity</span><span class="sxs-lookup"><span data-stu-id="85b1f-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="85b1f-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="85b1f-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="85b1f-209">`AddDefaultIdentity` çağırmak, aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="85b1f-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="85b1f-210">Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="85b1f-211">Statik kimlik varlıklarının yayımlanmasını engelle</span><span class="sxs-lookup"><span data-stu-id="85b1f-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="85b1f-212">Statik kimlik varlıklarının (kimlik Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliğini ve `RemoveIdentityAssets` hedefini uygulamanın proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="85b1f-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="85b1f-213">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="85b1f-213">Next Steps</span></span>

* <span data-ttu-id="85b1f-214">SQLite kullanarak kimlik yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-214">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="85b1f-215">Kimliği Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="85b1f-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85b1f-216">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="85b1f-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="85b1f-217">ASP.NET Core kimlik, ASP.NET Core uygulamalara oturum açma işlevselliği ekleyen bir üyelik sistemidir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="85b1f-218">Kullanıcılar, kimlik içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="85b1f-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="85b1f-219">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="85b1f-220">Kimlik, Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-220">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="85b1f-221">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="85b1f-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="85b1f-222">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-222">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="85b1f-223">Bu konu başlığında, bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kimlik kullanmayı öğrenirsiniz.</span><span class="sxs-lookup"><span data-stu-id="85b1f-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="85b1f-224">Kimlik kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için, bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="85b1f-225">Adddefaultıdentity ve AddEntity</span><span class="sxs-lookup"><span data-stu-id="85b1f-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="85b1f-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="85b1f-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="85b1f-227">`AddDefaultIdentity` çağırmak, aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="85b1f-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="85b1f-228">Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="85b1f-229">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="85b1f-229">Create a Web app with authentication</span></span>

<span data-ttu-id="85b1f-230">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="85b1f-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="85b1f-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85b1f-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85b1f-232">**Dosya** > **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="85b1f-233">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="85b1f-234">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="85b1f-235">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-235">Click **OK**.</span></span>
* <span data-ttu-id="85b1f-236">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="85b1f-237">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="85b1f-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="85b1f-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="85b1f-239">Oluşturulan proje, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="85b1f-240">Identity Razor sınıfı kitaplığı, `Identity` alanı ile uç noktaları kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="85b1f-241">Örnek:</span><span class="sxs-lookup"><span data-stu-id="85b1f-241">For example:</span></span>

* <span data-ttu-id="85b1f-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="85b1f-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="85b1f-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="85b1f-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="85b1f-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="85b1f-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="85b1f-245">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="85b1f-245">Apply migrations</span></span>

<span data-ttu-id="85b1f-246">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="85b1f-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85b1f-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85b1f-248">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="85b1f-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="85b1f-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="85b1f-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="85b1f-250">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="85b1f-250">Test Register and Login</span></span>

<span data-ttu-id="85b1f-251">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-251">Run the app and register a user.</span></span> <span data-ttu-id="85b1f-252">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="85b1f-253">Kimlik hizmetlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="85b1f-253">Configure Identity services</span></span>

<span data-ttu-id="85b1f-254">Hizmetler `ConfigureServices`eklenir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="85b1f-255">Tipik model, tüm `Add{Service}` yöntemlerini çağırmak ve sonra tüm `services.Configure{Service}` yöntemlerini çağırmalıdır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="85b1f-256">Yukarıdaki kod, varsayılan seçenek değerleriyle kimliği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="85b1f-257">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="85b1f-258">Kimlik, [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-258">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="85b1f-259">`UseAuthentication`, istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="85b1f-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="85b1f-260">Daha fazla bilgi için bkz. [ıdentityoptions sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="85b1f-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="85b1f-261">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="85b1f-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="85b1f-262">Bu bölümde gösterilen kodu oluşturmak için, yetkilendirme yönergeleriyle [birlikte bir Razor projesinde yapı iskelesi kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="85b1f-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85b1f-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85b1f-264">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="85b1f-265">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="85b1f-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="85b1f-266">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="85b1f-267">Aksi takdirde, `ApplicationDbContext`için doğru ad alanını kullanın:</span><span class="sxs-lookup"><span data-stu-id="85b1f-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="85b1f-268">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="85b1f-269">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="85b1f-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="85b1f-270">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="85b1f-270">Examine Register</span></span>

<span data-ttu-id="85b1f-271">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="85b1f-272">Kullanıcı, `_userManager` nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="85b1f-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="85b1f-273">`_userManager` bağımlılık ekleme tarafından sağlanır):</span><span class="sxs-lookup"><span data-stu-id="85b1f-273">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="85b1f-274">Kullanıcı başarıyla oluşturulduysa, Kullanıcı `_signInManager.SignInAsync`çağrısıyla oturum açar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="85b1f-275">**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="85b1f-276">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="85b1f-276">Log in</span></span>

<span data-ttu-id="85b1f-277">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="85b1f-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="85b1f-278">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="85b1f-279">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="85b1f-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="85b1f-280">Oturum açma sayfasındaki form gönderildiğinde `OnPostAsync` eylemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="85b1f-281">`PasswordSignInAsync`, `_signInManager` nesnesi üzerinde çağrılır (bağımlılık ekleme tarafından sağlanır).</span><span class="sxs-lookup"><span data-stu-id="85b1f-281">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="85b1f-282">Temel `Controller` sınıfı, denetleyici yöntemlerinden erişebileceğiniz bir `User` özelliğini kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="85b1f-282">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="85b1f-283">Örneğin, `User.Claims` numaralandırmanızı ve yetkilendirme kararları almanızı sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="85b1f-283">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="85b1f-284">Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="85b1f-284">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="85b1f-285">Oturumu kapat</span><span class="sxs-lookup"><span data-stu-id="85b1f-285">Log out</span></span>

<span data-ttu-id="85b1f-286">**Oturum çıkış** bağlantısı `LogoutModel.OnPost` eylemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="85b1f-287">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="85b1f-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="85b1f-288">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="85b1f-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="85b1f-289">Test kimliği</span><span class="sxs-lookup"><span data-stu-id="85b1f-289">Test Identity</span></span>

<span data-ttu-id="85b1f-290">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="85b1f-291">Kimliği test etmek için Gizlilik sayfasına [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="85b1f-292">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="85b1f-293">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="85b1f-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="85b1f-294">Kimliği keşfet</span><span class="sxs-lookup"><span data-stu-id="85b1f-294">Explore Identity</span></span>

<span data-ttu-id="85b1f-295">Kimliği daha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="85b1f-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="85b1f-296">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="85b1f-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="85b1f-297">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="85b1f-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="85b1f-298">Kimlik bileşenleri</span><span class="sxs-lookup"><span data-stu-id="85b1f-298">Identity Components</span></span>

<span data-ttu-id="85b1f-299">Tüm kimlik bağımlı NuGet paketleri [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="85b1f-300">Kimliğin birincil paketi [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)' dır.</span><span class="sxs-lookup"><span data-stu-id="85b1f-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="85b1f-301">Bu paket, ASP.NET Core kimliği için çekirdek arabirim kümesini içerir ve `Microsoft.AspNetCore.Identity.EntityFrameworkCore`tarafından dahildir.</span><span class="sxs-lookup"><span data-stu-id="85b1f-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="85b1f-302">ASP.NET Core kimliğe geçiriliyor</span><span class="sxs-lookup"><span data-stu-id="85b1f-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="85b1f-303">Mevcut kimlik deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için bkz. [kimlik doğrulama ve kimlik geçişi](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="85b1f-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="85b1f-304">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="85b1f-304">Setting password strength</span></span>

<span data-ttu-id="85b1f-305">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="85b1f-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="85b1f-306">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="85b1f-306">Next Steps</span></span>

* <span data-ttu-id="85b1f-307">SQLite kullanarak kimlik yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın.</span><span class="sxs-lookup"><span data-stu-id="85b1f-307">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="85b1f-308">Kimliği Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="85b1f-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
