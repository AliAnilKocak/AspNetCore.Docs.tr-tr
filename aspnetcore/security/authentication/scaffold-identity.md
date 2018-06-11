---
title: ASP.NET Core projelerinde iskele kimliği
author: rick-anderson
description: ASP.NET Core projesinde kimlik iskele öğrenin.
manager: wpickett
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 5/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/scaffold-identity
ms.openlocfilehash: e7a2cf3633ed48a0d2030739cdc092441fcae2ff
ms.sourcegitcommit: 63fb07fb3f71b32daf2c9466e132f2e7cc617163
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2018
ms.locfileid: "35252041"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core projelerinde iskele kimliği

tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2.1 ve üzeri sağlar [ASP.NET Core kimliği](xref:security/authentication/identity) olarak bir [Razor sınıf kitaplığı](xref:mvc/razor-pages/ui-class). Kimliği içeren uygulamaları kimlik Razor sınıf kitaplığı (RCL) bulunan kaynak koduna seçerek eklemek için iskele kurucu uygulayabilirsiniz. Kodu değiştirin ve davranışını değiştirmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, kayıt için kullanılan kodu oluşturmak için iskele kurucu istemeniz. Oluşturulan kod kimlik RCL aynı kodu daha önceliklidir.

Yapmak uygulamaları **değil** dahil kimlik doğrulama RCL kimlik paketini eklemek için iskele kurucu uygulayabilirsiniz. Oluşturulacak kimlik kodu seçme seçeneğiniz vardır.

Gerekli kodu çoğunu iskele kurucu oluşturur ancak işlemi tamamlamak için projenize güncelleştirmeniz gerekir. Bu belge kimliği yapı iskelesi güncelleştirmesini tamamlamak için gereken adımları açıklar.

Kimlik iskele kurucu çalıştırdığınızda, bir *ScaffoldingReadme.txt* dosya proje dizininde oluşturulur. *ScaffoldingReadme.txt* dosyası ne kimlik yapı iskelesi güncelleştirmesini tamamlamak için gerekli olan genel yönergeleri içerir. Bu belgede daha daha kapsamlı yönergeler içeren *ScaffoldingReadme.txt* dosya.

Dosya farklar gösterilmektedir ve dışında değişiklikleri geri olanak sağlayan bir kaynak denetim sistemi kullanmanızı öneririz. Değişiklikleri kimlik iskele kurucu çalıştırdıktan sonra inceleyin.

## <a name="scaffold-identity-into-an-empty-project"></a>Boş bir projeye iskele kimliği

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aşağıdaki vurgulanan çağrıları ekleme `Startup` sınıfı:

[!code-csharp[Main](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Varolan yetkilendirme olmadan Razor projeye iskele kimliği

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

Kimlik yapılandırılmıştır *Areas/Identity/IdentityHostingStartup.cs*. Daha fazla bilgi için bkz: [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Geçişler, UseAuthentication ve düzeni

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

İçinde `Configure` yöntemi `Startup` sınıfı, arama [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) sonra `UseStaticFiles`:

[!code-csharp[Main](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Düzen değişiklikleri

İsteğe bağlı: Kısmi oturum açma ekleme (`_LoginPartial`) Düzen dosyası için:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Yetkilendirme ile Razor projeye iskele kimliği

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register

[!INCLUDE[](~/includes/webapp-alias-notice.md)]
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
Bazı kimlik seçenekleri yapılandırılan *Areas/Identity/IdentityHostingStartup.cs*. Daha fazla bilgi için bkz: [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Varolan yetkilendirme olmadan bir MVC projeye iskele kimliği

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

İsteğe bağlı: Kısmi oturum açma ekleme (`_LoginPartial`) için *Views/Shared/_Layout.cshtml* dosyası:

[!code-html[Main](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Taşıma *Pages/Shared/_LoginPartial.cshtml* dosya *Views/Shared/_LoginPartial.cshtml*

Kimlik yapılandırılmıştır *Areas/Identity/IdentityHostingStartup.cs*. Daha fazla bilgi için IHostingStartup bakın.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Çağrı [UseAuthentication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) sonra `UseStaticFiles`:

[!code-csharp[Main](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Yetkilendirme ile MVC projeye bir iskele kimliği

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Silme *sayfaları/paylaşılan* klasörüne ve o klasördeki dosyaları.
