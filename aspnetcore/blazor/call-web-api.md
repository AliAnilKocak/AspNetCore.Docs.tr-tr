---
title: ASP.NET Core Blazor bir Web API 'SI çağırma
author: guardrex
description: Çıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapma dahil olmak üzere JSON yardımcıları kullanarak bir Blazor uygulamasından Web API 'SI çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660148"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor bir Web API 'SI çağırma

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) Apps, önceden yapılandırılmış bir `HttpClient` hizmetini kullanarak Web API 'lerini çağırır. Blazor JSON yardımcıları veya <xref:System.Net.Http.HttpRequestMessage>kullanarak JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçeneklerini içerebilen oluşturma istekleri. Blazor WebAssembly Apps 'teki `HttpClient` hizmeti, isteklerin kaynak sunucusuna geri getirilmesi için odaklanılmıştır. Bu konudaki kılavuz yalnızca WebAssembly uygulamalarına Blazor aittir.

[Blazor Server](xref:blazor/hosting-models#blazor-server) Apps, genellikle <xref:System.Net.Http.IHttpClientFactory>kullanılarak oluşturulan <xref:System.Net.Http.HttpClient> örneklerini kullanarak Web API 'lerini çağırır. Bu konudaki kılavuz Blazor sunucusu uygulamalarıyla ilgili değildir. Blazor Server uygulamaları geliştirirken, <xref:fundamentals/http-requests>' daki yönergeleri izleyin.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl Indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.

*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:

* Web API çağrısı (*Pages/CallWebAPI. Razor*)
* HTTP Isteği Sınayıcısı (*Bileşenler/HTTPRequestTester. Razor*)

## <a name="packages"></a>Paketler

*Deneysel* [Microsoft. aspnetcore.Blazorbaşvurun. Proje dosyasındaki HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet paketi. `Microsoft.AspNetCore.Blazor.HttpClient`, `HttpClient` ve [System. Text. JSON](https://www.nuget.org/packages/System.Text.Json/)tabanlıdır.

Kararlı bir API kullanmak için, [Newtonsoft. Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm)kullanan [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) paketini kullanın. `Microsoft.AspNet.WebApi.Client` ' de kararlı API kullanmak, bu konu başlığı altında açıklanan, deneysel `Microsoft.AspNetCore.Blazor.HttpClient` paketine özgü olan JSON yardımcıları sağlamaz.

## <a name="httpclient-and-json-helpers"></a>HttpClient ve JSON yardımcıları

Blazor WebAssembly uygulamasında, [HttpClient](xref:fundamentals/http-requests) , istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.

Blazor sunucusu uygulaması, varsayılan olarak bir `HttpClient` hizmeti içermez. [HttpClient Factory altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya `HttpClient` sağlayın.

`HttpClient` ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır. `HttpClient`, tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.

İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır. `@inject` yönergesini kullanarak bir `HttpClient` örneği ekleme:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler. Örnekler, aşağıdakileri depolayan bir `TodoItem` sınıfına dayalıdır:

* Öğenin benzersiz KIMLIĞI &ndash; KIMLIK (`Id`, `long`).
* Öğenin adı (`Name``string`) &ndash; adı.
* Durum (`IsComplete`, `bool`) Todo öğesi tamamlandığında gösterge &ndash;.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:

* `GetJsonAsync` &ndash; bir HTTP GET isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda `_todoItems` bileşen tarafından görüntülenir. `GetTodoItems` yöntemi, bileşen işlemeyi tamamladığında tetiklenir ([Onınitializedadsync](xref:blazor/lifecycle#component-initialization-methods)). Örnek uygulamaya bkz. örnek uygulama.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash;, JSON kodlu içerik dahil olmak üzere bir HTTP POST isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda, `_newItemName` bileşenin bağlantılı bir öğesi tarafından sağlanır. `AddItem` yöntemi bir `<button>` öğesi seçilerek tetiklenir. Örnek uygulamaya bkz. örnek uygulama.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* `PutJsonAsync` &ndash;, JSON kodlu içerik dahil olmak üzere bir HTTP PUT isteği gönderir.

  Aşağıdaki kodda, `Name` ve `IsCompleted` değerlerinin `_editItem`, bileşenin bağlantılı öğeleri tarafından sağlanır. Öğenin `Id`, öğe Kullanıcı arabiriminin başka bir bölümünde seçildiğinde ve `EditItem` çağrıldığında ayarlanır. `SaveItem` yöntemi, Kaydet `<button>` öğesi seçilerek tetiklenir. Örnek uygulamaya bkz. örnek uygulama.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http>, HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) , BIR Web API 'SINE http delete isteği göndermek için kullanılır.

Aşağıdaki kodda, DELETE `<button>` öğesi `DeleteItem` yöntemini çağırır. Bağlantılı `<input>` öğesi, silinecek öğenin `id` sağlar. Örnek uygulamaya bkz. örnek uygulama.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>Çıkış noktaları arası kaynak paylaşımı (CORS)

Tarayıcı güvenliği, bir Web sayfasının, Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller. Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir. Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller. Tarayıcıdan farklı bir kaynağa sahip bir uç noktaya istek yapmak için *uç noktanın* , [çıkış noktaları arası kaynak PAYLAŞıMı 'nı (CORS)](https://www.w3.org/TR/cors/)etkinleştirmesi gerekir.

[Blazor WebAssembly örnek uygulaması (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) , Web API bileşeninde CORS 'nin kullanımını gösterir (*Pages/callwebapi. Razor*).

Diğer sitelerin uygulamanıza çıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapmasına izin vermek için bkz. <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>API istek seçeneklerini getiren HttpClient ve HttpRequestMessage

Blazor WebAssembly uygulamasında WebAssembly üzerinde çalışırken, istekleri özelleştirmek için [HttpClient](xref:fundamentals/http-requests) ve <xref:System.Net.Http.HttpRequestMessage> kullanın. Örneğin, istek URI 'SI, HTTP yöntemi ve istenen istek üst bilgilerini belirtebilirsiniz.

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

API seçenekleri getirme hakkında daha fazla bilgi için bkz. [MDN Web belgeleri: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

CORS isteklerinde kimlik bilgileri (yetkilendirme tanımlama bilgileri/üstbilgileri) gönderilirken, CORS ilkesi tarafından `Authorization` üst bilgisine izin verilmelidir.

Aşağıdaki ilke için yapılandırma içerir:

* İstek kaynakları (`http://localhost:5000`, `https://localhost:5001`).
* Any yöntemi (fiil).
* `Content-Type` ve `Authorization` üst bilgileri. Özel bir üstbilgiye (örneğin, `x-custom-header`) izin vermek için, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>çağrılırken üstbilgiyi listeleyin.
* İstemci tarafı JavaScript kodu tarafından ayarlanan kimlik bilgileri (`credentials` Özellik `include`olarak ayarlanır).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Daha fazla bilgi için bkz. <xref:security/cors> ve örnek uygulamanın HTTP Isteği Sınayıcısı bileşeni (*Bileşenler/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/)
