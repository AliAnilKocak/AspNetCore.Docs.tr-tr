---
title: 'ASP.NET Core giriş :::no-loc(Identity):::'
author: rick-anderson
description: :::no-loc(Identity):::ASP.NET Core bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86445443"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>ASP.NET Core giriş :::no-loc(Identity):::

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core :::no-loc(Identity)::: :

* , Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.
* Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.

Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir :::no-loc(Identity)::: veya bir dış oturum açma sağlayıcısı kullanabilirler. Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.

[ :::no-loc(Identity)::: Kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) GitHub ' da kullanılabilir. [Yapı :::no-loc(Identity)::: İskelesi](xref:security/authentication/scaffold-identity) ve şablon etkileşimini gözden geçirmek için oluşturulan dosyaları görüntüleyin :::no-loc(Identity)::: .

:::no-loc(Identity):::genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır. Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.

Bu konu başlığında, :::no-loc(Identity)::: bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz. Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir. Kullanan uygulamaları oluşturma hakkında daha ayrıntılı yönergeler için :::no-loc(Identity)::: bkz. [sonraki adımlar](#next).

[Microsoft Identity platform](/azure/active-directory/develop/) :

* Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.
* ASP.NET Core ilgisi yoktur :::no-loc(Identity)::: .

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Kimlik doğrulamasıyla bir Web uygulaması oluşturma

Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** > **Yeni** > **Proje**’yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin. Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın. **Tamam** düğmesine tıklayın.
* Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.
* **Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Yukarıdaki komut, :::no-loc(Razor)::: SQLite kullanarak bir Web uygulaması oluşturur. LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Oluşturulan proje bir [ :::no-loc(Razor)::: sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) sağlar. :::no-loc(Identity)::: :::no-loc(Razor)::: Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `:::no-loc(Identity):::` . Örneğin:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

### <a name="apply-migrations"></a>Geçişleri Uygula

Veritabanını başlatmak için geçişleri uygulayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

SQLite kullanılırken geçişler Bu adımda gerekli değildir.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

LocalDB için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Test kaydı ve oturum açma

Uygulamayı çalıştırın ve bir Kullanıcı kaydedin. Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>:::no-loc(Identity):::Hizmetleri yapılandırma

Hizmetler ' de eklenir `ConfigureServices` . Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

Önceki vurgulanan kod :::no-loc(Identity)::: varsayılan seçenek değerleriyle yapılandırılır. Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.

:::no-loc(Identity):::çağırarak etkindir <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz. `app.UseAuthorization`, uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir. `UseRouting`, `UseAuthentication` , `UseAuthorization` ve `UseEndpoints` Önceki kodda gösterilen sırada çağrılmalıdır.

Ve hakkında daha fazla bilgi için `:::no-loc(Identity):::Options` `Startup` , bkz <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> . ve [uygulama başlatma](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Scafkatlama kaydı, oturum açma, oturum kapatma ve RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

,, `Register` `Login` `LogOut` Ve `RegisterConfirmation` dosyalarını ekleyin. Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına :::no-loc(Razor)::: sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın. Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır. PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.

Yapı iskelesi hakkında daha fazla bilgi için :::no-loc(Identity)::: bkz. [ :::no-loc(Razor)::: kimlik doğrulama ile bir projede yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Kaydı İncele

Bir kullanıcı sayfadaki **Kaydet** düğmesine tıkladığında `Register` `RegisterModel.OnPostAsync` eylem çağrılır. Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Oturum açma

Oturum açma formu şu durumlarda görüntülenir:

* **Oturum aç** bağlantısı seçilidir.
* Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.

Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır. `PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..

### <a name="log-out"></a>Oturumu Kapat

**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır. 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

Önceki kodda, `return RedirectToPage();` tarayıcının yeni bir istek gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.

*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Sınamanız:::no-loc(Identity):::

Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir. Test etmek için şunları :::no-loc(Identity)::: ekleyin [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin. Oturum açma sayfasına yönlendirilirsiniz.

### <a name="explore-no-locidentity"></a>Explorer:::no-loc(Identity):::

:::no-loc(Identity):::Daha ayrıntılı incelemek için:

* [Tam kimlik UI kaynağı oluşturma](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Bileşenleri

Tüm :::no-loc(Identity)::: bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.

İçin birincil paket :::no-loc(Identity)::: [Microsoft. aspnetcore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).. Bu paket, ASP.NET Core için temel arabirim kümesini içerir :::no-loc(Identity)::: ve tarafından dahildir `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>ASP.NET Core geçiriliyor:::no-loc(Identity):::

Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için :::no-loc(Identity)::: bkz. [geçiş kimlik :::no-loc(Identity)::: doğrulaması ve ](xref:migration/identity).

## <a name="setting-password-strength"></a>Parola gücünü ayarlama

Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: ve Ekle:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>ASP.NET Core 2,1 ' de tanıtılmıştı. Çağırma `AddDefault:::no-loc(Identity):::` , aşağıdakileri çağırmaya benzer:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Daha fazla bilgi için bkz. [ADDDEFAULT :::no-loc(Identity)::: kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Statik varlıkların yayımlanmasını Engelle :::no-loc(Identity):::

Statik :::no-loc(Identity)::: varlıkların (Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları :::no-loc(Identity)::: ) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `Remove:::no-loc(Identity):::Assets` hedefi uygulamanın proje dosyasına ekleyin:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Sonraki Adımlar

* [ASP.NET Core :::no-loc(Identity)::: kaynak kodu](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)
* SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın :::no-loc(Identity)::: .
* [Yapılandırma:::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core :::no-loc(Identity)::: , ASP.NET Core uygulamalara oturum açma işlevselliği ekleyen bir üyelik sistemidir. Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir :::no-loc(Identity)::: veya bir dış oturum açma sağlayıcısı kullanabilirler. Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.

:::no-loc(Identity):::Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir. Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.

Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) .

Bu konu başlığında, :::no-loc(Identity)::: bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz. Kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için :::no-loc(Identity)::: , bu makalenin sonundaki sonraki adımlar bölümüne bakın.

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: ve Ekle:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>ASP.NET Core 2,1 ' de tanıtılmıştı. Çağırma `AddDefault:::no-loc(Identity):::` , aşağıdakileri çağırmaya benzer:

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

Daha fazla bilgi için bkz. [ADDDEFAULT :::no-loc(Identity)::: kaynağı](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Kimlik doğrulamasıyla bir Web uygulaması oluşturma

Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** > **Yeni** > **Proje**’yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin. Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın. **Tamam** düğmesine tıklayın.
* Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.
* **Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Oluşturulan proje bir [ :::no-loc(Razor)::: sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) sağlar. :::no-loc(Identity)::: :::no-loc(Razor)::: Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `:::no-loc(Identity):::` . Örneğin:

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/Account/Logout
* /:::no-loc(Identity):::/Account/Manage

### <a name="apply-migrations"></a>Geçişleri Uygula

Veritabanını başlatmak için geçişleri uygulayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Test kaydı ve oturum açma

Uygulamayı çalıştırın ve bir Kullanıcı kaydedin. Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>:::no-loc(Identity):::Hizmetleri yapılandırma

Hizmetler ' de eklenir `ConfigureServices` . Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Önceki kod :::no-loc(Identity)::: varsayılan seçenek değerleriyle yapılandırılır. Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.

:::no-loc(Identity):::[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir. `UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Daha fazla bilgi için bkz. [ :::no-loc(Identity)::: Options sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Yapı iskelesi kaydı, oturum açma ve oturum kapatma

Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına :::no-loc(Razor)::: sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın. Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır. PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.

---

### <a name="examine-register"></a>Kaydı İncele

Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır. Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync` .

**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Oturum açma

Oturum açma formu şu durumlarda görüntülenir:

* **Oturum aç** bağlantısı seçilidir.
* Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.

Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır. `PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..

### <a name="log-out"></a>Oturumu Kapat

**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır. 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.

*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Sınamanız:::no-loc(Identity):::

Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir. Test etmek için :::no-loc(Identity)::: [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Gizlilik sayfasına ekleyin.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin. Oturum açma sayfasına yönlendirilirsiniz.

### <a name="explore-no-locidentity"></a>Explorer:::no-loc(Identity):::

:::no-loc(Identity):::Daha ayrıntılı incelemek için:

* [Tam kimlik UI kaynağı oluşturma](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::Bileşenleri

Tüm :::no-loc(Identity)::: bağımlı NuGet paketleri [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.

İçin birincil paket :::no-loc(Identity)::: [Microsoft. aspnetcore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).. Bu paket, ASP.NET Core için temel arabirim kümesini içerir :::no-loc(Identity)::: ve tarafından dahildir `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .

## <a name="migrating-to-aspnet-core-no-locidentity"></a>ASP.NET Core geçiriliyor:::no-loc(Identity):::

Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için :::no-loc(Identity)::: bkz. [geçiş kimlik :::no-loc(Identity)::: doğrulaması ve ](xref:migration/identity).

## <a name="setting-password-strength"></a>Parola gücünü ayarlama

Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .

## <a name="next-steps"></a>Sonraki Adımlar

* SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın :::no-loc(Identity)::: .
* [Yapılandırma:::no-loc(Identity):::](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
