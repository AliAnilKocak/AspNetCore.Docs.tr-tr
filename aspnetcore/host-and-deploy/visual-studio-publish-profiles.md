---
title: ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri (. pubxml)
author: rick-anderson
description: Visual Studio 'da yayımlama profilleri oluşturmayı ve bunları çeşitli hedeflere ASP.NET Core uygulama dağıtımlarını yönetmek için kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659378"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri (. pubxml)

[Sayed Ibrampahashve](https://github.com/sayedihashimi) [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

Bu belge, yayımlama profillerinin oluşturulması ve kullanılması için Visual Studio 2019 veya sonraki bir sürümü kullanılarak odaklanmıştır. Visual Studio ile oluşturulan yayımlama profilleri MSBuild ve Visual Studio ile birlikte kullanılabilir. Azure 'da yayımlama yönergeleri için bkz. <xref:tutorials/publish-to-azure-webapp-using-vs>.

`dotnet new mvc` komutu, aşağıdaki kök düzeyi [\<projesi > öğesini](/visualstudio/msbuild/project-element-msbuild)içeren bir proje dosyası üretir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

Önceki `<Project>` öğenin `Sdk` özniteliği, sırasıyla *$ (Msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.props* ve *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.targets*öğesinden MSBuild [özelliklerini](/visualstudio/msbuild/msbuild-properties) ve [hedeflerini](/visualstudio/msbuild/msbuild-targets) içeri aktarır. `$(MSBuildSDKsPath)` için varsayılan konum (Visual Studio 2019 Enterprise ile) *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\enterprise\msbuild\sdk* klasörüdür.

`Microsoft.NET.Sdk.Web` (Web SDK), `Microsoft.NET.Sdk` (.NET Core SDK) ve `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)) dahil diğer SDK 'lara bağlıdır. Her bağımlı SDK ile ilişkili MSBuild özellikleri ve hedefleri içeri aktarılır. Yayımlama hedefleri, kullanılan Yayımla yöntemine göre uygun hedef kümesini içeri aktarır.

MSBuild veya Visual Studio bir projeyi yüklediğinde, aşağıdaki üst düzey eylemler gerçekleşir:

* Projeyi oluştur
* Yayımlanacak işlem dosyaları
* Dosyaları hedefe Yayımla

## <a name="compute-project-items"></a>İşlem projesi öğeleri

Proje yüklendiğinde, [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) (dosyalar) hesaplanır. Öğe türü, dosyanın nasıl işlendiğini belirler. Varsayılan olarak, *. cs* dosyaları `Compile` öğesi listesine eklenir. `Compile` öğesi listesindeki dosyalar derlenir.

`Content` öğe listesi, derleme çıktılarına ek olarak yayımlanan dosyaları içerir. Varsayılan olarak, `wwwroot\**`, `**\*.config`ve `**\*.json` desenleriyle eşleşen dosyalar `Content` öğesi listesine dahil edilir. Örneğin, `wwwroot\**` [Glob deseninin](https://gruntjs.com/configuring-tasks#globbing-patterns) *Wwwroot* klasörü ve alt klasörlerindeki tüm dosyalar eşleşir.

::: moniker range=">= aspnetcore-3.0"

Web SDK 'Sı [Razor SDK 'sını](xref:razor-pages/sdk)içeri aktarır. Sonuç olarak, `**\*.cshtml` ve `**\*.razor` desenleriyle eşleşen dosyalar da `Content` öğe listesine dahil edilmiştir.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Web SDK 'Sı [Razor SDK 'sını](xref:razor-pages/sdk)içeri aktarır. Sonuç olarak, `**\*.cshtml` düzeniyle eşleşen dosyalar `Content` öğesi listesine de dahildir.

::: moniker-end

Yayınlama listesine açıkça bir dosya eklemek için, dosyayı, [dosyaları dahil et](#include-files) bölümünde gösterildiği gibi doğrudan *. csproj* dosyasına ekleyin.

Visual Studio 'da veya komut satırından yayımlarken **Yayımla** düğmesini seçerken:

* Özellikler/öğeler hesaplanır (oluşturmak için gereken dosyalar).
* **Yalnızca Visual Studio**: NuGet paketleri geri yüklendi. (Geri yüklemenin CLı üzerinde kullanıcı tarafından açık olması gerekir.)
* Proje oluşturulur.
* Yayımlama öğeleri hesaplanır (yayımlamak için gereken dosyalar).
* Proje yayımlandı (hesaplanan dosyalar yayımlama hedefine kopyalanır).

Bir ASP.NET Core projesi Proje dosyasında `Microsoft.NET.Sdk.Web` başvurduğunda, Web uygulaması dizininin köküne bir *app_offline. htm* dosyası yerleştirilir. Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline. htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.

## <a name="basic-command-line-publishing"></a>Temel komut satırı yayımlama

Komut satırı yayımlama, .NET Core tarafından desteklenen tüm platformlarda çalışmaktadır ve Visual Studio 'Yu gerektirmez. Aşağıdaki örneklerde .NET Core CLI [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu proje dizininden çalıştırılır ( *. csproj* dosyasını içerir). Proje klasörü geçerli çalışma dizini değilse, proje dosyası yolunda açıkça geçiş yapın. Örnek:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Bir Web uygulaması oluşturmak ve yayımlamak için aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet new mvc
dotnet publish
```

`dotnet publish` komutu aşağıdaki çıkışın bir çeşidini üretir:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Varsayılan yayımlama klasörü biçimi *bin\Debug\\{Target Framework bilinen adı} \publish\\* . Örneğin, *Bin\debug\netcoreapp2,2\publish\\* .

Aşağıdaki komut bir `Release` derlemesini ve yayımlama dizinini belirtir:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

`dotnet publish` komutu, `Publish` hedefini çağıran MSBuild 'i çağırır. `dotnet publish` geçirilen parametreler MSBuild 'e geçirilir. `-c` ve `-o` parametreleri, sırasıyla MSBuild 'in `Configuration` ve `OutputPath` özelliklerine eşlenir.

MSBuild özellikleri aşağıdaki biçimlerden birini kullanarak geçirilebilir:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Örneğin, aşağıdaki komut bir ağ paylaşımında bir `Release` derlemesi yayımlar. Ağ paylaşma, eğik çizgiler (/*saat*) ile belirtilir ve tüm .NET Core desteklenen platformlarda çalışmaktadır.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Dağıtım için yayımlanan uygulamanın çalışmadığından emin olun. *Yayımla* klasöründeki dosyalar, uygulama çalışırken kilitlenir. Kilitli dosyalar kopyalanamadığından dağıtım gerçekleştirilemez.

## <a name="publish-profiles"></a>Yayımlama profilleri

Bu bölüm, bir yayımlama profili oluşturmak için Visual Studio 2019 veya üstünü kullanır. Profil oluşturulduktan sonra, Visual Studio 'dan veya komut satırından yayımlama kullanılabilir. Yayımlama profilleri Yayımlama sürecini basitleştirebilir ve herhangi bir sayıda profil bulunabilir.

Aşağıdaki yollardan birini seçerek Visual Studio 'da bir yayımlama profili oluşturun:

* **Çözüm Gezgini** projeye sağ tıklayın ve **Yayımla**' yı seçin.
* **Build** menüsünden **{Project Name} Yayımla** ' yı seçin.

Uygulama özellikleri sayfasının **Yayımla** sekmesi görüntülenir. Projenin bir yayımlama profili yoksa, **bir yayımlama hedefi seçin** sayfası görüntülenir. Aşağıdaki yayımlama hedeflerinden birini seçmeniz istenir:

* Azure uygulama hizmeti
* Linux üzerinde Azure App Service
* Azure Sanal Makineler
* Klasör
* IIS, FTP, Web Dağıtımı (herhangi bir Web sunucusu için)
* Profili içeri aktar

En uygun yayımlama hedefini belirlemek için, [hangi yayımlama seçeneklerinin benim için](/visualstudio/ide/not-in-toc/web-publish-options)uygun olduğunu öğrenin.

Hedef Yayımla **klasörü** seçildiğinde, yayımlanmış varlıkları depolamak için bir klasör yolu belirtin. Varsayılan klasör yolu, { *Project CONFIGURATION}\\{Target Framework bilinen adı} \publish\\\\* . Örneğin, *Bin\release\netcoreapp2,2\publish\\* . Tamamlanacak **Profil oluştur** düğmesini seçin.

Bir yayımlama profili oluşturulduktan sonra, **Yayımla** sekmesinin içeriği değişir. Yeni oluşturulan profil bir açılan listede görüntülenir. Aşağı açılan listenin altında **Yeni profil** oluştur ' u seçerek yeni bir profil oluşturun.

Visual Studio 'nun yayımlama aracı, yayımlama profilini açıklayan bir *Özellikler/PublishProfiles/{PROFILE Name}. pubxml* MSBuild dosyası oluşturuyor. *. Pubxml* dosyası:

* Yayımlama yapılandırma ayarlarını içerir ve yayımlama işlemi tarafından kullanılır.
* Derleme ve yayımlama işlemini özelleştirmek için değiştirilebilir.

Azure hedefine yayımlarken, *. pubxml* dosyası Azure abonelik tanımlarınızı içerir. Bu hedef türünde, bu dosyayı kaynak denetimine eklemek önerilmez. Azure olmayan bir hedefe yayımlarken, *. pubxml* dosyasını denetlemek güvenlidir.

Gizli bilgiler (yayımlama parolası gibi) Kullanıcı/makine düzeyinde şifrelenir. *Özellikler/PublishProfiles/{PROFILE Name}. pubxml. User* dosyasında depolanır. Bu dosya hassas bilgileri depolayabildiğinden, kaynak denetimine denetlenmemelidir.

ASP.NET Core Web uygulaması yayımlama hakkında genel bir bakış için bkz. <xref:host-and-deploy/index>. ASP.NET Core Web uygulaması yayımlamak için gereken MSBuild görevleri ve hedefleri, [ASPNET/WebSDK deposunda](https://github.com/aspnet/websdk)açık kaynaktır.

Aşağıdaki komutlar Folder, MSDeploy ve [kudu](https://github.com/projectkudu/kudu/wiki) yayımlama profillerini kullanabilir. MSDeploy platformlar arası destek olmadığından, aşağıdaki MSDeploy seçenekleri yalnızca Windows 'da desteklenir.

**Klasör (platformlar arası):**

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**MSDeploy paketi:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

Yukarıdaki örneklerde:

* `dotnet publish` ve `dotnet build` kudu API 'Lerini Azure 'da herhangi bir platformda yayımlamak için destekler. Visual Studio yayımlama, kudu API 'Lerini destekler, ancak Azure 'da platformlar arası yayımlama için WebSDK tarafından desteklenir.
* `dotnet publish` komutuna `DeployOnBuild` iletmeyin.

Daha fazla bilgi için bkz. [Microsoft. net. SDK. Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

Projenin *Properties/PublishProfiles* klasörüne aşağıdaki içeriğe sahip bir yayımlama profili ekleyin:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Klasör Yayımla örneği

*Folderprofile*adlı bir profille yayımlarken, aşağıdaki komutlardan birini kullanın:

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

.NET Core CLI [DotNet derleme](/dotnet/core/tools/dotnet-build) komutu, derleme ve yayımlama işlemini çalıştırmak için `msbuild` çağırır. `dotnet build` ve `msbuild` komutları, bir klasör profilinde geçirilerek eşdeğerdir. `msbuild` doğrudan Windows üzerinde çağrılırken, MSBuild 'in .NET Framework sürümü kullanılır. Klasör olmayan bir profilde `dotnet build` çağrılıyor:

* MSDeploy kullanan `msbuild`çağırır.
* Hataya neden olur (Windows üzerinde çalışırken bile). Klasör olmayan bir profille yayımlamak için `msbuild` doğrudan çağırın.

Aşağıdaki klasör yayımlama profili, Visual Studio ile oluşturulmuştur ve bir ağ paylaşımında yayımlar:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Yukarıdaki örnekte:

* `<ExcludeApp_Data>` özelliği yalnızca bir XML şeması gereksinimini karşılamak için vardır. `<ExcludeApp_Data>` özelliğinin, proje kökünde bir *App_Data* klasörü olsa bile, yayımlama işlemi üzerinde hiçbir etkisi yoktur. *App_Data* klasörü, ASP.NET 4. x projelerinde olduğu gibi özel bir işleme almaz.

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* `<LastUsedBuildConfiguration>` özelliği `Release`olarak ayarlanır. Visual Studio 'dan yayımlarken, `<LastUsedBuildConfiguration>` değeri yayımlama işlemi başlatıldığında değeri kullanılarak ayarlanır. `<LastUsedBuildConfiguration>` özeldir ve içeri aktarılan MSBuild dosyasında geçersiz kılınmamalıdır. Ancak, bu özellik aşağıdaki yaklaşımlardan birini kullanarak komut satırından geçersiz kılınabilir.
  * .NET Core CLI kullanarak:

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * MSBuild 'i kullanma:

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Daha fazla bilgi için bkz. [MSBuild: yapılandırma özelliğini ayarlama](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Komut satırından bir MSDeploy uç noktasına yayımlama

Aşağıdaki örnek, *AzureWebApp*adlı Visual Studio tarafından oluşturulan bir ASP.NET Core Web uygulamasını kullanır. Visual Studio ile bir Azure Apps yayımlama profili eklenir. Profil oluşturma hakkında daha fazla bilgi için, [Yayımlama profilleri](#publish-profiles) bölümüne bakın.

Uygulamayı bir yayımlama profili kullanarak dağıtmak için, bir Visual Studio **Geliştirici Komut İstemi**`msbuild` komutunu yürütün. Komut istemi, Windows görev çubuğundaki **Başlat** menüsünün *Visual Studio* klasöründe bulunur. Daha kolay erişim için, Visual Studio 'daki **Araçlar** menüsüne komut istemi ekleyebilirsiniz. Daha fazla bilgi için bkz. [Visual Studio için geliştirici komut istemi](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild aşağıdaki komut sözdizimini kullanır:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {PATH} uygulamanın proje dosyasının yolunu &ndash;.
* {PROFILE} &ndash; yayımlama profilinin adı.
* {USERNAME} MSDeploy Kullanıcı adı &ndash;. {USERNAME}, yayımlama profilinde bulunabilir.
* {PASSWORD} MSDeploy parolasını &ndash;. {PROFILE} öğesinden {PASSWORD} öğesini edinin *. PublishSettings* dosyası. ' Nı indirin *. PublishSettings* dosyası şunlardan biri:
  * **Çözüm Gezgini**: > **bulut Gezginini** **görüntüle** ' yi seçin. Azure aboneliğinize bağlanın. **Uygulama hizmetleri**'ni açın. Uygulamaya sağ tıklayın. **Yayımlama profilini indir**' i seçin.
  * Azure portal: Web uygulamasının **genel bakış** panelinde **Yayımlama profilini al** ' ı seçin.

Aşağıdaki örnek, *AzureWebApp-Web dağıtımı*adlı bir yayımlama profili kullanır:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Bir yayımlama profili, Windows komut kabuğu 'ndan .NET Core CLI [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutuyla da kullanılabilir:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> `dotnet msbuild` komutu platformlar arası bir komuttur ve macOS ve Linux üzerinde ASP.NET Core uygulamalar derleyebilir. Ancak, macOS ve Linux 'ta MSBuild, bir uygulamayı Azure 'a veya diğer MSDeploy uç noktalarına dağıtmıyor.

## <a name="set-the-environment"></a>Ortamı ayarlama

Uygulamanın [ortamını](xref:fundamentals/environments)ayarlamak için Yayımlama profili ( *. pubxml*) veya proje dosyasına `<EnvironmentName>` özelliğini dahil edin:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

*Web. config* dönüştürmelerine ihtiyaç duyuyorsanız (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak), bkz. <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Dosyaları Dışla

ASP.NET Core Web Apps yayımlandığında, aşağıdaki varlıklar dahil edilmiştir:

* Yapı yapıtları
* Aşağıdaki glob desenleriyle eşleşen klasörler ve dosyalar:
  * `**\*.config` (örneğin, *Web. config*)
  * `**\*.json` (örneğin, *appSettings. JSON*)
  * `wwwroot\**`

MSBuild, [Glob desenlerini](https://gruntjs.com/configuring-tasks#globbing-patterns)destekler. Örneğin, aşağıdaki `<Content>` öğesi, metin ( *. txt*) dosyalarının *wwwroot\content* klasörüne ve alt klasörlerine kopyalanmasını önler:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Önceki biçimlendirme bir yayımlama profiline veya *. csproj* dosyasına eklenebilir. *. Csproj* dosyasına eklendiğinde, kural projedeki tüm yayımlama profillerine eklenir.

Aşağıdaki `<MsDeploySkipRules>` öğesi, tüm dosyaları *wwwroot\content* klasöründen dışlar:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>`, dağıtım sitesinden *atlama* hedeflerini silmez. `<Content>` hedeflenen dosya ve klasörler dağıtım sitesinden silinir. Örneğin, dağıtılan bir Web uygulamasının aşağıdaki dosyalar olduğunu varsayalım:

* *Görünümler/Home/about1. cshtml*
* *Görünümler/Home/About2. cshtml*
* *Görünümler/Home/About3. cshtml*

Aşağıdaki `<MsDeploySkipRules>` öğeleri eklendiyse, bu dosyalar dağıtım sitesinde silinmez.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Önceki `<MsDeploySkipRules>` öğeleri *Atlanan* dosyaların dağıtılmasını engeller. Dağıtıldıktan sonra bu dosyaları silmez.

Aşağıdaki `<Content>` öğesi, dağıtım sitesindeki hedeflenen dosyaları siler:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Önceki `<Content>` öğesiyle komut satırı dağıtımını kullanmak aşağıdaki çıkışın bir varyasyonunu verir:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>İçerme dosyaları

Aşağıdaki bölümlerde, yayımlama zamanında dosya ekleme için farklı yaklaşımlar ana hatlarıyla verilmiştir. [Genel dosya ekleme](#general-file-inclusion) bölümü, Web SDK 'sında bir Yayımla hedefi dosyası tarafından belirtilen `DotNetPublishFiles` öğesini kullanır. [Seçmeli dosya ekleme](#selective-file-inclusion) bölümü, .NET Core SDK bir Yayımla hedefi dosyası tarafından belirtilen `ResolvedFileToPublish` öğesini kullanır. Web SDK .NET Core SDK bağlı olduğundan, her iki öğe bir ASP.NET Core projesinde kullanılabilir.

### <a name="general-file-inclusion"></a>Genel dosya ekleme

Aşağıdaki örnek `<ItemGroup>` öğesi, proje dizininin dışında bulunan bir klasörü yayımlanmış sitenin bir klasörüne kopyalamayı gösterir. Aşağıdaki biçimlendirme `<ItemGroup>` eklenen dosyalar varsayılan olarak dahil edilir.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Önceki işaretlemesi:

* *. Csproj* dosyasına veya yayımlama profiline eklenebilir. *. Csproj* dosyasına eklenirse, bu, projedeki her bir yayımlama profiline eklenir.
* `Include` özniteliğinin glob düzeniyle eşleşen dosyaları depolamak için bir `_CustomFiles` öğesi bildirir. Düzende başvurulan *görüntüler* klasörü, proje dizininin dışında bulunur. `$(MSBuildProjectDirectory)`adlı [ayrılmış bir özellik](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), proje dosyasının mutlak yoluna çözümlenir.
* `DotNetPublishFiles` öğe için dosyaların bir listesini sağlar. Varsayılan olarak, öğenin `<DestinationRelativePath>` öğesi boştur. Varsayılan değer, biçimlendirmede geçersiz kılınır ve `%(RecursiveDir)`gibi [iyi bilinen öğe meta verilerini](/visualstudio/msbuild/msbuild-well-known-item-metadata) kullanır. İç metin, yayımlanan sitenin *Wwwroot/görüntüler* klasörünü temsil eder.

### <a name="selective-file-inclusion"></a>Seçmeli dosya ekleme

Aşağıdaki örnekte vurgulanan biçimlendirme şunları göstermektedir:

* Projenin dışında bulunan bir dosyayı yayınlanan sitenin *Wwwroot* klasörüne kopyalama. *ReadMe2.MD* dosyasının adı korunur.
* *Wwwroot\content* klasörü dışlanıyor.
* *Views\home\about2,cshtml*hariç tutulanıyor.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

Yukarıdaki örnekte, varsayılan davranışı `Include` özniteliğinde sunulan dosyaları her zaman yayımlanan siteye kopyalamak için olan `ResolvedFileToPublish` öğesi kullanılmıştır. `Never` veya `PreserveNewest`iç metniyle bir `<CopyToPublishDirectory>` alt öğesi ekleyerek varsayılan davranışı geçersiz kılın. Örnek:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Daha fazla dağıtım örneği için bkz. [Web SDK deposu Benioku dosyası](https://github.com/aspnet/websdk).

## <a name="run-a-target-before-or-after-publishing"></a>Yayımlamadan önce veya sonra bir hedef Çalıştır

Yerleşik `BeforePublish` ve `AfterPublish` hedefleri, yayınlama hedefinden önce veya sonra bir hedef yürütür. Aşağıdaki öğeleri yayımlama profiline, yayımlamadan önce ve sonra da konsol iletilerini günlüğe kaydetmek için ekleyin:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Güvenilmeyen bir sertifikayı kullanarak bir sunucuya yayımlama

Yayımlama profiline `True` değeri olan `<AllowUntrustedCertificate>` özelliğini ekleyin:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Kudu hizmeti

Azure App Service Web uygulaması dağıtımında dosyaları görüntülemek için [kudu hizmetini](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)kullanın. `scm` belirtecini Web uygulaması adına ekleyin. Örnek:

| URL'si                                    | Sonuç       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Web App      |
| `http://mysite.scm.azurewebsites.net/` | Kudu hizmeti |

Dosyaları görüntülemek, düzenlemek, silmek veya eklemek için [hata ayıklama konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) menü öğesini seçin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Web dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy), IIS sunucularına Web uygulamaları ve Web siteleri dağıtımını basitleştirir.
* [Web SDK GitHub deposu](https://github.com/aspnet/websdk/issues): dağıtım için dosya sorunları ve istek özellikleri.
* [Visual Studio 'dan bir Azure VM 'de ASP.NET Web uygulaması yayımlama](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
