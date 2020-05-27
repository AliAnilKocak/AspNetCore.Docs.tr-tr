---
<span data-ttu-id="451b9-101">title: ' ASP.NET Core webassembly öğesinden bir Web API 'SI çağırma Blazor "Yazar: ' Blazor çıkış noktaları arası kaynak PAYLAŞıMı (CORS) istekleri yapma da dahil olmak üzere JSON yardımcıları kullanarak webassembly uygulamasından BIR Web API 'Si çağırmayı öğrenin. '</span><span class="sxs-lookup"><span data-stu-id="451b9-101">title: 'Call a web API from ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to call a web API from a Blazor WebAssembly app using JSON helpers, including making cross-origin resource sharing (CORS) requests.'</span></span>
<span data-ttu-id="451b9-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="451b9-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="451b9-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="451b9-103">'Blazor'</span></span>
- <span data-ttu-id="451b9-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="451b9-104">'Identity'</span></span>
- <span data-ttu-id="451b9-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="451b9-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="451b9-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="451b9-106">'Razor'</span></span>
- <span data-ttu-id="451b9-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="451b9-107">'SignalR' uid:</span></span> 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="451b9-108">ASP.NET Core bir Web API 'SI çağırmaBlazor</span><span class="sxs-lookup"><span data-stu-id="451b9-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="451b9-109">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından</span><span class="sxs-lookup"><span data-stu-id="451b9-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

<span data-ttu-id="451b9-110">[ Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) uygulamaları önceden yapılandırılmış bir hizmeti kullanarak Web API 'lerini çağırır <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="451b9-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="451b9-111">JSON yardımcıları veya ile birlikte JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçenekleri içerebilen oluşturma istekleri Blazor <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="451b9-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="451b9-112"><xref:System.Net.Http.HttpClient> Blazor Webassembly Apps 'teki hizmet, isteklerin kaynak sunucusuna geri getirilmesi üzerine odaklanır.</span><span class="sxs-lookup"><span data-stu-id="451b9-112">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="451b9-113">Bu konudaki kılavuz yalnızca Blazor webassembly uygulamalarına aittir.</span><span class="sxs-lookup"><span data-stu-id="451b9-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="451b9-114">[ Blazor Sunucu](xref:blazor/hosting-models#blazor-server) uygulamaları <xref:System.Net.Http.HttpClient> , genellikle kullanılarak oluşturulan örnekleri kullanarak Web API 'lerini çağırır <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="451b9-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="451b9-115">Bu konudaki kılavuz, sunucu uygulamalarıyla ilgili değildir Blazor .</span><span class="sxs-lookup"><span data-stu-id="451b9-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="451b9-116">BlazorSunucu uygulamaları geliştirirken, içindeki yönergeleri izleyin <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="451b9-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="451b9-117">[Örnek kodu görüntüle veya indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl Indirilir](xref:index#how-to-download-a-sample)): *BlazorWebAssemblySample* uygulamasını seçin.</span><span class="sxs-lookup"><span data-stu-id="451b9-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="451b9-118">*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:</span><span class="sxs-lookup"><span data-stu-id="451b9-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="451b9-119">Web API çağrısı (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="451b9-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="451b9-120">HTTP Isteği Sınayıcısı (*Bileşenler/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="451b9-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="451b9-121">Paketler</span><span class="sxs-lookup"><span data-stu-id="451b9-121">Packages</span></span>

<span data-ttu-id="451b9-122">Proje dosyasındaki [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="451b9-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="451b9-123">HttpClient hizmetini ekleme</span><span class="sxs-lookup"><span data-stu-id="451b9-123">Add the HttpClient service</span></span>

<span data-ttu-id="451b9-124">İçinde `Program.Main` , <xref:System.Net.Http.HttpClient> zaten yoksa bir hizmet ekleyin:</span><span class="sxs-lookup"><span data-stu-id="451b9-124">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="451b9-125">HttpClient ve JSON yardımcıları</span><span class="sxs-lookup"><span data-stu-id="451b9-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="451b9-126">BlazorWebassembly uygulamasında, [HttpClient](xref:fundamentals/http-requests) , istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="451b9-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="451b9-127">BlazorSunucu uygulaması <xref:System.Net.Http.HttpClient> Varsayılan olarak bir hizmet içermez.</span><span class="sxs-lookup"><span data-stu-id="451b9-127">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="451b9-128"><xref:System.Net.Http.HttpClient> [HttpClient Factory altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir uygulama sağlayın.</span><span class="sxs-lookup"><span data-stu-id="451b9-128">Provide an <xref:System.Net.Http.HttpClient> to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="451b9-129"><xref:System.Net.Http.HttpClient>ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="451b9-129"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="451b9-130"><xref:System.Net.Http.HttpClient>, tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.</span><span class="sxs-lookup"><span data-stu-id="451b9-130"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="451b9-131">İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="451b9-131">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="451b9-132"><xref:System.Net.Http.HttpClient>Yönergesini kullanarak bir örnek ekleme [`@inject`](xref:mvc/views/razor#inject) :</span><span class="sxs-lookup"><span data-stu-id="451b9-132">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="451b9-133">Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler.</span><span class="sxs-lookup"><span data-stu-id="451b9-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="451b9-134">Örnekler `TodoItem` şunları depolayan bir sınıfa dayalıdır:</span><span class="sxs-lookup"><span data-stu-id="451b9-134">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="451b9-135">Öğenin kimliği ( `Id` , `long` ) &ndash; benzersiz kimliği.</span><span class="sxs-lookup"><span data-stu-id="451b9-135">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="451b9-136">Öğenin adı ( `Name` , `string` ) &ndash; .</span><span class="sxs-lookup"><span data-stu-id="451b9-136">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="451b9-137">`IsComplete` `bool` Todo öğesi tamamlandığında durum (,) &ndash; göstergesi.</span><span class="sxs-lookup"><span data-stu-id="451b9-137">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="451b9-138">JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:</span><span class="sxs-lookup"><span data-stu-id="451b9-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="451b9-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>&ndash;BIR http get isteği gönderir ve bir nesne oluşturmak IÇIN JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="451b9-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="451b9-140">Aşağıdaki kodda,, `todoItems` bileşeni tarafından görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="451b9-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="451b9-141">`GetTodoItems`Yöntemi, bileşen işlemeyi tamamladığında tetiklenir ([Onınitializedadsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="451b9-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="451b9-142">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="451b9-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="451b9-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>&ndash;JSON kodlu içerik dahil olmak üzere BIR http post isteği gönderir ve bir nesne oluşturmak IÇIN JSON yanıt gövdesini ayrıştırır.</span><span class="sxs-lookup"><span data-stu-id="451b9-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="451b9-144">Aşağıdaki kodda, `newItemName` bileşenin bağlantılı bir öğesi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="451b9-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="451b9-145">`AddItem`Yöntemi bir öğesi seçilerek tetiklenir `<button>` .</span><span class="sxs-lookup"><span data-stu-id="451b9-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="451b9-146">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="451b9-146">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="451b9-147">Döndürecek çağrılar <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="451b9-147">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="451b9-148">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="451b9-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="451b9-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>&ndash;JSON kodlu içerik dahil olmak üzere BIR http put isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="451b9-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="451b9-150">Aşağıdaki kodda, `editItem` `Name` ve değerleri `IsCompleted` bileşenin bağlantılı öğelerine göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="451b9-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="451b9-151">Öğe, `Id` Kullanıcı arabiriminin başka bir bölümünde seçildiğinde ayarlanır ve `EditItem` çağrılır.</span><span class="sxs-lookup"><span data-stu-id="451b9-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="451b9-152">`SaveItem`Yöntemi, Kaydet öğesi seçilerek tetiklenir `<button>` .</span><span class="sxs-lookup"><span data-stu-id="451b9-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="451b9-153">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="451b9-153">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="451b9-154">Döndürecek çağrılar <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="451b9-154">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="451b9-155">Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> genişletme yöntemini kullanın:</span><span class="sxs-lookup"><span data-stu-id="451b9-155">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="451b9-156"><xref:System.Net.Http>HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="451b9-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="451b9-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>bir Web API 'sine HTTP DELETE isteği göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="451b9-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="451b9-158">Aşağıdaki kodda, Delete `<button>` öğesi `DeleteItem` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="451b9-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="451b9-159">Bağlantılı `<input>` öğe, `id` Silinecek öğenin bir listesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="451b9-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="451b9-160">Örnek uygulamaya bkz. örnek uygulama.</span><span class="sxs-lookup"><span data-stu-id="451b9-160">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="451b9-161">Ihttpclientfactory ile adlandırılmış HttpClient</span><span class="sxs-lookup"><span data-stu-id="451b9-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="451b9-162"><xref:System.Net.Http.IHttpClientFactory>Hizmetler ve bir adlandırılmış yapılandırma <xref:System.Net.Http.HttpClient> desteklenir.</span><span class="sxs-lookup"><span data-stu-id="451b9-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="451b9-163">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="451b9-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="451b9-164">`FetchData`bileşen (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="451b9-164">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="451b9-165">Yazılan HttpClient</span><span class="sxs-lookup"><span data-stu-id="451b9-165">Typed HttpClient</span></span>

<span data-ttu-id="451b9-166">Türü <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.HttpClient> bir veya daha fazla Web API uç noktasından veri döndürmek için, bir veya daha fazla uygulamanın örneklerinden, varsayılan veya adlandırılmış bir veya daha fazlasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="451b9-166">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="451b9-167">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="451b9-167">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="451b9-168">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="451b9-168">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="451b9-169">Bileşenler, <xref:System.Net.Http.HttpClient> Web API 'sini çağırmak için türü ekler.</span><span class="sxs-lookup"><span data-stu-id="451b9-169">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="451b9-170">`FetchData`bileşen (*Pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="451b9-170">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="451b9-171">Hataları işleme</span><span class="sxs-lookup"><span data-stu-id="451b9-171">Handle errors</span></span>

<span data-ttu-id="451b9-172">Bir Web API 'SI ile etkileşirken hatalar oluştuğunda, geliştirici kodu tarafından işlenebilir.</span><span class="sxs-lookup"><span data-stu-id="451b9-172">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="451b9-173">Örneğin, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> ile sunucu API 'sinden BIR JSON yanıtı bekler `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="451b9-173">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="451b9-174">Yanıt JSON biçiminde değilse, içerik doğrulaması bir oluşturur <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="451b9-174">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="451b9-175">Aşağıdaki örnekte, hava durumu tahmin verileri isteği için URI uç noktası yanlış yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="451b9-175">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="451b9-176">URI 'nin olması gerekir `WeatherForecast` ancak çağrıda `WeatherForcast` (eksik "e") görünmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="451b9-176">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="451b9-177"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Çağrı JSON 'ın döndürülmesini bekler, ancak sunucu, ' a sahip sunucudaki işlenmeyen bir özel durum IÇIN HTML döndürür `Content-Type` `text/html` .</span><span class="sxs-lookup"><span data-stu-id="451b9-177">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="451b9-178">Yol bulunamadığı ve ara yazılım, istek için bir sayfa veya görünüm hizmeti veremediği için sunucuda işlenmeyen özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="451b9-178">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="451b9-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>İstemcisinde, <xref:System.NotSupportedException> yanıt içeriği JSON olmayan olarak doğrulandığında oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="451b9-179">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="451b9-180">Özel `catch` mantık hatayı günlüğe kaydetmek veya kullanıcıya kolay bir hata iletisi sunmak üzere bloğunda özel durum yakalanır:</span><span class="sxs-lookup"><span data-stu-id="451b9-180">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="451b9-181">Yukarıdaki örnek tanıtım amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="451b9-181">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="451b9-182">Bir Web API sunucu uygulaması, bir uç nokta mevcut olmadığında veya sunucu üzerinde işlenmeyen bir durum oluştuğunda bile JSON döndürecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="451b9-182">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="451b9-183">Daha fazla bilgi için bkz. <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="451b9-183">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="451b9-184">Çıkış noktaları arası kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="451b9-184">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="451b9-185">Tarayıcı güvenliği, bir Web sayfasının, Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="451b9-185">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="451b9-186">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="451b9-186">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="451b9-187">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="451b9-187">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="451b9-188">Tarayıcıdan farklı bir kaynağa sahip bir uç noktaya istek yapmak için *uç noktanın* , [çıkış noktaları arası kaynak PAYLAŞıMı 'nı (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="451b9-188">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="451b9-189">[ Blazor Webassembly örnek uygulaması (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , Web API bileşeninde CORS 'nin kullanımını gösterir (*Pages/callwebapi. Razor*).</span><span class="sxs-lookup"><span data-stu-id="451b9-189">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="451b9-190">Diğer sitelerin uygulamanıza çıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapmasına izin vermek için bkz <xref:security/cors> ..</span><span class="sxs-lookup"><span data-stu-id="451b9-190">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="451b9-191">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="451b9-191">Additional resources</span></span>

* <span data-ttu-id="451b9-192"><xref:security/blazor/webassembly/additional-scenarios>&ndash; <xref:System.Net.Http.HttpClient> GÜVENLI Web API istekleri yapmak için kullanımı ile ilgili kapsamı içerir.</span><span class="sxs-lookup"><span data-stu-id="451b9-192"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="451b9-193">Kestrel HTTPS uç noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="451b9-193">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="451b9-194">W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="451b9-194">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
