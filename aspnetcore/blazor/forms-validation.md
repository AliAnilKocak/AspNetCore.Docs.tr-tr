---
title: BlazorForms ve doğrulama ASP.NET Core
author: guardrex
description: İçindeki form ve alan doğrulama senaryolarını nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: b57e2a34f79691f7f2b1ed69cfad25de00c5ca13
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176215"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="95d92-103">BlazorForms ve doğrulama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95d92-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="95d92-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="95d92-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="95d92-105">Formlar ve doğrulama, Blazor [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="95d92-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="95d92-106">Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="95d92-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="95d92-107">Bir form, bileşeni kullanılarak tanımlanır <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="95d92-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="95d92-108">Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodu gösterir:</span><span class="sxs-lookup"><span data-stu-id="95d92-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="95d92-109">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="95d92-109">In the preceding example:</span></span>

* <span data-ttu-id="95d92-110">Form, `name` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular `ExampleModel` .</span><span class="sxs-lookup"><span data-stu-id="95d92-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="95d92-111">Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda ( `exampleModel` ) tutulur.</span><span class="sxs-lookup"><span data-stu-id="95d92-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="95d92-112">Alanı, `Model` öğesinin özniteliğine atanır `<EditForm>` .</span><span class="sxs-lookup"><span data-stu-id="95d92-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="95d92-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Bileşenin `@bind-Value` bağlamaları:</span><span class="sxs-lookup"><span data-stu-id="95d92-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="95d92-114">Model özelliği ( `exampleModel.Name` ) <xref:Microsoft.AspNetCore.Components.Forms.InputText> bileşen `Value` özelliğine.</span><span class="sxs-lookup"><span data-stu-id="95d92-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="95d92-115">Özellik bağlama hakkında daha fazla bilgi için bkz <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> ..</span><span class="sxs-lookup"><span data-stu-id="95d92-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="95d92-116">Bileşen özelliğine bir değişiklik olayı temsilcisi <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="95d92-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="95d92-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, veri ek açıklamalarını kullanarak doğrulama desteği ekler.</span><span class="sxs-lookup"><span data-stu-id="95d92-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="95d92-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen doğrulama iletilerini özetler.</span><span class="sxs-lookup"><span data-stu-id="95d92-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="95d92-119">`HandleValidSubmit`Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).</span><span class="sxs-lookup"><span data-stu-id="95d92-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="95d92-120">Yerleşik Forms bileşenleri</span><span class="sxs-lookup"><span data-stu-id="95d92-120">Built-in forms components</span></span>

<span data-ttu-id="95d92-121">Kullanıcı girişini almak ve doğrulamak için yerleşik bir giriş bileşenleri kümesi vardır.</span><span class="sxs-lookup"><span data-stu-id="95d92-121">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="95d92-122">Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır.</span><span class="sxs-lookup"><span data-stu-id="95d92-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="95d92-123">Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="95d92-123">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="95d92-124">Giriş bileşeni</span><span class="sxs-lookup"><span data-stu-id="95d92-124">Input component</span></span> | <span data-ttu-id="95d92-125">Olarak işlendi&hellip;</span><span class="sxs-lookup"><span data-stu-id="95d92-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="95d92-126">Dahil olmak üzere tüm giriş bileşenleri, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="95d92-126">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="95d92-127">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="95d92-127">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="95d92-128">Giriş bileşenleri, düzenleme sırasında doğrulamak ve CSS sınıfını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranışı sağlar.</span><span class="sxs-lookup"><span data-stu-id="95d92-128">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="95d92-129">Bazı bileşenler, yararlı ayrıştırma mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="95d92-129">Some components include useful parsing logic.</span></span> <span data-ttu-id="95d92-130">Örneğin, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> bunları doğrulama hatası olarak kaydederek düzeltilemez değerleri düzgün şekilde işleyin.</span><span class="sxs-lookup"><span data-stu-id="95d92-130">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="95d92-131">Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="95d92-131">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="95d92-132">Aşağıdaki tür, daha `Starship` önce daha büyük bir özellik kümesi ve daha önceki veri açıklamalarını kullanarak doğrulama mantığını tanımlar `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="95d92-132">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="95d92-133">Önceki örnekte, `Description` hiçbir veri ek açıklaması mevcut olmadığından isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="95d92-133">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="95d92-134">Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular `Starship` :</span><span class="sxs-lookup"><span data-stu-id="95d92-134">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="95d92-135">, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Hangi alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components/cascading-values-and-parameters) olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95d92-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="95d92-136"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Ayrıca geçerli ve geçersiz Gönderimlerle (,) uygun olaylar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> sağlar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> .</span><span class="sxs-lookup"><span data-stu-id="95d92-136">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="95d92-137">Alternatif olarak, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> doğrulamayı tetiklemek ve alan değerlerini özel doğrulama kodu ile denetlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="95d92-137">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="95d92-138">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="95d92-138">In the following example:</span></span>

* <span data-ttu-id="95d92-139">`HandleSubmit`Yöntemi **`Submit`** Düğme seçildiğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="95d92-139">The `HandleSubmit` method runs when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="95d92-140">Form, formun ' i kullanılarak onaylanır <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="95d92-140">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="95d92-141">Form, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` sunucusunda BIR Web API uç noktası çağıran yönteme geçerek daha sonra onaylanır (*gösterilmez*).</span><span class="sxs-lookup"><span data-stu-id="95d92-141">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="95d92-142">Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı olarak çalıştırılır `isValid` .</span><span class="sxs-lookup"><span data-stu-id="95d92-142">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="95d92-143">Giriş olayına göre InputText</span><span class="sxs-lookup"><span data-stu-id="95d92-143">InputText based on the input event</span></span>

<span data-ttu-id="95d92-144"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Olayı yerine olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın `input` `change` .</span><span class="sxs-lookup"><span data-stu-id="95d92-144">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="95d92-145">Aşağıdaki örnekte, `CustomInputText` bileşen Framework `InputText` bileşenini devralır ve olay bağlamayı ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) `oninput` olaya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="95d92-145">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="95d92-146">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="95d92-146">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="95d92-147">`CustomInputText`Bileşen her yerde kullanılabilir <xref:Microsoft.AspNetCore.Components.Forms.InputText> :</span><span class="sxs-lookup"><span data-stu-id="95d92-147">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="95d92-148">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="95d92-148">`Pages/TestForm.razor`:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="95d92-149">Radyo düğmeleri</span><span class="sxs-lookup"><span data-stu-id="95d92-149">Radio buttons</span></span>

<span data-ttu-id="95d92-150">Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir.</span><span class="sxs-lookup"><span data-stu-id="95d92-150">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="95d92-151">Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir.</span><span class="sxs-lookup"><span data-stu-id="95d92-151">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="95d92-152">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="95d92-152">The following example shows how to:</span></span>

* <span data-ttu-id="95d92-153">Radyo düğmesi grubu için veri bağlamayı işleyin.</span><span class="sxs-lookup"><span data-stu-id="95d92-153">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="95d92-154">Özel bir bileşen kullanarak doğrulamayı destekler `InputRadio` .</span><span class="sxs-lookup"><span data-stu-id="95d92-154">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="95d92-155">Aşağıdaki, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> `InputRadio` kullanıcıdan bir derecelendirme almak ve doğrulamak için önceki bileşeni kullanır:</span><span class="sxs-lookup"><span data-stu-id="95d92-155">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="95d92-156">`<select>`Öğe seçeneklerini C# nesne değerlerine bağlama `null`</span><span class="sxs-lookup"><span data-stu-id="95d92-156">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="95d92-157">Bir `<select>` öğe seçeneği değerini C# nesne değeri olarak göstermek için herhangi bir mümkün değildir `null` , çünkü:</span><span class="sxs-lookup"><span data-stu-id="95d92-157">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="95d92-158">HTML özniteliklerinin değerleri olamaz `null` .</span><span class="sxs-lookup"><span data-stu-id="95d92-158">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="95d92-159">HTML olarak en yakın eşdeğeri, `null` ÖĞESINDEN html özniteliğinin yokluğunda olur `value` `<option>` .</span><span class="sxs-lookup"><span data-stu-id="95d92-159">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="95d92-160">`<option>`Özniteliği olmayan bir seçerken `value` , tarayıcı değeri bu öğenin *metin içeriği* olarak değerlendirir `<option>` .</span><span class="sxs-lookup"><span data-stu-id="95d92-160">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="95d92-161">BlazorFramework, aşağıdakileri içereceği varsayılan davranışı bastırmak için denenmez:</span><span class="sxs-lookup"><span data-stu-id="95d92-161">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="95d92-162">Çerçevede özel durum geçici çözümleri zinciri oluşturma.</span><span class="sxs-lookup"><span data-stu-id="95d92-162">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="95d92-163">Geçerli çerçeve davranışında son değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="95d92-163">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="95d92-164">HTML 'deki en büyük plausible `null` eşdeğeri boş bir *dizedir* `value` .</span><span class="sxs-lookup"><span data-stu-id="95d92-164">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="95d92-165">BlazorÇerçeve, `null` bir değerine iki yönlü bağlama yönelik boş dize dönüştürmelerini işler `<select>` .</span><span class="sxs-lookup"><span data-stu-id="95d92-165">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="95d92-166">BlazorÇerçeve, `null` iki yönlü bir değere bağlamayı denerken boş dize dönüştürmeleri için otomatik olarak işleme yapmaz `<select>` .</span><span class="sxs-lookup"><span data-stu-id="95d92-166">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="95d92-167">Daha fazla bilgi için bkz. [ `<select>` null değere bağlamayı çözme (DotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="95d92-167">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="95d92-168">Doğrulama desteği</span><span class="sxs-lookup"><span data-stu-id="95d92-168">Validation support</span></span>

<span data-ttu-id="95d92-169"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, Basamaklandırılan veri açıklamalarını kullanarak doğrulama desteğini iliştirir <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="95d92-169">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="95d92-170">Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="95d92-170">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="95d92-171">Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, öğesini <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> özel bir uygulamayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="95d92-171">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="95d92-172">ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="95d92-172">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="95d92-173">Başvuru kaynağına yapılan önceki bağlantılar, deponun `master` dalından kod sağlar ve bu, ürün biriminin ASP.NET Core sonraki sürümü için geçerli geliştirmeyi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="95d92-173">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="95d92-174">Farklı bir sürümün dalını seçmek için GitHub dal seçicisini (örneğin `release/3.1` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="95d92-174">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

Blazor<span data-ttu-id="95d92-175">iki tür doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="95d92-175"> performs two types of validation:</span></span>

* <span data-ttu-id="95d92-176">*Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="95d92-176">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="95d92-177">Alan doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="95d92-177">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="95d92-178">Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="95d92-178">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="95d92-179">Model doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener.</span><span class="sxs-lookup"><span data-stu-id="95d92-179">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="95d92-180">Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="95d92-180">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="95d92-181">Doğrulama özeti ve doğrulama Iletisi bileşenleri</span><span class="sxs-lookup"><span data-stu-id="95d92-181">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="95d92-182"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:</span><span class="sxs-lookup"><span data-stu-id="95d92-182">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="95d92-183">Parametresi ile belirli bir model için çıkış doğrulama iletileri `Model` :</span><span class="sxs-lookup"><span data-stu-id="95d92-183">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="95d92-184"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Bileşeni, [doğrulama Iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="95d92-184">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="95d92-185">Özniteliği ile doğrulama için alanı `For` ve model özelliğini adlandırırken bir lambda ifadesini belirtin:</span><span class="sxs-lookup"><span data-stu-id="95d92-185">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="95d92-186"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Ve <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> bileşenleri, rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="95d92-186">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="95d92-187">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="95d92-187">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="95d92-188">Uygulamanın stil sayfasındaki (veya) doğrulama iletilerinin stilini denetleyin `wwwroot/css/app.css` `wwwroot/css/site.css` .</span><span class="sxs-lookup"><span data-stu-id="95d92-188">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="95d92-189">Varsayılan `validation-message` sınıf, doğrulama iletilerinin metin rengini kırmızı olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="95d92-189">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="95d92-190">Özel doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="95d92-190">Custom validation attributes</span></span>

<span data-ttu-id="95d92-191">Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını geçirin <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="95d92-191">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="95d92-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType>, `null` değeridir.</span><span class="sxs-lookup"><span data-stu-id="95d92-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="95d92-193">Yönteminde doğrulama için ekleme Hizmetleri `IsValid` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="95d92-193">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="95d92-194">veri ek açıklamaları doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="95d92-194"> data annotations validation package</span></span>

<span data-ttu-id="95d92-195">, [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="95d92-195">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="95d92-196">Paket şu anda *deneysel*.</span><span class="sxs-lookup"><span data-stu-id="95d92-196">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="95d92-197">[CompareProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="95d92-197">[CompareProperty] attribute</span></span>

<span data-ttu-id="95d92-198"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Doğrulama sonucunu belirli bir üyeyle ilişkilendirmediği için bileşen ile iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="95d92-198">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="95d92-199">Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="95d92-199">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="95d92-200">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *Deneysel* paket, `ComparePropertyAttribute` Bu sınırlamalar etrafında çalışabilen ek bir doğrulama özniteliği sunar.</span><span class="sxs-lookup"><span data-stu-id="95d92-200">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="95d92-201">Bir Blazor uygulamada, `[CompareProperty]` özniteliği için doğrudan değiştirme olur [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) .</span><span class="sxs-lookup"><span data-stu-id="95d92-201">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="95d92-202">İç içe modeller, koleksiyon türleri ve karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="95d92-202">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="95d92-203">yerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="95d92-203"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="95d92-204">Ancak, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> yalnızca koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.</span><span class="sxs-lookup"><span data-stu-id="95d92-204">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="95d92-205">Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için `ObjectGraphDataAnnotationsValidator` *deneysel* paket tarafından sunulan öğesini kullanın [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="95d92-205">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="95d92-206">İle model özelliklerine açıklama ekleyin `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="95d92-206">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="95d92-207">Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:</span><span class="sxs-lookup"><span data-stu-id="95d92-207">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="95d92-208">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="95d92-208">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="95d92-209">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="95d92-209">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="95d92-210">Form doğrulamasına göre Gönder düğmesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="95d92-210">Enable the submit button based on form validation</span></span>

<span data-ttu-id="95d92-211">Form doğrulamasına göre Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="95d92-211">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="95d92-212"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>Bileşen başlatıldığında modeli atamak için formunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="95d92-212">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="95d92-213"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için bağlam geri aramasında formu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="95d92-213">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="95d92-214">Yöntemi içindeki olay işleyicisinin üstünden geri dön `Dispose` .</span><span class="sxs-lookup"><span data-stu-id="95d92-214">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="95d92-215">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="95d92-215">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="95d92-216">Yukarıdaki örnekte, şu şekilde ayarlayın `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="95d92-216">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="95d92-217">Form geçerli varsayılan değerlerle önceden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="95d92-217">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="95d92-218">Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="95d92-218">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="95d92-219">Önceki yaklaşımın yan etkisi, <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Kullanıcı herhangi bir alanla etkileşime geçtiğinde bileşen geçersiz alanlarla doldurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="95d92-219">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="95d92-220">Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:</span><span class="sxs-lookup"><span data-stu-id="95d92-220">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="95d92-221"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Form üzerinde bir bileşen kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="95d92-221">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="95d92-222"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Gönder düğmesi seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).</span><span class="sxs-lookup"><span data-stu-id="95d92-222">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="95d92-223">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="95d92-223">Troubleshoot</span></span>

> <span data-ttu-id="95d92-224">InvalidOperationException: EditForm bir model parametresi veya bir EditContext parametresi gerektiriyor, ancak her ikisini de içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="95d92-224">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="95d92-225">Veya olduğunu doğrulayın <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="95d92-225">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="95d92-226">Forma atama yaparken <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> , aşağıdaki örnekte gösterildiği gibi model türünün örneği oluşturulmuş olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="95d92-226">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
