---
title: ASP.NET core'da geliştirmede uygulama gizli anahtarlarının güvenli bir şekilde depolanması
author: rick-anderson
description: ASP.NET Core uygulaması geliştirme sırasında uygulama gizli diziler olarak hassas bilgilerini depolamak ve almak öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/13/2019
uid: security/app-secrets
ms.openlocfilehash: 195901e466262020fd1217bd9dfb6162910bb861
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899525"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="8e9c6-103">ASP.NET core'da geliştirmede uygulama gizli anahtarlarının güvenli bir şekilde depolanması</span><span class="sxs-lookup"><span data-stu-id="8e9c6-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="8e9c6-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), ve [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8e9c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8e9c6-105">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8e9c6-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8e9c6-106">Bu belge, depolamak ve ASP.NET Core uygulaması geliştirme sırasında hassas verileri almak için teknikleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-106">This document explains techniques for storing and retrieving sensitive data during the development of an ASP.NET Core app.</span></span> <span data-ttu-id="8e9c6-107">Asla kaynak kodunda parola ya da diğer hassas verileri depolayın.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="8e9c6-108">Üretim gizli dizileri olmamalıdır kullanılabilir geliştirme veya test için.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="8e9c6-109">Depolama ve Azure test ve üretim parolalarını ile korumak [Azure Key Vault yapılandırma sağlayıcısı](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8e9c6-109">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="8e9c6-110">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="8e9c6-110">Environment variables</span></span>

<span data-ttu-id="8e9c6-111">Ortam değişkenleri, uygulama gizli anahtarlarının kod veya yerel yapılandırma dosyaları depolama önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-111">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="8e9c6-112">Ortam değişkenleri tüm daha önce belirtilen yapılandırma kaynakları için yapılandırma değerlerini geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-112">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="8e9c6-113">Ortam değişkeni değerlerini okunmasını çağırarak yapılandırma <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> içinde `Startup` Oluşturucusu:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-113">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="8e9c6-114">ASP.NET Core web uygulaması, göz önünde bulundurun **bireysel kullanıcı hesapları** güvenlik etkin.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="8e9c6-115">Varsayılan veritabanı bağlantı dizesi projesinin dahil *appsettings.json* anahtar dosyasıyla `DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="8e9c6-116">Varsayılan bağlantı dizesini kullanıcı modunda çalışır ve bir parola gerektirmez LocalDB ' dir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="8e9c6-117">Uygulama dağıtımı sırasında `DefaultConnection` anahtar değeri ile bir ortam değişken değerini geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="8e9c6-118">Ortam değişkeni hassas kimlik bilgileriyle tam bağlantı dizesi depolayabilir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="8e9c6-119">Ortam değişkenleri genellikle düz ve şifresiz metin olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="8e9c6-120">İşlem ve makine tehlikedeyse, ortam değişkenleri Güvenilmeyen taraflar tarafından erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="8e9c6-121">Kullanıcı gizli dizilerinin açığa çıkmasını önlemek amacıyla ek ölçüler gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="8e9c6-122">Gizli dizi Yöneticisi</span><span class="sxs-lookup"><span data-stu-id="8e9c6-122">Secret Manager</span></span>

<span data-ttu-id="8e9c6-123">Gizli dizi Yöneticisi Aracı, bir ASP.NET Core projesi geliştirilmesi sırasında hassas verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="8e9c6-124">Bu bağlamda bir hassas verileri uygulama gizli anahtarı bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="8e9c6-125">Uygulama gizli anahtarlarının proje ağacı ayrı bir konumda depolanır.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="8e9c6-126">Uygulama gizli dizileri belirli bir projeyle ilişkili veya birkaç projede paylaşılan.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="8e9c6-127">Uygulama gizli anahtarlarının kaynak denetimine denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="8e9c6-128">Gizli dizi Yöneticisi aracını depolanan gizli dizileri şifrelemez ve güvenilir bir deposu olarak değerlendirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="8e9c6-129">Bu, yalnızca geliştirme amaçları içindir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-129">It's for development purposes only.</span></span> <span data-ttu-id="8e9c6-130">Anahtarları ve değerleri, kullanıcı profili dizini bir JSON yapılandırma dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="8e9c6-131">Gizli dizi Yöneticisi aracını nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="8e9c6-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="8e9c6-132">Gizli dizi Yöneticisi aracını nerede ve nasıl depolanacağını ve gibi uygulama ayrıntılarını dengelediği.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="8e9c6-133">Bu uygulama ayrıntılarını bilmeden aracını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="8e9c6-134">Değerleri, yerel makinede bulunan JSON yapılandırma dosyası bir sistem korumalı kullanıcı profili klasöründe depolanır:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="8e9c6-135">Windows</span><span class="sxs-lookup"><span data-stu-id="8e9c6-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="8e9c6-136">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="8e9c6-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="8e9c6-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8e9c6-138">Dosya sistemi yolu:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="8e9c6-139">Önceki dosya yolları ve yerine `<user_secrets_id>` ile `UserSecretsId` belirtilen değeri *.csproj* dosya.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="8e9c6-140">Konum veya gizli dizi Yöneticisi Aracı ile kaydedilen verilerin biçimi bağımlı kod yazmayın.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="8e9c6-141">Bu uygulama ayrıntılarını değişebilir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-141">These implementation details may change.</span></span> <span data-ttu-id="8e9c6-142">Örneğin, gizli değerleri şifreli değildir, ancak gelecekte olabilir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="8e9c6-143">Gizli dizi Yöneticisi aracını yükleyin</span><span class="sxs-lookup"><span data-stu-id="8e9c6-143">Install the Secret Manager tool</span></span>

<span data-ttu-id="8e9c6-144">Gizli dizi Yöneticisi Aracı ile .NET Core SDK'sı 2.1.300 .NET Core CLI ile birlikte gelen veya üzeri.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-144">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="8e9c6-145">Aracı yükleme 2.1.300 önce .NET Core SDK sürümleri için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-145">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="8e9c6-146">Çalıştırma `dotnet --version` yüklü .NET Core SDK'sı sürüm numarasını görmek için bir komut kabuğu'ndan.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-146">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="8e9c6-147">.NET Core SDK kullanılan araç içeriyorsa, bir uyarı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-147">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="8e9c6-148">Yükleme [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) ASP.NET Core projenizdeki NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-148">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="8e9c6-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-149">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="8e9c6-150">Aracı yüklemesini doğrulamak için bir komut kabuğuna şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-150">Execute the following command in a command shell to validate the tool installation:</span></span>

```console
dotnet user-secrets -h
```

<span data-ttu-id="8e9c6-151">Gizli dizi Yöneticisi aracını örnek kullanımı, Seçenekler ve komut Yardımı görüntüler:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-151">The Secret Manager tool displays sample usage, options, and command help:</span></span>

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> <span data-ttu-id="8e9c6-152">Aynı dizinde olmalıdır *.csproj* tanımlanan araçları çalıştırmak için dosya *.csproj* dosyanın `DotNetCliToolReference` öğeleri.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-152">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="8e9c6-153">Gizli depolama etkinleştir</span><span class="sxs-lookup"><span data-stu-id="8e9c6-153">Enable secret storage</span></span>

<span data-ttu-id="8e9c6-154">Projeye özgü yapılandırma ayarlarını, kullanıcı profilinizin depolanan gizli dizi Yöneticisi aracı çalışır.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-154">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8e9c6-155">Gizli dizi Yöneticisi aracını içeren bir `init` .NET Core SDK'sı 3.0.100 komutunu veya üzeri.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-155">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="8e9c6-156">Kullanıcı parolalarını kullanmak için proje dizininde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-156">To use user secrets, run the following command in the project directory:</span></span>

```console
dotnet user-secrets init
```

<span data-ttu-id="8e9c6-157">Önceki komutta ekler bir `UserSecretsId` öğesi içinde bir `PropertyGroup` , *.csproj* dosya.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-157">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="8e9c6-158">Varsayılan olarak, iç metni `UserSecretsId` bir GUID'dir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-158">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="8e9c6-159">İç metni isteğe bağlıdır, ancak projeye benzersizdir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-159">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8e9c6-160">Kullanıcı parolalarını kullanmak için tanımlamak bir `UserSecretsId` öğesi içinde bir `PropertyGroup` , *.csproj* dosya.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-160">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="8e9c6-161">İç metni `UserSecretsId` isteğe bağlıdır, ancak projeye benzersizdir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-161">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="8e9c6-162">Geliştiriciler genellikle oluşturmak için bir GUID `UserSecretsId`.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-162">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="8e9c6-163">Visual Studio'da, Çözüm Gezgini'nde projeye sağ tıklayıp seçin **nıcı parolalarını Yönet** bağlam menüsünden.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-163">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="8e9c6-164">Bu hareket ekler bir `UserSecretsId` öğesi için bir GUID ile doldurulmuş *.csproj* dosya.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-164">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="8e9c6-165">Bir gizli dizisi ayarlayın</span><span class="sxs-lookup"><span data-stu-id="8e9c6-165">Set a secret</span></span>

<span data-ttu-id="8e9c6-166">Bir anahtarı ve değeri içeren bir uygulama gizli anahtarı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-166">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="8e9c6-167">Proje gizliliği ilişkilendirilen `UserSecretsId` değeri.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-167">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="8e9c6-168">Örneğin, hangi dizininden aşağıdaki komutu çalıştırın *.csproj* dosyası var:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-168">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="8e9c6-169">Önceki örnekte, iki nokta üst üste gösterir `Movies` bir nesne ile sabitidir bir `ServiceApiKey` özelliği.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-169">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="8e9c6-170">Gizli dizi Yöneticisi Aracı diğer dizinlerden çok kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-170">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="8e9c6-171">Kullanım `--project` seçeneği, dosya sistemi yolu sağlamak *.csproj* dosya yok.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-171">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="8e9c6-172">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-172">For example:</span></span>

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="8e9c6-173">Visual Studio'da düzleştirme JSON yapısı</span><span class="sxs-lookup"><span data-stu-id="8e9c6-173">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="8e9c6-174">Visual Studio'nun **nıcı parolalarını Yönet** hareket açılır bir *secrets.json* dosyasını metin düzenleyicisinde.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-174">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="8e9c6-175">Öğesinin içeriğini değiştirin *secrets.json* depolanacak anahtar-değer çiftleri ile.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-175">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="8e9c6-176">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-176">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8e9c6-177">JSON yapısı değişiklikleri sonra düzleştirilmiş `dotnet user-secrets remove` veya `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-177">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="8e9c6-178">Örneğin, çalışan `dotnet user-secrets remove "Movies:ConnectionString"` daraltır `Movies` nesne sabit değeri.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-178">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="8e9c6-179">Değiştirilen dosya şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-179">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="8e9c6-180">Birden çok gizli dizileri ayarlayın</span><span class="sxs-lookup"><span data-stu-id="8e9c6-180">Set multiple secrets</span></span>

<span data-ttu-id="8e9c6-181">Gizli dizi için JSON yönelterek ayarlanabilir `set` komutu.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-181">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="8e9c6-182">Aşağıdaki örnekte, *söz konusu input.json* dosyanın içeriğini yöneltilen için `set` komutu.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-182">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="8e9c6-183">Windows</span><span class="sxs-lookup"><span data-stu-id="8e9c6-183">Windows</span></span>](#tab/windows)

<span data-ttu-id="8e9c6-184">Bir komut kabuğunu açın ve aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-184">Open a command shell, and execute the following command:</span></span>

  ```console
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="8e9c6-185">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="8e9c6-185">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="8e9c6-186">Bir komut kabuğunu açın ve aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-186">Open a command shell, and execute the following command:</span></span>

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="8e9c6-187">Gizli dizi erişimi</span><span class="sxs-lookup"><span data-stu-id="8e9c6-187">Access a secret</span></span>

<span data-ttu-id="8e9c6-188">[ASP.NET Core yapılandırma API'si](xref:fundamentals/configuration/index) gizli dizi Yöneticisi gizli dizilere erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-188">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="8e9c6-189">Projeniz .NET Framework hedefliyorsa, yükleme [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-189">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8e9c6-190">Proje çağırdığında, ASP.NET Core 2.0 veya sonraki sürümlerde, kullanıcı parolaları yapılandırma kaynağı otomatik olarak geliştirme modunda eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> önceden yapılandırılmış varsayılan ana bilgisayar yeni bir örneğini başlatmak için.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-190">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="8e9c6-191">`CreateDefaultBuilder` çağrıları <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> olduğunda <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> olduğu <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-191">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="8e9c6-192">Zaman `CreateDefaultBuilder` değilse çağrılır, kullanıcı parolaları yapılandırma kaynağı açıkça çağrılarak ekleme <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> içinde `Startup` Oluşturucusu.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-192">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor.</span></span> <span data-ttu-id="8e9c6-193">Çağrı `AddUserSecrets` yalnızca, uygulama geliştirme ortamında, aşağıdaki örnekte gösterildiği gibi çalışır:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-193">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="8e9c6-194">Yükleme [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-194">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="8e9c6-195">Kullanıcı parolaları yapılandırma kaynağı çağrısıyla ekleme <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> içinde `Startup` Oluşturucusu:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-195">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="8e9c6-196">Kullanıcı parolaları aracılığıyla alınabilir `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-196">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="8e9c6-197">Gizli diziler için bir POCO eşleme</span><span class="sxs-lookup"><span data-stu-id="8e9c6-197">Map secrets to a POCO</span></span>

<span data-ttu-id="8e9c6-198">Tüm nesne sabit değeri bir POCO (basit bir .NET sınıf özelliklere sahip) eşlemeye ilgili özellikleri toplamak için yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-198">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e9c6-199">Önceki gizli dizileri için bir POCO eşleyin `Configuration` API'nin [Nesne grafiği bağlama](xref:fundamentals/configuration/index#bind-to-an-object-graph) özelliği.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-199">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="8e9c6-200">Aşağıdaki kod bir özel bağlar `MovieSettings` POCO ve erişimleri `ServiceApiKey` özellik değeri:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-200">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="8e9c6-201">`Movies:ConnectionString` Ve `Movies:ServiceApiKey` gizli dizileri ilgili özellikler eşleştirilmiş `MovieSettings`:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-201">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="8e9c6-202">Gizli dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="8e9c6-202">String replacement with secrets</span></span>

<span data-ttu-id="8e9c6-203">Parolaları düz metin halinde depolanmasını güvenli değil.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-203">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="8e9c6-204">Örneğin, bir veritabanı bağlantı dizesi, içinde depolanan *appsettings.json* belirtilen kullanıcı için bir parola içerebilir:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-204">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="8e9c6-205">Bir gizli dizi parolayı depolamak daha güvenli bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-205">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="8e9c6-206">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-206">For example:</span></span>

```console
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="8e9c6-207">Kaldırma `Password` bağlantı dizesinde anahtar-değer çiftinden *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-207">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="8e9c6-208">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-208">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="8e9c6-209">Parolanın değer ayarlanabilir bir <xref:System.Data.SqlClient.SqlConnectionStringBuilder> nesnenin <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> özelliği bağlantı dizesini tamamlamak için:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-209">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="8e9c6-210">Gizli dizilerini listeleme</span><span class="sxs-lookup"><span data-stu-id="8e9c6-210">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e9c6-211">Hangi dizininden aşağıdaki komutu çalıştırarak *.csproj* dosyası var:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-211">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets list
```

<span data-ttu-id="8e9c6-212">Aşağıdaki çıktı görünür:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-212">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="8e9c6-213">Önceki örnekte, iki nokta üst üste anahtar adlarının nesne hiyerarşisi içinde gösterir. *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="8e9c6-213">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="8e9c6-214">Tek bir gizli dizi Kaldır</span><span class="sxs-lookup"><span data-stu-id="8e9c6-214">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e9c6-215">Hangi dizininden aşağıdaki komutu çalıştırarak *.csproj* dosyası var:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-215">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="8e9c6-216">Uygulamanın *secrets.json* dosyası ile ilişkili anahtar-değer çiftini kaldırmak için değiştirildiği `MoviesConnectionString` anahtarı:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-216">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="8e9c6-217">Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-217">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="8e9c6-218">Tüm gizli dizileri kaldırın</span><span class="sxs-lookup"><span data-stu-id="8e9c6-218">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="8e9c6-219">Hangi dizininden aşağıdaki komutu çalıştırarak *.csproj* dosyası var:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-219">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```console
dotnet user-secrets clear
```

<span data-ttu-id="8e9c6-220">Uygulama için tüm kullanıcı gizli dizilerini gelen silinmiş *secrets.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-220">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="8e9c6-221">Çalışan `dotnet user-secrets list` aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="8e9c6-221">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="8e9c6-222">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8e9c6-222">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
