---
title: ASP.NET Blazor Çekirdek küreselleşme ve yerelleştirme
author: guardrex
description: Razor bileşenlerini birden çok kültür ve dilde kullanıcılar için nasıl erişilebilir hale getirebileceğimi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 0883a67e0129590f7a3fb68689eaba8d85e5523f
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440720"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="ee310-103">ASP.NET Blazor Çekirdek küreselleşme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="ee310-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="ee310-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ee310-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ee310-105">Jilet bileşenleri, birden fazla kültür ve dilde kullanıcılar tarafından erişilebilir hale getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="ee310-106">Aşağıdaki .NET küreselleşme ve yerelleştirme senaryoları kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="ee310-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="ee310-107">. NET'in kaynak sistemi</span><span class="sxs-lookup"><span data-stu-id="ee310-107">.NET's resources system</span></span>
* <span data-ttu-id="ee310-108">Kültüre özel sayı ve tarih biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="ee310-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="ee310-109">Sınırlı sayıda ASP.NET Core'un yerelleştirme senaryoları şu anda desteklenir:</span><span class="sxs-lookup"><span data-stu-id="ee310-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="ee310-110">`IStringLocalizer<>`uygulamalarda Blazor *desteklenir.*</span><span class="sxs-lookup"><span data-stu-id="ee310-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="ee310-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`ve Veri Ek Açıklamaları yerelleştirme ASP.NET Core MVC senaryoları ve uygulamalarda Blazor **desteklenmez.**</span><span class="sxs-lookup"><span data-stu-id="ee310-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="ee310-112">Daha fazla bilgi için bkz. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="ee310-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="ee310-113">Genelleştirme</span><span class="sxs-lookup"><span data-stu-id="ee310-113">Globalization</span></span>

Blazor<span data-ttu-id="ee310-114">'ın `@bind` işlevselliği, kullanıcının geçerli kültürüne göre görüntü için biçimleri ve ayrıştırıcı değerleri gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="ee310-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="ee310-115">Geçerli kültüre <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> tesisten erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="ee310-116">[CultureInfo.InvariantKültür](xref:System.Globalization.CultureInfo.InvariantCulture) aşağıdaki alan türleri için`<input type="{TYPE}" />`kullanılır ():</span><span class="sxs-lookup"><span data-stu-id="ee310-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="ee310-117">Önceki alan türleri:</span><span class="sxs-lookup"><span data-stu-id="ee310-117">The preceding field types:</span></span>

* <span data-ttu-id="ee310-118">Uygun tarayıcı tabanlı biçimlendirme kuralları kullanılarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ee310-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="ee310-119">Serbest biçimli metin içeremez.</span><span class="sxs-lookup"><span data-stu-id="ee310-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="ee310-120">Tarayıcının uygulamasına dayalı kullanıcı etkileşimi özellikleri sağlayın.</span><span class="sxs-lookup"><span data-stu-id="ee310-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="ee310-121">Aşağıdaki alan türleri belirli biçimlendirme gereksinimlerine sahiptir ve Blazor tüm büyük tarayıcılar tarafından desteklenmedikleri için şu anda tarafından desteklenmez:</span><span class="sxs-lookup"><span data-stu-id="ee310-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="ee310-122">`@bind`bir `@bind:culture` değerayrıştma <xref:System.Globalization.CultureInfo?displayProperty=fullName> ve biçimlendirme sağlamak için parametreyi destekler.</span><span class="sxs-lookup"><span data-stu-id="ee310-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="ee310-123">Alan türlerini kullanırken `date` `number` bir kültür belirtilmesi önerilmez.</span><span class="sxs-lookup"><span data-stu-id="ee310-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="ee310-124">`date`ve `number` gerekli kültürü Blazor sağlayan yerleşik desteğe sahip olmak.</span><span class="sxs-lookup"><span data-stu-id="ee310-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="ee310-125">Yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="ee310-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="ee310-126">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ee310-126"> WebAssembly</span></span>

<span data-ttu-id="ee310-127">Varsayılan olarak, WebAssembly uygulamaları Blazor için ''nin bağlayıcı yapılandırması, Blazoraçıkça istenen yerel durumlar dışında uluslararasılaştırma bilgilerini siler.</span><span class="sxs-lookup"><span data-stu-id="ee310-127">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ee310-128">Bağlayıcının davranışını denetleme hakkında daha fazla bilgi <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>ve kılavuz için bkz.</span><span class="sxs-lookup"><span data-stu-id="ee310-128">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<!-- HOLD FOR 3.2 PREVIEW 4: Replace prior paragraph with ...

Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.

By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested. For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale. For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.

-->

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="ee310-129">Sunucu</span><span class="sxs-lookup"><span data-stu-id="ee310-129"> Server</span></span>

Blazor<span data-ttu-id="ee310-130">Sunucu uygulamaları [Yerelleştirme Middleware](xref:fundamentals/localization#localization-middleware)kullanılarak yerelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-130"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="ee310-131">Ara yazılım, uygulamadan kaynak isteyen kullanıcılar için uygun kültürü seçer.</span><span class="sxs-lookup"><span data-stu-id="ee310-131">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="ee310-132">Kültür aşağıdaki yaklaşımlardan biri kullanılarak ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="ee310-132">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="ee310-133">Çerezler</span><span class="sxs-lookup"><span data-stu-id="ee310-133">Cookies</span></span>](#cookies)
* [<span data-ttu-id="ee310-134">Kültürü seçmek için UI sağlayın</span><span class="sxs-lookup"><span data-stu-id="ee310-134">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="ee310-135">Daha fazla bilgi ve <xref:fundamentals/localization>örnekler için bkz.</span><span class="sxs-lookup"><span data-stu-id="ee310-135">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="ee310-136">Tanımlama bilgileri</span><span class="sxs-lookup"><span data-stu-id="ee310-136">Cookies</span></span>

<span data-ttu-id="ee310-137">Bir yerelleştirme kültür çerezkullanıcının kültürünü devam edebilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-137">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="ee310-138">Çerez, uygulamanın `OnGet` ana sayfa *(Pages/Host.cshtml.cs)* yöntemiyle oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ee310-138">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="ee310-139">Yerelleştirme Middleware kullanıcının kültürünü ayarlamak için sonraki isteklerde çerez okur.</span><span class="sxs-lookup"><span data-stu-id="ee310-139">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="ee310-140">Çerez kullanımı, WebSocket bağlantısının kültürü doğru şekilde yaymasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="ee310-140">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="ee310-141">Yerelleştirme düzenleri URL yolunu veya sorgu dizesini temel alıyorsa, düzen WebSockets ile çalışamayabilir, bu nedenle kültürü devam ettiremeyebilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-141">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="ee310-142">Bu nedenle, bir yerelleştirme kültür çerez kullanımı önerilen bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="ee310-142">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="ee310-143">Kültür bir yerelleştirme çerezinde kalıcıysa, herhangi bir teknik bir kültür atamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-143">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="ee310-144">Uygulamanın sunucu tarafı ASP.NET Core için zaten kurulmuş bir yerelleştirme şeması varsa, uygulamanın varolan yerelleştirme altyapısını kullanmaya devam edin ve uygulamanın şeması içinde yerelleştirme kültür çerezini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="ee310-144">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="ee310-145">Aşağıdaki örnek, yerelleştirme middleware tarafından okunabilir bir çerez geçerli kültür ayarlamak için nasıl gösterir.</span><span class="sxs-lookup"><span data-stu-id="ee310-145">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="ee310-146">Blazor Server uygulamasında aşağıdaki içerikleri içeren bir *Pages/_Host.cshtml.cs* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ee310-146">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="ee310-147">Yerelleştirme, uygulama tarafından aşağıdaki olaylar dizisinde ele alınır:</span><span class="sxs-lookup"><span data-stu-id="ee310-147">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="ee310-148">Tarayıcı uygulamaya bir ilk HTTP isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="ee310-148">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="ee310-149">Kültür Yerelleştirme Middleware tarafından atanır.</span><span class="sxs-lookup"><span data-stu-id="ee310-149">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="ee310-150">`OnGet` *_Host.cshtml.cs'deki* yöntem, yanıtın bir parçası olarak bir çerezdeki kültürü devam eder.</span><span class="sxs-lookup"><span data-stu-id="ee310-150">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="ee310-151">Tarayıcı, etkileşimli Blazor bir Sunucu oturumu oluşturmak için bir WebSocket bağlantısı açar.</span><span class="sxs-lookup"><span data-stu-id="ee310-151">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="ee310-152">Yerelleştirme Middleware çerez okur ve kültür atar.</span><span class="sxs-lookup"><span data-stu-id="ee310-152">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="ee310-153">Blazor Sunucu oturumu doğru kültürle başlar.</span><span class="sxs-lookup"><span data-stu-id="ee310-153">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="ee310-154">Kültürü seçmek için UI sağlayın</span><span class="sxs-lookup"><span data-stu-id="ee310-154">Provide UI to choose the culture</span></span>

<span data-ttu-id="ee310-155">Kullanıcının bir kültür seçmesine izin verecek kullanıcı kullanıcı nın kullanıcı yı sağlaması için *yeniden yönlendirme tabanlı* bir yaklaşım önerilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-155">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="ee310-156">Bu işlem, bir kullanıcı güvenli bir kaynağa erişmeye çalıştığında bir web uygulamasında olana benzer.</span><span class="sxs-lookup"><span data-stu-id="ee310-156">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="ee310-157">Kullanıcı oturum açma sayfasına yönlendirilir ve daha sonra özgün kaynağa yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ee310-157">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="ee310-158">Uygulama, bir denetleyiciye yönlendirme yoluyla kullanıcının seçili kültürünü devam ettir.</span><span class="sxs-lookup"><span data-stu-id="ee310-158">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="ee310-159">Denetleyici, kullanıcının seçili kültürünü bir çerez olarak ayarlar ve kullanıcıyı orijinal URI'ye yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="ee310-159">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="ee310-160">Kullanıcının seçili kültürünü bir çerezde ayarlamak ve orijinal URI'ye yönlendirmeyi gerçekleştirmek için sunucuda bir HTTP bitiş noktası kurun:</span><span class="sxs-lookup"><span data-stu-id="ee310-160">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="ee310-161">Açık `LocalRedirect` yeniden yönlendirme saldırılarını önlemek için eylem sonucunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="ee310-161">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="ee310-162">Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="ee310-162">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="ee310-163">Aşağıdaki bileşen, kullanıcı bir kültür seçtiğinde ilk yeniden yönlendirmenin nasıl gerçekleştirilecekine bir örnek gösterir:</span><span class="sxs-lookup"><span data-stu-id="ee310-163">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="ee310-164">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ee310-164">Additional resources</span></span>

* <xref:fundamentals/localization>
