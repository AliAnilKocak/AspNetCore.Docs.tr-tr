---
title: ASP.NET Core Blazor webassembly 'ten BIR Web API 'si çağırma
author: guardrex
description: Çıkış noktaları arası kaynak paylaşımı (CORS) istekleri Blazor yapma dahil olmak üzere, JSON yardımcıları kullanarak webassembly uygulamasından BIR Web API 'si çağırmayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767154"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>ASP.NET Core bir Web API 'SI çağırmaBlazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Juan de la Cruz](https://github.com/juandelacruz23) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Webassembly uygulamaları önceden yapılandırılmış `HttpClient` bir hizmeti kullanarak Web API 'lerini çağırır. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) JSON yardımcıları veya ile birlikte <xref:System.Net.Http.HttpRequestMessage>JavaScript [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) seçenekleri içerebilen oluşturma istekleri. Blazor Webassembly Blazor Apps 'teki `HttpClient` hizmet, isteklerin kaynak sunucusuna geri getirilmesi üzerine odaklanır. Bu konudaki kılavuz yalnızca Blazor webassembly uygulamalarına aittir.

Sunucu uygulamaları, genellikle kullanılarak <xref:System.Net.Http.IHttpClientFactory>oluşturulan <xref:System.Net.Http.HttpClient> örnekleri kullanarak Web API 'lerini çağırır. [ Blazor ](xref:blazor/hosting-models#blazor-server) Bu konudaki kılavuz, Blazor sunucu uygulamalarıyla ilgili değildir. Sunucu uygulamaları Blazor geliştirirken, içindeki <xref:fundamentals/http-requests>yönergeleri izleyin.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([nasıl indirilir](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* uygulamasını seçin.

*BlazorWebAssemblySample* örnek uygulamasında aşağıdaki bileşenlere bakın:

* Web API çağrısı (*Pages/CallWebAPI. Razor*)
* HTTP Isteği Sınayıcısı (*Bileşenler/HTTPRequestTester. Razor*)

## <a name="packages"></a>Paketler

Proje dosyasındaki [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet paketine başvurun.

## <a name="add-the-httpclient-service"></a>HttpClient hizmetini ekleme

İçinde `Program.Main`, zaten yoksa `HttpClient` bir hizmet ekleyin:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient ve JSON yardımcıları

Blazor Webassembly uygulamasında, [HttpClient](xref:fundamentals/http-requests) , istekleri kaynak sunucuya geri getirmek için önceden yapılandırılmış bir hizmet olarak kullanılabilir.

Blazor Sunucu uygulaması varsayılan olarak bir `HttpClient` hizmet içermez. `HttpClient` [HttpClient Factory altyapısını](xref:fundamentals/http-requests)kullanarak uygulamaya bir uygulama sağlayın.

`HttpClient`ve JSON yardımcıları, üçüncü taraf Web API uç noktalarını çağırmak için de kullanılır. `HttpClient`, tarayıcı [getirme API 'si](https://developer.mozilla.org/docs/Web/API/Fetch_API) kullanılarak uygulanır ve aynı kaynak ilkesini zorlama dahil olmak üzere sınırlamalarına tabidir.

İstemcinin temel adresi, kaynak sunucunun adresine ayarlanır. `@inject` Yönergesini kullanarak bir `HttpClient` örnek ekleme:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Aşağıdaki örneklerde, bir Todo Web API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işler. Örnekler şunları depolayan bir `TodoItem` sınıfa dayalıdır:

* Öğenin kimliği`Id`( `long`, &ndash; ) benzersiz kimliği.
* Öğenin adı`Name`( `string`, &ndash; ).
* Todo öğesi`IsComplete`tamamlandığında `bool`durum &ndash; (,) göstergesi.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON yardımcı yöntemleri bir URI 'ye (aşağıdaki örneklerde bir Web API 'si) istek gönderir ve yanıtı işler:

* `GetFromJsonAsync`&ndash; BIR http get isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda,, `_todoItems` bileşeni tarafından görüntülenir. `GetTodoItems` Yöntemi, bileşen işlemeyi tamamladığında tetiklenir ([Onınitializedadsync](xref:blazor/lifecycle#component-initialization-methods)). Örnek uygulamaya bkz. örnek uygulama.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; JSON kodlu içerik dahil olmak üzere BIR http post isteği gönderir ve bir nesne oluşturmak için JSON yanıt gövdesini ayrıştırır.

  Aşağıdaki kodda, `_newItemName` bileşenin bağlantılı bir öğesi tarafından sağlanır. `AddItem` Yöntemi bir `<button>` öğesi seçilerek tetiklenir. Örnek uygulamaya bkz. örnek uygulama.

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
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  `PostAsJsonAsync` Döndürecek çağrılar <xref:System.Net.Http.HttpResponseMessage>. Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; JSON kodlu içerik dahil olmak üzere BIR http put isteği gönderir.

  Aşağıdaki kodda, `_editItem` `Name` ve `IsCompleted` değerleri bileşenin bağlantılı öğelerine göre sağlanır. `Id` Öğe, Kullanıcı arabiriminin başka bir bölümünde seçildiğinde ayarlanır ve `EditItem` çağrılır. `SaveItem` Yöntemi, Kaydet `<button>` öğesi seçilerek tetiklenir. Örnek uygulamaya bkz. örnek uygulama.

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
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  `PutAsJsonAsync` Döndürecek çağrılar <xref:System.Net.Http.HttpResponseMessage>. Yanıt iletisinden JSON içeriğinin serisini kaldırmak için, `ReadFromJsonAsync<T>` genişletme yöntemini kullanın:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>HTTP istekleri göndermeye ve HTTP yanıtlarını almaya yönelik ek uzantı yöntemleri içerir. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) , BIR Web API 'SINE http delete isteği göndermek için kullanılır.

Aşağıdaki kodda, Delete `<button>` öğesi `DeleteItem` yöntemini çağırır. Bağlantılı `<input>` öğe, silinecek öğenin `id` bir listesini sağlar. Örnek uygulamaya bkz. örnek uygulama.

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

[Webassembly örnek uygulaması (BlazorWebAssemblySample), Web API bileşeninde CORS 'nin kullanımını gösterir (Pages/callwebapi. Razor). Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) *Pages/CallWebAPI.razor*

Diğer sitelerin uygulamanıza çıkış noktaları arası kaynak paylaşımı (CORS) istekleri yapmasına izin vermek için bkz <xref:security/cors>..

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C üzerinde çapraz kaynak kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/)
