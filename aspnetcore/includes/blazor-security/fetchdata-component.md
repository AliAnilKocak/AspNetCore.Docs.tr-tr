<span data-ttu-id="5f5e5-101">`FetchData` bileşen şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="5f5e5-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="5f5e5-102">Erişim belirteci sağlayın.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-102">Provision an access token.</span></span>
* <span data-ttu-id="5f5e5-103">*Sunucu* uygulamasında korumalı bir kaynak API 'si çağırmak için erişim belirtecini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="5f5e5-104">`@attribute [Authorize]` yönergesi, kullanıcının bu bileşeni ziyaret edebilmek için yetkilendirilmiş olması gereken Blazor WebAssembly yetkilendirme sistemine işaret ediyor.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="5f5e5-105">*İstemci* uygulamasındaki özniteliğin varlığı, sunucudaki API 'nin doğru kimlik bilgileri olmadan çağrılmasına engel olmaz.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="5f5e5-106">*Sunucu* uygulamasının aynı zamanda uygun uç noktalar üzerinde `[Authorize]` kullanması gerekir ve bunları doğru şekilde korur.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="5f5e5-107">`AuthenticationService.RequestAccessToken();`, API 'yi çağırmak için isteğe eklenebilen bir erişim belirteci isteme işlemini gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="5f5e5-108">Belirteç önbelleğe alınmışsa veya hizmet Kullanıcı etkileşimi olmadan yeni bir erişim belirteci sağlayabiliyor ise, belirteç isteği başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="5f5e5-109">Aksi takdirde, belirteç isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="5f5e5-110">İsteğe dahil edilecek gerçek belirteci almak için, uygulamanın `tokenResult.TryGetToken(out var token)`çağırarak isteğin başarılı olduğunu denetlemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="5f5e5-111">İstek başarılı olduysa, belirteç değişkeni erişim belirteciyle doldurulur.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="5f5e5-112">Belirtecin `Value` özelliği, `Authorization` istek başlığına dahil etmek için sabit dizeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="5f5e5-113">Belirteç Kullanıcı etkileşimi olmadan sağlanamadığından istek başarısız olduysa, belirteç sonucu bir yeniden yönlendirme URL 'SI içerir.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="5f5e5-114">Bu URL 'ye gidildiğinde, Kullanıcı oturum açma sayfasına geçer ve başarılı bir kimlik doğrulamasından sonra geçerli sayfaya geri dönün.</span><span class="sxs-lookup"><span data-stu-id="5f5e5-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
...
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="5f5e5-115">Daha fazla bilgi için bkz. [bir kimlik doğrulama işleminden önce uygulama durumunu kaydetme](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="5f5e5-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
