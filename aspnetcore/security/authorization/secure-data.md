---
title: Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma
author: rick-anderson
description: Yetkilendirmeyle korunan kullanıcı verileriyle ASP.NET Core bir Web uygulaması oluşturmayı öğrenin. HTTPS, kimlik doğrulaması, güvenlik, içerir ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: 5f86e514ee6339888171d83ab3117e9b3fcf107e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627825"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="0d594-104">Yetkilendirmeyle korunan kullanıcı verileriyle ASP.NET Core bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="0d594-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="0d594-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0d594-106">[Bu PDF 'ye](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) bakın</span><span class="sxs-lookup"><span data-stu-id="0d594-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d594-107">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0d594-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0d594-108">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0d594-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0d594-109">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="0d594-109">There are three security groups:</span></span>

* <span data-ttu-id="0d594-110">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0d594-111">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0d594-112">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="0d594-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0d594-113">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0d594-114">Bu belgedeki görüntüler en son şablonlarla tam olarak eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="0d594-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="0d594-115">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="0d594-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0d594-116">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için**yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="0d594-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0d594-117">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0d594-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0d594-118">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="0d594-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="0d594-120">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="0d594-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="0d594-122">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0d594-122">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="0d594-124">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0d594-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0d594-125">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="0d594-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="0d594-127">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="0d594-127">The administrator has all privileges.</span></span> <span data-ttu-id="0d594-128">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0d594-129">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0d594-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0d594-130">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="0d594-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0d594-131">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0d594-132">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0d594-133">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d594-134">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0d594-134">Prerequisites</span></span>

<span data-ttu-id="0d594-135">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="0d594-135">This tutorial is advanced.</span></span> <span data-ttu-id="0d594-136">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="0d594-136">You should be familiar with:</span></span>

* [<span data-ttu-id="0d594-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d594-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0d594-138">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="0d594-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0d594-139">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="0d594-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0d594-140">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="0d594-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0d594-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0d594-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0d594-142">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="0d594-142">The starter and completed app</span></span>

<span data-ttu-id="0d594-143">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="0d594-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0d594-144">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="0d594-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0d594-145">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="0d594-145">The starter app</span></span>

<span data-ttu-id="0d594-146">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="0d594-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0d594-147">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0d594-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0d594-148">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="0d594-148">Secure user data</span></span>

<span data-ttu-id="0d594-149">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0d594-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0d594-150">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0d594-151">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="0d594-151">Tie the contact data to the user</span></span>

<span data-ttu-id="0d594-152">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d594-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0d594-153">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0d594-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0d594-154">`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="0d594-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0d594-155">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="0d594-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0d594-156">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="0d594-157">Rol hizmetlerini Ekle Identity</span><span class="sxs-lookup"><span data-stu-id="0d594-157">Add Role services to Identity</span></span>

<span data-ttu-id="0d594-158">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0d594-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="0d594-159">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="0d594-159">Require authenticated users</span></span>

<span data-ttu-id="0d594-160">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için geri dönüş kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="0d594-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="0d594-161">Önceki vurgulanmış kod, [geri dönüş kimlik doğrulama ilkesini](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="0d594-162">Geri dönüş kimlik doğrulama ilkesi ***all*** , Razor bir kimlik doğrulama özniteliğiyle sayfalar, denetleyiciler veya eylem yöntemleri dışında tüm kullanıcıların kimliklerinin doğrulanmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0d594-162">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="0d594-163">Örneğin, Razor Sayfalar, denetleyiciler veya eylem yöntemleri, `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` geri dönüş kimlik doğrulama ilkesi yerine uygulanan kimlik doğrulama özniteliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d594-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="0d594-164">Geri dönüş kimlik doğrulama ilkesi:</span><span class="sxs-lookup"><span data-stu-id="0d594-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="0d594-165">Açıkça bir kimlik doğrulama ilkesi belirtmeyen tüm isteklere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0d594-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="0d594-166">Endpoint Routing tarafından sunulan istekler için, bu, yetkilendirme özniteliği belirtmeyen tüm uç noktaları içerir.</span><span class="sxs-lookup"><span data-stu-id="0d594-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="0d594-167">[Statik dosyalar](xref:fundamentals/static-files)gibi yetkilendirme ara yazılımı sonrasında diğer ara yazılım tarafından hizmet verilen istekler için, bu ilkeyi tüm isteklere uygular.</span><span class="sxs-lookup"><span data-stu-id="0d594-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="0d594-168">Geri dönüş kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="0d594-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0d594-169">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="0d594-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0d594-170"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Sınıfı da içerir <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0d594-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0d594-171">, `DefaultPolicy` `[Authorize]` İlke belirtilmediğinde özniteliğiyle kullanılan ilkedir.</span><span class="sxs-lookup"><span data-stu-id="0d594-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="0d594-172">`[Authorize]` , aksine adlandırılmış bir ilke içermez `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="0d594-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="0d594-173">İlkeler hakkında daha fazla bilgi için bkz <xref:security/authorization/policies> ..</span><span class="sxs-lookup"><span data-stu-id="0d594-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="0d594-174">MVC denetleyicilerinin ve Razor sayfaların tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için alternatif bir yol yetkilendirme filtresi ekliyor:</span><span class="sxs-lookup"><span data-stu-id="0d594-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="0d594-175">Yukarıdaki kod, bir yetkilendirme filtresi kullanır ve geri dönüş ilkesini ayarlama Endpoint Routing kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d594-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="0d594-176">Geri dönüş ilkesini ayarlamak, tüm kullanıcıların kimliğinin doğrulanmasını gerektirmek için tercih edilen yoldur.</span><span class="sxs-lookup"><span data-stu-id="0d594-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="0d594-177">[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) `Index` `Privacy` Anonim kullanıcıların, kaydolmadan önce site hakkında bilgi alması Için ve sayfalarına AllowAnonymous ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0d594-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0d594-178">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0d594-178">Configure the test account</span></span>

<span data-ttu-id="0d594-179">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="0d594-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0d594-180">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d594-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0d594-181">Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="0d594-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0d594-182">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="0d594-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0d594-183">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="0d594-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0d594-184">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0d594-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0d594-185">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0d594-186">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="0d594-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0d594-187">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="0d594-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0d594-188">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0d594-189">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="0d594-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0d594-190">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0d594-191">`ContactIsOwnerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d594-192">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="0d594-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0d594-193">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="0d594-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0d594-194">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="0d594-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="0d594-195">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="0d594-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0d594-196">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="0d594-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0d594-197">`Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="0d594-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0d594-198">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="0d594-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0d594-199">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="0d594-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0d594-200">`ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0d594-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0d594-201">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-201">Create a manager authorization handler</span></span>

<span data-ttu-id="0d594-202">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d594-203">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="0d594-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0d594-204">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="0d594-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0d594-205">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="0d594-206">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d594-207">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="0d594-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0d594-208">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0d594-209">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="0d594-209">Register the authorization handlers</span></span>

<span data-ttu-id="0d594-210">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d594-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0d594-211">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d594-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0d594-212">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0d594-213">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0d594-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="0d594-214">`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d594-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0d594-215">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="0d594-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0d594-216">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="0d594-216">Support authorization</span></span>

<span data-ttu-id="0d594-217">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="0d594-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0d594-218">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="0d594-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="0d594-219">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="0d594-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0d594-220">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="0d594-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="0d594-221">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="0d594-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0d594-222">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="0d594-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0d594-223">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="0d594-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0d594-224">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0d594-224">The preceding code:</span></span>

* <span data-ttu-id="0d594-225">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="0d594-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0d594-226">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="0d594-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0d594-227">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="0d594-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0d594-228">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="0d594-228">Update the CreateModel</span></span>

<span data-ttu-id="0d594-229">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="0d594-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0d594-230">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0d594-231">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0d594-232">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="0d594-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0d594-233">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="0d594-233">Update the IndexModel</span></span>

<span data-ttu-id="0d594-234">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0d594-235">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0d594-235">Update the EditModel</span></span>

<span data-ttu-id="0d594-236">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0d594-237">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="0d594-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0d594-238">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="0d594-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0d594-239">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0d594-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0d594-240">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d594-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0d594-241">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0d594-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0d594-242">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="0d594-242">Update the DeleteModel</span></span>

<span data-ttu-id="0d594-243">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="0d594-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0d594-244">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="0d594-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="0d594-245">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="0d594-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0d594-246">Yetkilendirme hizmetini *Sayfalar/_ViewImports. cshtml* dosyasına ekleme, bu nedenle tüm görünümlerde kullanılabilir hale gelir:</span><span class="sxs-lookup"><span data-stu-id="0d594-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0d594-247">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="0d594-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0d594-248">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0d594-249">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0d594-250">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0d594-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0d594-251">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0d594-252">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d594-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0d594-253">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="0d594-253">Update Details</span></span>

<span data-ttu-id="0d594-254">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0d594-255">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0d594-256">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="0d594-256">Add or remove a user to a role</span></span>

<span data-ttu-id="0d594-257">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="0d594-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0d594-258">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="0d594-258">Removing privileges from a user.</span></span> <span data-ttu-id="0d594-259">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="0d594-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0d594-260">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="0d594-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="0d594-261">Challenge ve Fordeklarasyon arasındaki farklar</span><span class="sxs-lookup"><span data-stu-id="0d594-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="0d594-262">Bu uygulama, varsayılan ilkeyi [kimliği doğrulanmış kullanıcıları gerektirecek](#rau)şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="0d594-263">Aşağıdaki kod anonim kullanıcılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="0d594-263">The following code allows anonymous users.</span></span> <span data-ttu-id="0d594-264">Anonim kullanıcılara, çekişme ile Fordeklarasyon arasındaki farkları gösterme izni verilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="0d594-265">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="0d594-265">In the preceding code:</span></span>

* <span data-ttu-id="0d594-266">Kullanıcının kimliği **doğrulanmadığı** zaman, bir `ChallengeResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0d594-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="0d594-267">Bir `ChallengeResult` döndürüldüğünde, Kullanıcı oturum açma sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="0d594-268">Kullanıcının kimliği doğrulandığında, ancak yetkilendirilmediğinde, bir `ForbidResult` döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0d594-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="0d594-269">Bir `ForbidResult` döndürüldüğünde, Kullanıcı erişim reddedildi sayfasına yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0d594-270">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="0d594-270">Test the completed app</span></span>

<span data-ttu-id="0d594-271">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="0d594-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0d594-272">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d594-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0d594-273">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="0d594-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0d594-274">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="0d594-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="0d594-275">Uygulamanın kişileri varsa:</span><span class="sxs-lookup"><span data-stu-id="0d594-275">If the app has contacts:</span></span>

* <span data-ttu-id="0d594-276">Tablodaki tüm kayıtları silin `Contact` .</span><span class="sxs-lookup"><span data-stu-id="0d594-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="0d594-277">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="0d594-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="0d594-278">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="0d594-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0d594-279">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="0d594-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0d594-280">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="0d594-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0d594-281">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="0d594-281">Verify the following operations:</span></span>

* <span data-ttu-id="0d594-282">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0d594-283">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0d594-284">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0d594-285">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0d594-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0d594-286">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0d594-287">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="0d594-287">User</span></span>                | <span data-ttu-id="0d594-288">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="0d594-288">Seeded by the app</span></span> | <span data-ttu-id="0d594-289">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="0d594-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0d594-290">No</span><span class="sxs-lookup"><span data-stu-id="0d594-290">No</span></span>                | <span data-ttu-id="0d594-291">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="0d594-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0d594-292">Yes</span><span class="sxs-lookup"><span data-stu-id="0d594-292">Yes</span></span>               | <span data-ttu-id="0d594-293">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="0d594-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0d594-294">Yes</span><span class="sxs-lookup"><span data-stu-id="0d594-294">Yes</span></span>               | <span data-ttu-id="0d594-295">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="0d594-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0d594-296">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0d594-297">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="0d594-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0d594-298">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0d594-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0d594-299">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-299">Create the starter app</span></span>

* <span data-ttu-id="0d594-300">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0d594-301">**Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0d594-302">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0d594-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0d594-303">`-uld` SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="0d594-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0d594-304">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="0d594-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0d594-305">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="0d594-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0d594-306">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="0d594-307">Komutuyla bir hata yaşarsanız `dotnet aspnet-codegenerator razorpage` , [Bu GitHub sorununa](https://github.com/aspnet/Scaffolding/issues/984)bakın.</span><span class="sxs-lookup"><span data-stu-id="0d594-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="0d594-308">*Pages/Shared/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="0d594-309">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="0d594-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0d594-310">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="0d594-310">Seed the database</span></span>

<span data-ttu-id="0d594-311">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) sınıfını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0d594-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="0d594-312">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="0d594-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="0d594-313">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="0d594-313">Test that the app seeded the database.</span></span> <span data-ttu-id="0d594-314">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="0d594-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="0d594-315">Bu öğreticide, yetkilendirme tarafından korunan kullanıcı verileriyle ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0d594-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0d594-316">Kimliği doğrulanmış (kayıtlı) kullanıcıların oluşturduğu kişilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0d594-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0d594-317">Üç güvenlik grubu vardır:</span><span class="sxs-lookup"><span data-stu-id="0d594-317">There are three security groups:</span></span>

* <span data-ttu-id="0d594-318">**Kayıtlı kullanıcılar** tüm onaylanan verileri görüntüleyebilir ve kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0d594-319">**Yöneticiler** , kişi verilerini onaylayabilir veya reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0d594-320">Yalnızca onaylanan kişiler kullanıcılara görünür.</span><span class="sxs-lookup"><span data-stu-id="0d594-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0d594-321">**Yöneticiler** , verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0d594-322">Aşağıdaki görüntüde, User Rick ( `rick@example.com` ) oturum açtı.</span><span class="sxs-lookup"><span data-stu-id="0d594-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0d594-323">Rick, onaylanan kişileri görüntüleyebilir ve **Düzenle** / **Delete**' e / ait kişiler için**yeni bağlantılar oluştur** ' a bakın.</span><span class="sxs-lookup"><span data-stu-id="0d594-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0d594-324">Yalnızca Rick tarafından oluşturulan son kayıt, **Düzenle** ve **Sil** bağlantılarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0d594-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0d594-325">Yönetici veya yönetici durumu "Onaylandı" olarak değiştirene kadar diğer kullanıcılar son kaydı görmez.</span><span class="sxs-lookup"><span data-stu-id="0d594-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Rick oturum açmış olduğunu gösteren ekran görüntüsü](secure-data/_static/rick.png)

<span data-ttu-id="0d594-327">Aşağıdaki görüntüde, `manager@contoso.com` ve yöneticisinin rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="0d594-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü manager@contoso.com](secure-data/_static/manager1.png)

<span data-ttu-id="0d594-329">Aşağıdaki görüntüde, bir kişinin Yöneticiler Ayrıntılar görünümü gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="0d594-329">The following image shows the managers details view of a contact:</span></span>

![Yöneticinin bir kişinin görünümü](secure-data/_static/manager.png)

<span data-ttu-id="0d594-331">**Onaylama** ve **reddetme** düğmeleri yalnızca Yöneticiler ve yöneticiler için görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0d594-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0d594-332">Aşağıdaki görüntüde, `admin@contoso.com` ve yönetici rolünde oturum açıldı:</span><span class="sxs-lookup"><span data-stu-id="0d594-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Oturum açmış olduğunu gösteren ekran görüntüsü admin@contoso.com](secure-data/_static/admin.png)

<span data-ttu-id="0d594-334">Yöneticinin tüm ayrıcalıkları vardır.</span><span class="sxs-lookup"><span data-stu-id="0d594-334">The administrator has all privileges.</span></span> <span data-ttu-id="0d594-335">Herhangi bir kişiyi okuyabilir/düzenleyebilir/silebilir ve kişilerin durumunu değiştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0d594-336">Uygulama, aşağıdaki model için [Yapı iskelesi](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) tarafından oluşturulmuştur `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0d594-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0d594-337">Örnek, aşağıdaki yetkilendirme işleyicilerini içerir:</span><span class="sxs-lookup"><span data-stu-id="0d594-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0d594-338">`ContactIsOwnerAuthorizationHandler`: Bir kullanıcının yalnızca verilerini düzenleyebilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0d594-339">`ContactManagerAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0d594-340">`ContactAdministratorsAuthorizationHandler`: Yöneticilerin kişileri onaylamasını veya reddetmesini ve kişileri düzenlemesini/silmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d594-341">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0d594-341">Prerequisites</span></span>

<span data-ttu-id="0d594-342">Bu öğretici gelişmiş bir deyişle.</span><span class="sxs-lookup"><span data-stu-id="0d594-342">This tutorial is advanced.</span></span> <span data-ttu-id="0d594-343">Şunu tanımanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="0d594-343">You should be familiar with:</span></span>

* [<span data-ttu-id="0d594-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d594-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0d594-345">Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="0d594-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0d594-346">Hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="0d594-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0d594-347">Yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="0d594-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0d594-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0d594-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0d594-349">Başlatıcı ve tamamlanmış uygulama</span><span class="sxs-lookup"><span data-stu-id="0d594-349">The starter and completed app</span></span>

<span data-ttu-id="0d594-350">[Tamamlanmış](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) uygulamayı [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="0d594-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0d594-351">Tamamlanmış uygulamayı [Test](#test-the-completed-app) edin, böylece güvenlik özellikleri hakkında bilgi sahibi olun.</span><span class="sxs-lookup"><span data-stu-id="0d594-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0d594-352">Başlangıç uygulaması</span><span class="sxs-lookup"><span data-stu-id="0d594-352">The starter app</span></span>

<span data-ttu-id="0d594-353">[Başlangıç](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) uygulamasını [indirin](xref:index#how-to-download-a-sample) .</span><span class="sxs-lookup"><span data-stu-id="0d594-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0d594-354">Uygulamayı çalıştırın, **ContactManager** bağlantısına dokunun ve bir kişi oluşturup silemdiğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0d594-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0d594-355">Güvenli Kullanıcı verileri</span><span class="sxs-lookup"><span data-stu-id="0d594-355">Secure user data</span></span>

<span data-ttu-id="0d594-356">Aşağıdaki bölümlerde, güvenli Kullanıcı verileri uygulaması oluşturmak için tüm önemli adımlar verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0d594-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0d594-357">Tamamlanmış projeye başvurmak faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0d594-358">Kişi verilerini kullanıcıya bağlama</span><span class="sxs-lookup"><span data-stu-id="0d594-358">Tie the contact data to the user</span></span>

<span data-ttu-id="0d594-359">[Identity](xref:security/authentication/identity)Kullanıcıların verilerini düzenleyebilmeleri, ancak diğer kullanıcıların verilerini düzenleyebilmeleri için ASP.NET Kullanıcı kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d594-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0d594-360">`OwnerID`Model ekleyin `ContactStatus` `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0d594-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0d594-361">`OwnerID` kullanıcının `AspNetUser` veritabanındaki TABLODAKI kimliği [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="0d594-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0d594-362">Bu `Status` alan, bir kişinin genel kullanıcılar tarafından görüntülenebilir olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="0d594-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0d594-363">Yeni bir geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="0d594-364">Rol hizmetlerini Ekle Identity</span><span class="sxs-lookup"><span data-stu-id="0d594-364">Add Role services to Identity</span></span>

<span data-ttu-id="0d594-365">Rol hizmetleri eklemek için [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0d594-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="0d594-366">Kimliği doğrulanmış kullanıcılar iste</span><span class="sxs-lookup"><span data-stu-id="0d594-366">Require authenticated users</span></span>

<span data-ttu-id="0d594-367">Kullanıcıların kimliklerinin doğrulanmasını gerektirmek için varsayılan kimlik doğrulama ilkesini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="0d594-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="0d594-368">Bir Razor sayfada, denetleyicide veya eylem yöntemi düzeyinde kimlik doğrulamasından vazgeçebilirsiniz `[AllowAnonymous]` .</span><span class="sxs-lookup"><span data-stu-id="0d594-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="0d594-369">Varsayılan kimlik doğrulama ilkesini kullanıcıların kimliğinin doğrulanmasını gerektirecek şekilde ayarlamak, yeni eklenen Razor sayfaları ve denetleyicileri korur.</span><span class="sxs-lookup"><span data-stu-id="0d594-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0d594-370">Varsayılan olarak kimlik doğrulamanın gerekli olması, yeni denetleyicilere ve Razor sayfalarına bağlı olarak, özniteliğini dahil etmek için daha güvenlidir `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="0d594-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0d594-371">Anonim kullanıcıların, kayıt yaptırmadan önce site hakkında bilgi alması için dizine, hakkında ve Iletişim sayfalarına [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0d594-372">Test hesabını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0d594-372">Configure the test account</span></span>

<span data-ttu-id="0d594-373">`SeedData`Sınıfı iki hesap oluşturur: yönetici ve yönetici.</span><span class="sxs-lookup"><span data-stu-id="0d594-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0d594-374">Bu hesaplara bir parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets) kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d594-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0d594-375">Parolayı proje dizininden ayarlayın ( *program.cs*içeren dizin):</span><span class="sxs-lookup"><span data-stu-id="0d594-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0d594-376">Güçlü bir parola belirtilmemişse, çağrıldığında bir özel durum oluşturulur `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="0d594-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0d594-377">`Main`Sınama parolasını kullanacak şekilde güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="0d594-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0d594-378">Test hesapları oluşturma ve kişileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0d594-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0d594-379">`Initialize` `SeedData` Test hesapları oluşturmak için sınıfındaki yöntemi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0d594-380">Yönetici kullanıcı KIMLIĞINI ve kişilerini ekleyin `ContactStatus` .</span><span class="sxs-lookup"><span data-stu-id="0d594-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0d594-381">Kişilerden birini "gönderildi" ve bir "reddedildi" yapın.</span><span class="sxs-lookup"><span data-stu-id="0d594-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0d594-382">Kullanıcı KIMLIĞINI ve durumunu tüm kişilere ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0d594-383">Yalnızca bir kişi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="0d594-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0d594-384">Sahip, yönetici ve yönetici yetkilendirme işleyicileri oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0d594-385">Bir *Yetkilendirme* klasörü oluşturun ve içinde bir `ContactIsOwnerAuthorizationHandler` sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="0d594-386">`ContactIsOwnerAuthorizationHandler`Kullanıcının kaynağın sahibi olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="0d594-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0d594-387">`ContactIsOwnerAuthorizationHandler`Çağıran [bağlamı. ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)Kimliği doğrulanmış geçerli kullanıcı kişi sahibsiyse başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="0d594-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0d594-388">Yetkilendirme işleyicileri genellikle:</span><span class="sxs-lookup"><span data-stu-id="0d594-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="0d594-389">`context.Succeed`Gereksinimler karşılandığında döndürün.</span><span class="sxs-lookup"><span data-stu-id="0d594-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0d594-390">`Task.CompletedTask`Gereksinimler karşılanmazsa döndürün.</span><span class="sxs-lookup"><span data-stu-id="0d594-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0d594-391">`Task.CompletedTask` başarılı veya başarısız değil, &mdash; diğer yetkilendirme işleyicilerinin çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="0d594-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0d594-392">Açıkça başarısız olması gerekiyorsa, [bağlamı döndürün. Başarısız oldu](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="0d594-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0d594-393">Uygulama, iletişim sahiplerinin kendi verilerini düzenlemesine/silmesine/oluşturmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="0d594-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0d594-394">`ContactIsOwnerAuthorizationHandler` gereksinim parametresine geçirilen işlemi denetlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0d594-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0d594-395">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-395">Create a manager authorization handler</span></span>

<span data-ttu-id="0d594-396">`ContactManagerAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d594-397">`ContactManagerAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="0d594-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0d594-398">İçerik değişikliklerini yalnızca Yöneticiler onaylayabilir veya reddedebilir (yeni veya değiştirilmiş).</span><span class="sxs-lookup"><span data-stu-id="0d594-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0d594-399">Yönetici yetkilendirme işleyicisi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="0d594-400">`ContactAdministratorsAuthorizationHandler` *Yetkilendirme* klasöründe bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0d594-401">`ContactAdministratorsAuthorizationHandler`Kaynak üzerinde davranan kullanıcının bir yönetici olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="0d594-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0d594-402">Yönetici tüm işlemleri yapabilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0d594-403">Yetkilendirme işleyicilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="0d594-403">Register the authorization handlers</span></span>

<span data-ttu-id="0d594-404">Entity Framework Core kullanan hizmetlerin, [Addkapsamlıdır](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)kullanılarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) için kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d594-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0d594-405">, `ContactIsOwnerAuthorizationHandler` [Identity](xref:security/authentication/identity) Entity Framework Core oluşturulan ASP.NET Core kullanır.</span><span class="sxs-lookup"><span data-stu-id="0d594-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0d594-406">İşleyicileri hizmet koleksiyonuyla kaydedin, bu sayede `ContactsController` [bağımlılık ekleme](xref:fundamentals/dependency-injection)üzerinden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0d594-407">Aşağıdaki kodu sonuna ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0d594-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="0d594-408">`ContactAdministratorsAuthorizationHandler` ve `ContactManagerAuthorizationHandler` tekton olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="0d594-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0d594-409">Bu, EF kullanmayan ve gereken tüm bilgiler yöntemin parametresinde olduğundan tektonlar vardır `Context` `HandleRequirementAsync` .</span><span class="sxs-lookup"><span data-stu-id="0d594-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0d594-410">Destek yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="0d594-410">Support authorization</span></span>

<span data-ttu-id="0d594-411">Bu bölümde, Razor sayfaları güncelleştirir ve bir işlem gereksinimleri sınıfı eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="0d594-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0d594-412">İlgili kişi işlemleri gereksinimleri sınıfını gözden geçirin</span><span class="sxs-lookup"><span data-stu-id="0d594-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="0d594-413">Sınıfını gözden geçirin `ContactOperations` .</span><span class="sxs-lookup"><span data-stu-id="0d594-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0d594-414">Bu sınıf, uygulamanın desteklediği gereksinimleri içerir:</span><span class="sxs-lookup"><span data-stu-id="0d594-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="0d594-415">Kişiler sayfaları için temel sınıf oluşturma Razor</span><span class="sxs-lookup"><span data-stu-id="0d594-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0d594-416">Kişiler sayfalarında kullanılan hizmetleri içeren bir temel sınıf oluşturun Razor .</span><span class="sxs-lookup"><span data-stu-id="0d594-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0d594-417">Temel sınıf, başlatma kodunu bir konuma koyar:</span><span class="sxs-lookup"><span data-stu-id="0d594-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0d594-418">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0d594-418">The preceding code:</span></span>

* <span data-ttu-id="0d594-419">`IAuthorizationService`Yetkilendirme işleyicilerine erişim için hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="0d594-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0d594-420">Hizmeti ekler Identity `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="0d594-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0d594-421">Öğesini ekleyin `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="0d594-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0d594-422">CreateModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="0d594-422">Update the CreateModel</span></span>

<span data-ttu-id="0d594-423">Sayfa modeli oluşturma oluşturucusunu temel sınıfı kullanacak şekilde güncelleştirin `DI_BasePageModel` :</span><span class="sxs-lookup"><span data-stu-id="0d594-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0d594-424">Yöntemi şu `CreateModel.OnPostAsync` şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0d594-425">Kullanıcı KIMLIĞINI `Contact` modele ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0d594-426">Kullanıcının kişi oluşturma iznine sahip olduğunu doğrulamak için yetkilendirme işleyicisini çağırın.</span><span class="sxs-lookup"><span data-stu-id="0d594-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0d594-427">Indexmodel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="0d594-427">Update the IndexModel</span></span>

<span data-ttu-id="0d594-428">`OnGetAsync`Yöntemi yalnızca onaylanan kişilerin genel kullanıcılara gösterilmesi için güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0d594-429">EditModel güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0d594-429">Update the EditModel</span></span>

<span data-ttu-id="0d594-430">Kullanıcının kişiye ait olduğunu doğrulamak için bir yetkilendirme işleyicisi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0d594-431">Kaynak yetkilendirmesi doğrulandığında, `[Authorize]` öznitelik yeterli değildir.</span><span class="sxs-lookup"><span data-stu-id="0d594-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0d594-432">Öznitelikler değerlendirildiğinde uygulamanın kaynağa erişimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="0d594-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0d594-433">Kaynak tabanlı yetkilendirme sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0d594-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0d594-434">Uygulamanın kaynağa erişimi olduğunda, sayfa modeline yükleyerek veya işleyicinin kendisi içine yükleyerek denetimlerin gerçekleştirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d594-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0d594-435">Kaynak anahtarını geçirerek kaynağa sıkça erişirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0d594-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0d594-436">DeleteModel 'i Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="0d594-436">Update the DeleteModel</span></span>

<span data-ttu-id="0d594-437">Kullanıcının kişide silme iznine sahip olduğunu doğrulamak için, silme sayfası modelini yetkilendirme işleyicisini kullanacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="0d594-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0d594-438">Yetkilendirme hizmetini görünümlere ekleme</span><span class="sxs-lookup"><span data-stu-id="0d594-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="0d594-439">Şu anda kullanıcı ARABIRIMI, kullanıcının değiştiremeyeceğiniz kişiler için Düzenle ve Sil bağlantılarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="0d594-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0d594-440">Yetkilendirme hizmetini *Görünümler/_ViewImports. cshtml* dosyasında, tüm görünümlerde kullanılabilir olacak şekilde ekleme:</span><span class="sxs-lookup"><span data-stu-id="0d594-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0d594-441">Yukarıdaki biçimlendirme birkaç deyim ekliyor `using` .</span><span class="sxs-lookup"><span data-stu-id="0d594-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0d594-442">*Sayfalar/kişiler/Index. cshtml* 'deki **Düzenle** ve **Sil** bağlantılarını yalnızca uygun izinlere sahip kullanıcılar için işlendikleri şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0d594-443">Verileri değiştirme izni olmayan kullanıcıların bağlantılarının gizlenmesi, uygulamanın güvenliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0d594-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0d594-444">Bağlantıların gizlenmesi, yalnızca geçerli bağlantıları görüntüleyerek uygulamayı daha kolay hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0d594-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0d594-445">Kullanıcılar, sahip olmadıkları veriler üzerinde düzenleme ve silme işlemlerini çağırmak için oluşturulan URL 'Leri hacme edebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0d594-446">RazorSayfanın veya denetleyicinin, verilerin güvenliğini sağlamak için erişim denetimlerini zorlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0d594-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0d594-447">Güncelleştirme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="0d594-447">Update Details</span></span>

<span data-ttu-id="0d594-448">Yöneticilerin kişileri onaylamasını veya reddedebilmesi için Ayrıntılar görünümünü güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0d594-449">Ayrıntılar sayfası modelini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0d594-450">Bir role Kullanıcı ekleme veya rolü kaldırma</span><span class="sxs-lookup"><span data-stu-id="0d594-450">Add or remove a user to a role</span></span>

<span data-ttu-id="0d594-451">Hakkında bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8502) bakın:</span><span class="sxs-lookup"><span data-stu-id="0d594-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0d594-452">Bir kullanıcıdan ayrıcalıklar kaldırılıyor.</span><span class="sxs-lookup"><span data-stu-id="0d594-452">Removing privileges from a user.</span></span> <span data-ttu-id="0d594-453">Örneğin, bir sohbet uygulamasındaki kullanıcıyı kapatma.</span><span class="sxs-lookup"><span data-stu-id="0d594-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0d594-454">Bir kullanıcıya ayrıcalık ekleme.</span><span class="sxs-lookup"><span data-stu-id="0d594-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0d594-455">Tamamlanmış uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="0d594-455">Test the completed app</span></span>

<span data-ttu-id="0d594-456">Önceden oluşturulmuş kullanıcı hesapları için bir parola ayarlamadıysanız, parola ayarlamak için [gizli dizi Yöneticisi aracını](xref:security/app-secrets#secret-manager) kullanın:</span><span class="sxs-lookup"><span data-stu-id="0d594-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0d594-457">Güçlü bir parola seçin: sekiz veya daha fazla karakter, en az bir büyük harf karakter, sayı ve simge kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d594-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0d594-458">Örneğin, `Passw0rd!` güçlü parola gereksinimlerini karşılar.</span><span class="sxs-lookup"><span data-stu-id="0d594-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0d594-459">Aşağıdaki komutu projenin klasöründen çalıştırın, burada `<PW>` parola olur:</span><span class="sxs-lookup"><span data-stu-id="0d594-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="0d594-460">Veritabanını bırakma ve güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0d594-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="0d594-461">Veritabanını temel alarak uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="0d594-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="0d594-462">Tamamlanmış uygulamayı test etmenin kolay bir yolu da üç farklı tarayıcı (veya bilito/InPrivate oturumlar) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="0d594-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0d594-463">Tek bir tarayıcıda yeni bir Kullanıcı kaydedin (örneğin, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="0d594-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0d594-464">Her bir tarayıcıda farklı bir kullanıcıyla oturum açın.</span><span class="sxs-lookup"><span data-stu-id="0d594-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0d594-465">Aşağıdaki işlemleri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="0d594-465">Verify the following operations:</span></span>

* <span data-ttu-id="0d594-466">Kayıtlı kullanıcılar tüm onaylanan iletişim verilerini görüntüleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0d594-467">Kayıtlı kullanıcılar kendi verilerini düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0d594-468">Yöneticiler, kişi verilerini onaylayabilir/reddedebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0d594-469">`Details`Görünüm **Onayla** ve **Reddet** düğmelerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0d594-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0d594-470">Yöneticiler tüm verileri onaylayabilir/reddedebilir ve düzenleyebilir/silebilir.</span><span class="sxs-lookup"><span data-stu-id="0d594-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0d594-471">Kullanıcı</span><span class="sxs-lookup"><span data-stu-id="0d594-471">User</span></span>                | <span data-ttu-id="0d594-472">Uygulama tarafından sağlanan</span><span class="sxs-lookup"><span data-stu-id="0d594-472">Seeded by the app</span></span> | <span data-ttu-id="0d594-473">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="0d594-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0d594-474">No</span><span class="sxs-lookup"><span data-stu-id="0d594-474">No</span></span>                | <span data-ttu-id="0d594-475">Kendi verilerini düzenleyin/silin.</span><span class="sxs-lookup"><span data-stu-id="0d594-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0d594-476">Yes</span><span class="sxs-lookup"><span data-stu-id="0d594-476">Yes</span></span>               | <span data-ttu-id="0d594-477">Kendi verilerini onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="0d594-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0d594-478">Yes</span><span class="sxs-lookup"><span data-stu-id="0d594-478">Yes</span></span>               | <span data-ttu-id="0d594-479">Tüm verileri onaylama/reddetme ve düzenleme/silme.</span><span class="sxs-lookup"><span data-stu-id="0d594-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0d594-480">Yöneticinin tarayıcısında bir kişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0d594-481">Yönetici iletişim kutusundan silme ve düzenleme için URL 'YI kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="0d594-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0d594-482">Bu bağlantıları test kullanıcısının bu işlemleri gerçekleştiremediğinizi doğrulamak için test kullanıcısının tarayıcısına yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0d594-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0d594-483">Başlangıç uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-483">Create the starter app</span></span>

* <span data-ttu-id="0d594-484">Razor"ContactManager" adlı bir sayfalar uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0d594-485">**Ayrı kullanıcı hesaplarıyla**uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0d594-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0d594-486">Ad alanı örnekte kullanılan ad alanıyla eşleşecek şekilde "ContactManager" olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0d594-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0d594-487">`-uld` SQLite yerine LocalDB belirtir</span><span class="sxs-lookup"><span data-stu-id="0d594-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0d594-488">*Modeller/Ilgili kişi ekle. cs*:</span><span class="sxs-lookup"><span data-stu-id="0d594-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0d594-489">Modeli dolandırıcıdan katlayın `Contact` .</span><span class="sxs-lookup"><span data-stu-id="0d594-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0d594-490">İlk geçiş oluşturun ve veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="0d594-491">*Pages/_Layout. cshtml* dosyasındaki **ContactManager** bağlayıcısını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0d594-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="0d594-492">Kişiyi oluşturarak, düzenleyerek ve silerek uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="0d594-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0d594-493">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="0d594-493">Seed the database</span></span>

<span data-ttu-id="0d594-494">*Veri* klasörüne [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) sınıfını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0d594-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="0d594-495">`SeedData.Initialize`Şuradan arayın `Main` :</span><span class="sxs-lookup"><span data-stu-id="0d594-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="0d594-496">Uygulamanın veritabanını sunan test edin.</span><span class="sxs-lookup"><span data-stu-id="0d594-496">Test that the app seeded the database.</span></span> <span data-ttu-id="0d594-497">Kişi VERITABANıNDA herhangi bir satır varsa, çekirdek Yöntem çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="0d594-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="0d594-498">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0d594-498">Additional resources</span></span>

* [<span data-ttu-id="0d594-499">Azure App Service’te .NET Core ve SQL Veritabanı web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="0d594-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="0d594-500">[ASP.NET Core yetkilendirme Laboratuvarı](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="0d594-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="0d594-501">Bu laboratuvar, bu öğreticide sunulan güvenlik özellikleri hakkında daha fazla ayrıntıya gider.</span><span class="sxs-lookup"><span data-stu-id="0d594-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="0d594-502">Özel ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="0d594-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
