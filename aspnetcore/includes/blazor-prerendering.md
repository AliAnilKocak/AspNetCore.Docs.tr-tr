<span data-ttu-id="0c472-101">Blazor sunucu uygulaması prerendering olduğunda, tarayıcıyla bir bağlantı kurulmadığından, JavaScript 'e çağırma gibi bazı eylemler mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="0c472-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="0c472-102">Bileşenler, ön işlenmiş olduğunda farklı şekilde işlenmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="0c472-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="0c472-103">Tarayıcı bağlantısı kurulana kadar JavaScript birlikte çalışma çağrılarını geciktirmek için `OnAfterRenderAsync` bileşen yaşam döngüsü olayını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0c472-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="0c472-104">Bu olay yalnızca uygulama tam olarak işlendikten ve istemci bağlantısı kurulduktan sonra çağırılır.</span><span class="sxs-lookup"><span data-stu-id="0c472-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            JSRuntime.InvokeVoidAsync(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

<span data-ttu-id="0c472-105">Aşağıdaki bileşen, prerendering ile uyumlu bir şekilde bileşenin başlatma mantığının bir parçası olarak JavaScript birlikte çalışabilirinin nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="0c472-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="0c472-106">Bileşeni, `OnAfterRenderAsync` içinden bir işleme güncelleştirmesi tetiklemenin mümkün olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="0c472-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="0c472-107">Geliştirici Bu senaryoda sonsuz bir döngü oluşturmaktan kaçınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0c472-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="0c472-108">@No__t_0 çağrıldığında, bileşen işlenene kadar hiçbir JavaScript öğesi olmadığından, `ElementRef` yalnızca `OnAfterRenderAsync` için kullanılır ve daha önceki bir yaşam döngüsü yönteminde değil.</span><span class="sxs-lookup"><span data-stu-id="0c472-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="0c472-109">`StateHasChanged`, bileşeni JavaScript birlikte çalışma çağrısından alınan yeni durumla yeniden eklemek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0c472-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="0c472-110">@No__t_0 yalnızca `infoFromJs` `null` olduğunda çağrıldığı için, kod sonsuz bir döngü oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="0c472-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```
