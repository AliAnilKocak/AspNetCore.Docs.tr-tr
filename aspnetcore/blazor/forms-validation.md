---
title: BlazorForms ve doğrulama ASP.NET Core
author: guardrex
description: İçindeki form ve alan doğrulama senaryolarını nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/18/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 6fde5800a6a791c4a5923c13964c34977a59c017
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865303"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="82fd8-103">BlazorForms ve doğrulama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82fd8-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="82fd8-104">[Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/)ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="82fd8-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="82fd8-105">Formlar ve doğrulama, Blazor [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="82fd8-106">Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="82fd8-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="82fd8-107">Bir form, bileşeni kullanılarak tanımlanır <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="82fd8-108">Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodu gösterir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="82fd8-109">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="82fd8-109">In the preceding example:</span></span>

* <span data-ttu-id="82fd8-110">Form, `name` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular `ExampleModel` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="82fd8-111">Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda ( `exampleModel` ) tutulur.</span><span class="sxs-lookup"><span data-stu-id="82fd8-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="82fd8-112">Alanı, `Model` öğesinin özniteliğine atanır `<EditForm>` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="82fd8-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Bileşenin `@bind-Value` bağlamaları:</span><span class="sxs-lookup"><span data-stu-id="82fd8-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="82fd8-114">Model özelliği ( `exampleModel.Name` ) <xref:Microsoft.AspNetCore.Components.Forms.InputText> bileşen `Value` özelliğine.</span><span class="sxs-lookup"><span data-stu-id="82fd8-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="82fd8-115">Özellik bağlama hakkında daha fazla bilgi için bkz <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> ..</span><span class="sxs-lookup"><span data-stu-id="82fd8-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="82fd8-116">Bileşen özelliğine bir değişiklik olayı temsilcisi <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="82fd8-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [Doğrulayıcı bileşeni](#validator-components) , veri ek açıklamalarını kullanarak doğrulama desteğini iliştirir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="82fd8-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen doğrulama iletilerini özetler.</span><span class="sxs-lookup"><span data-stu-id="82fd8-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="82fd8-119">`HandleValidSubmit` Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).</span><span class="sxs-lookup"><span data-stu-id="82fd8-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="82fd8-120">Yerleşik Forms bileşenleri</span><span class="sxs-lookup"><span data-stu-id="82fd8-120">Built-in forms components</span></span>

<span data-ttu-id="82fd8-121">Bir dizi yerleşik bileşen, Kullanıcı girişini almak ve doğrulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="82fd8-122">Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır.</span><span class="sxs-lookup"><span data-stu-id="82fd8-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="82fd8-123">Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="82fd8-124">Giriş bileşeni</span><span class="sxs-lookup"><span data-stu-id="82fd8-124">Input component</span></span> | <span data-ttu-id="82fd8-125">Olarak işlendi&hellip;</span><span class="sxs-lookup"><span data-stu-id="82fd8-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="82fd8-126">Giriş bileşeni</span><span class="sxs-lookup"><span data-stu-id="82fd8-126">Input component</span></span> | <span data-ttu-id="82fd8-127">Olarak işlendi&hellip;</span><span class="sxs-lookup"><span data-stu-id="82fd8-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

<span data-ttu-id="82fd8-128">Dahil olmak üzere tüm giriş bileşenleri, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="82fd8-128">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="82fd8-129">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-129">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="82fd8-130">Giriş bileşenleri, alan CSS sınıfının alan durumunu yansıtacak şekilde güncelleştirilmesi dahil olmak üzere, bir alan değiştirildiğinde doğrulamak için varsayılan davranışı sağlar.</span><span class="sxs-lookup"><span data-stu-id="82fd8-130">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="82fd8-131">Bazı bileşenler, yararlı ayrıştırma mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-131">Some components include useful parsing logic.</span></span> <span data-ttu-id="82fd8-132">Örneğin, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> düzeltilemez değerleri <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> doğrulama hatası olarak kaydederek, düzeltilemez değerleri düzgün şekilde işleyin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-132">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="82fd8-133">Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="82fd8-133">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="82fd8-134">Aşağıdaki tür, daha `Starship` önce daha büyük bir özellik kümesi ve daha önceki veri açıklamalarını kullanarak doğrulama mantığını tanımlar `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="82fd8-134">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="82fd8-135">Önceki örnekte, `Description` hiçbir veri ek açıklaması mevcut olmadığından isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="82fd8-135">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="82fd8-136">Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular `Starship` :</span><span class="sxs-lookup"><span data-stu-id="82fd8-136">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
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
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="82fd8-137">, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Hangi alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components/cascading-values-and-parameters) olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="82fd8-137">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="82fd8-138">Ya **da** ' a atayın <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-138">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="82fd8-139">Her ikisinin atanması desteklenmez ve bir **çalışma zamanı hatası**oluşturur.</span><span class="sxs-lookup"><span data-stu-id="82fd8-139">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="82fd8-140"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Geçerli ve geçersiz form gönderimi için uygun olaylar sağlar:</span><span class="sxs-lookup"><span data-stu-id="82fd8-140">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="82fd8-141"><xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>Doğrulamayı tetiklemek ve alan değerlerini denetlemek için özel kod kullanmak üzere kullanın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-141">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="82fd8-142">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="82fd8-142">In the following example:</span></span>

* <span data-ttu-id="82fd8-143">`HandleSubmit` **`Submit`** Düğme seçildiğinde Yöntem yürütülür.</span><span class="sxs-lookup"><span data-stu-id="82fd8-143">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="82fd8-144">Form çağırarak onaylanır <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-144">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="82fd8-145">Doğrulama sonucuna bağlı olarak ek kod yürütülür.</span><span class="sxs-lookup"><span data-stu-id="82fd8-145">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="82fd8-146">İş mantığını atanan yöntemine yerleştirin <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-146">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="82fd8-147">Framework API 'SI, doğrulama iletilerini doğrudan bir öğesinden temizlemek için yok <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-147">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="82fd8-148">Bu nedenle, genellikle bir form içinde yeni bir doğrulama iletileri eklemeniz önerilmez <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-148">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="82fd8-149">Doğrulama iletilerini yönetmek için, bu makalede açıklandığı gibi [iş mantığı doğrulama kodunuzla](#business-logic-validation)bir [Doğrulayıcı bileşeni](#validator-components) kullanın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-149">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="82fd8-150">Görünen ad desteği</span><span class="sxs-lookup"><span data-stu-id="82fd8-150">Display name support</span></span>

<span data-ttu-id="82fd8-151">*Bu bölüm, .NET 5 Release Candidate 1 (RC1) veya sonraki sürümler için geçerlidir. Bu sürüm, orta Eylül 'de kullanıma sunulacaktır.*</span><span class="sxs-lookup"><span data-stu-id="82fd8-151">*This section applies to .NET 5 Release Candidate 1 (RC1) or later, which will be released in mid-September.*</span></span>

<span data-ttu-id="82fd8-152">Aşağıdaki yerleşik bileşenler parametresiyle görünen adları destekler `DisplayName` :</span><span class="sxs-lookup"><span data-stu-id="82fd8-152">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="82fd8-153">Aşağıdaki `InputDate` bileşen örneğinde:</span><span class="sxs-lookup"><span data-stu-id="82fd8-153">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="82fd8-154">Görünen ad ( `DisplayName` ) olarak ayarlanır `birthday` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-154">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="82fd8-155">Bileşen, `BirthDate` bir tür olarak özelliğe bağlanır `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-155">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="82fd8-156">Kullanıcı bir tarih değeri sağlamıyorsa, doğrulama hatası şöyle görünür:</span><span class="sxs-lookup"><span data-stu-id="82fd8-156">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="82fd8-157">Doğrulayıcı bileşenleri</span><span class="sxs-lookup"><span data-stu-id="82fd8-157">Validator components</span></span>

<span data-ttu-id="82fd8-158">Doğrulayıcı bileşenleri, form için bir için yöneterek form doğrulamasını destekler <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-158">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="82fd8-159">BlazorFramework, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [doğrulama özniteliklerine (veri ek açıklamaları)](xref:mvc/models/validation#validation-attributes)göre formlara doğrulama desteği eklemek için bileşeni sağlar.</span><span class="sxs-lookup"><span data-stu-id="82fd8-159">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="82fd8-160">Farklı formlar için doğrulama mesajlarını aynı sayfada veya aynı formda farklı form işleme adımlarında işlemek için özel Doğrulayıcı bileşenleri oluşturun, örneğin, istemci tarafı doğrulama ve ardından sunucu tarafı doğrulama.</span><span class="sxs-lookup"><span data-stu-id="82fd8-160">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="82fd8-161">Bu bölümde gösterilen Doğrulayıcı bileşeni örneği, `CustomValidator` Bu makalenin aşağıdaki bölümlerinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="82fd8-161">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="82fd8-162">İş mantığı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="82fd8-162">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="82fd8-163">Sunucu doğrulaması</span><span class="sxs-lookup"><span data-stu-id="82fd8-163">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="82fd8-164">Özel veri ek açıklaması doğrulama öznitelikleri, birçok durumda özel Doğrulayıcı bileşenleri yerine kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-164">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="82fd8-165">Formun modeline uygulanan özel öznitelikler bileşenin kullanımıyla etkinleştirilir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-165">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="82fd8-166">Sunucu tarafı doğrulama ile kullanıldığında, modele uygulanan özel özniteliklerin sunucuda çalıştırılabilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-166">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="82fd8-167">Daha fazla bilgi için bkz. <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="82fd8-167">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="82fd8-168">Bir doğrulayıcı bileşeni oluştur <xref:Microsoft.AspNetCore.Components.ComponentBase> :</span><span class="sxs-lookup"><span data-stu-id="82fd8-168">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="82fd8-169">Form, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> bileşenin geçişli bir [parametresidir](xref:blazor/components/cascading-values-and-parameters) .</span><span class="sxs-lookup"><span data-stu-id="82fd8-169">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="82fd8-170">Doğrulayıcı bileşeni başlatıldığında, <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> form hatalarının geçerli bir listesini korumak için yeni bir oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="82fd8-170">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="82fd8-171">İleti deposu, form bileşenindeki geliştirici kodu yöntemi çağırdığında hata alır `DisplayErrors` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-171">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="82fd8-172">Hatalar, `DisplayErrors` içindeki yöntemine geçirilir [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) .</span><span class="sxs-lookup"><span data-stu-id="82fd8-172">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="82fd8-173">Sözlükte, anahtar bir veya daha fazla hata içeren form alanının adıdır.</span><span class="sxs-lookup"><span data-stu-id="82fd8-173">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="82fd8-174">Değer, hata listesidir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-174">The value is the error list.</span></span>
* <span data-ttu-id="82fd8-175">Aşağıdakilerden herhangi biri oluştuğunda iletiler temizlenir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-175">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="82fd8-176"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>Olay oluşturulduğunda doğrulama istenir <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-176">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="82fd8-177">Tüm hatalar temizlenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-177">All of the errors are cleared.</span></span>
  * <span data-ttu-id="82fd8-178">Olay ortaya çıktığında formdaki bir alan değişir <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-178">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="82fd8-179">Yalnızca alanın hataları temizlenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-179">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="82fd8-180">`ClearErrors`Yöntemi geliştirici kodu tarafından çağrılır.</span><span class="sxs-lookup"><span data-stu-id="82fd8-180">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="82fd8-181">Tüm hatalar temizlenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-181">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
{
    public class CustomValidator : ComponentBase
    {
        private ValidationMessageStore messageStore;

        [CascadingParameter]
        private EditContext CurrentEditContext { get; set; }

        protected override void OnInitialized()
        {
            if (CurrentEditContext == null)
            {
                throw new InvalidOperationException(
                    $"{nameof(CustomValidator)} requires a cascading " +
                    $"parameter of type {nameof(EditContext)}. " +
                    $"For example, you can use {nameof(CustomValidator)} " +
                    $"inside an {nameof(EditForm)}.");
            }

            messageStore = new ValidationMessageStore(CurrentEditContext);

            CurrentEditContext.OnValidationRequested += (s, e) => 
                messageStore.Clear();
            CurrentEditContext.OnFieldChanged += (s, e) => 
                messageStore.Clear(e.FieldIdentifier);
        }

        public void DisplayErrors(Dictionary<string, List<string>> errors)
        {
            foreach (var err in errors)
            {
                messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
            }

            CurrentEditContext.NotifyValidationStateChanged();
        }

        public void ClearErrors()
        {
            messageStore.Clear();
            CurrentEditContext.NotifyValidationStateChanged();
        }
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="82fd8-182">İş mantığı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="82fd8-182">Business logic validation</span></span>

<span data-ttu-id="82fd8-183">İş mantığı doğrulaması, bir sözlükte form hataları alan bir [Doğrulayıcı bileşeniyle](#validator-components) gerçekleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-183">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="82fd8-184">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="82fd8-184">In the following example:</span></span>

* <span data-ttu-id="82fd8-185">`CustomValidator`Bu makalenin [Doğrulayıcı bileşenleri](#validator-components) bölümündeki bileşen kullanılır.</span><span class="sxs-lookup"><span data-stu-id="82fd8-185">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="82fd8-186">Kullanıcı, gönderme `Description` `Defense` sınıflandırmasını () seçerse, bu doğrulama, alıcının açıklaması () için bir değer gerektirir `Classification` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-186">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="82fd8-187">, Bileşende doğrulama iletileri ayarlandığında, bu, doğrulayıcının öğesine eklenir ve şu şekilde <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> gösterilir <xref:Microsoft.AspNetCore.Components.Forms.EditForm> :</span><span class="sxs-lookup"><span data-stu-id="82fd8-187">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```csharp
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="82fd8-188">[Doğrulama bileşenlerinin](#validator-components)kullanılmasına alternatif olarak, veri ek açıklaması doğrulama öznitelikleri de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-188">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="82fd8-189">Formun modeline uygulanan özel öznitelikler bileşenin kullanımıyla etkinleştirilir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-189">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="82fd8-190">Sunucu tarafı doğrulama ile kullanıldığında, özniteliklerin sunucuda çalıştırılabilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-190">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="82fd8-191">Daha fazla bilgi için bkz. <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="82fd8-191">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="82fd8-192">Sunucu doğrulaması</span><span class="sxs-lookup"><span data-stu-id="82fd8-192">Server validation</span></span>

<span data-ttu-id="82fd8-193">Sunucu doğrulama, bir sunucu [Doğrulayıcı bileşeniyle](#validator-components)gerçekleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-193">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="82fd8-194">Bileşeniyle birlikte istemci tarafı doğrulamayı işleyin <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-194">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="82fd8-195">Form, istemci tarafı doğrulaması ( <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> çağrıldığında) geçtiğinde, <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> Form işleme için bir arka uç sunucu API 'sine gönderin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-195">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="82fd8-196">Sunucuda işlem modeli doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="82fd8-196">Process model validation on the server.</span></span>
* <span data-ttu-id="82fd8-197">Sunucu API 'SI, geliştirici tarafından sağlanan yerleşik Framework veri ek açıklamaları doğrulama ve özel doğrulama mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-197">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="82fd8-198">Sunucuda doğrulama başarılı olursa, formu işleyin ve bir başarı durum kodu geri gönderin (*200-Tamam*).</span><span class="sxs-lookup"><span data-stu-id="82fd8-198">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="82fd8-199">Doğrulama başarısız olursa, bir hata durum kodu (*400-hatalı istek*) ve alan doğrulama hatalarını döndürün.</span><span class="sxs-lookup"><span data-stu-id="82fd8-199">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="82fd8-200">Başarı durumunda formu devre dışı bırakın ya da hataları görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-200">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="82fd8-201">Aşağıdaki örnek temel alınarak verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-201">The following example is based on:</span></span>

* <span data-ttu-id="82fd8-202">Barındırılan Blazor [ Blazor proje şablonu](xref:blazor/hosting-models#blazor-webassembly)tarafından oluşturulan barındırılan bir çözüm.</span><span class="sxs-lookup"><span data-stu-id="82fd8-202">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="82fd8-203">Örnek, Blazor [güvenlik ve Identity belgelerde](xref:blazor/security/webassembly/index#implementation-guidance)açıklanan Güvenli barındırılan çözümlerin herhangi biriyle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-203">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="82fd8-204">Önceki [yerleşik form bileşenleri](#built-in-forms-components) bölümünde *Starfleet Stargeme veritabanı* formu örneği.</span><span class="sxs-lookup"><span data-stu-id="82fd8-204">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="82fd8-205">BlazorFramework <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşeni.</span><span class="sxs-lookup"><span data-stu-id="82fd8-205">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="82fd8-206">`CustomValidator` [Doğrulayıcı bileşenleri](#validator-components) bölümünde gösterilen bileşen.</span><span class="sxs-lookup"><span data-stu-id="82fd8-206">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="82fd8-207">Aşağıdaki örnekte, sunucu API 'SI, `Description` Kullanıcı `Defense` teslim sınıflandırmasını () seçerse, alıcının açıklaması () için bir değer sağlandığını doğrular `Classification` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-207">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="82fd8-208">`Starship` `Shared` Hem istemci hem de sunucu uygulamalarının modeli kullanabilmesi için modeli çözümün projesine yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-208">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="82fd8-209">Model veri ek açıklamaları gerektirdiğinden, [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) projenin proje dosyasına bir paket başvurusu ekleyin `Shared` :</span><span class="sxs-lookup"><span data-stu-id="82fd8-209">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="82fd8-210">Paketin en son önizleme dışı sürümünü öğrenmek için [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations)adresindeki paket **sürümü geçmişine** bakın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-210">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="82fd8-211">Sunucu API 'SI projesinde, starsevkiyat doğrulama isteklerini () işlemek için bir denetleyici ekleyin `Controllers/StarshipValidation.cs` ve başarısız doğrulama iletilerini geri döndürün:</span><span class="sxs-lookup"><span data-stu-id="82fd8-211">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {MESSAGE}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="82fd8-212">Sunucuda bir model bağlama doğrulama hatası oluştuğunda, [`ApiController`](xref:web-api/index) ( <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ) normalde ile [varsayılan hatalı istek yanıtı](xref:web-api/index#default-badrequest-response) döndürür <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-212">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="82fd8-213">Yanıt, şu örnekte gösterildiği gibi yalnızca doğrulama hatalarından daha fazla veri içerir: *Starfleet Starsevk veritabanı* formunun tüm alanları gönderilmediğinde ve form doğrulama başarısız olduğunda:</span><span class="sxs-lookup"><span data-stu-id="82fd8-213">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="82fd8-214">Sunucu API 'SI önceki varsayılan JSON yanıtını döndürürse, istemcinin düğümün alt öğelerini elde etmek için yanıtı ayrıştırması mümkündür `errors` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-214">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="82fd8-215">Ancak, dosyayı ayrıştırmaya uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-215">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="82fd8-216">JSON ayrıştırılırken, <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) form doğrulama hatası işleme hataları üretmek için çağrıldıktan sonra ek kod gerekir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-216">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="82fd8-217">İdeal olarak, sunucu API 'SI yalnızca doğrulama hatalarını döndürmelidir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-217">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="82fd8-218">Sunucu API 'sinin yanıtını yalnızca doğrulama hatalarını döndürecek şekilde değiştirmek için, içinde ile açıklanmakta olan eylemlerde çağrılan temsilciyi değiştirin <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-218">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="82fd8-219">API uç noktası ( `/StarshipValidation` ) için ile bir döndürür <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-219">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="82fd8-220">Diğer API uç noktaları için, nesne sonucunu yeni bir ile döndürerek varsayılan davranışı koruyun <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> :</span><span class="sxs-lookup"><span data-stu-id="82fd8-220">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="82fd8-221">Daha fazla bilgi için bkz. <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="82fd8-221">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="82fd8-222">İstemci projesinde, [Doğrulayıcı bileşenleri](#validator-components) bölümünde gösterilen Doğrulayıcı bileşenini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-222">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="82fd8-223">İstemci projesinde, *Starfleet Başlangıçgönder veritabanı* formu, bileşen yardımı ile sunucu doğrulama hatalarını gösterecek şekilde güncelleştirilir `CustomValidator` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-223">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="82fd8-224">Sunucu API 'SI doğrulama iletileri döndürdüğünde, bileşen öğesine eklenir `CustomValidator` <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-224">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="82fd8-225">Hatalar, formun <xref:Microsoft.AspNetCore.Components.Forms.EditContext> form tarafından görüntülenmek üzere tarafından kullanılabilir <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> :</span><span class="sxs-lookup"><span data-stu-id="82fd8-225">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```csharp
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {MESSAGE}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="82fd8-226">[Doğrulama bileşenlerine](#validator-components)alternatif olarak, veri ek açıklaması doğrulama öznitelikleri de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-226">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="82fd8-227">Formun modeline uygulanan özel öznitelikler bileşenin kullanımıyla etkinleştirilir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-227">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="82fd8-228">Sunucu tarafı doğrulama ile kullanıldığında, özniteliklerin sunucuda çalıştırılabilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-228">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="82fd8-229">Daha fazla bilgi için bkz. <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="82fd8-229">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="82fd8-230">Bu bölümdeki sunucu tarafı doğrulama yaklaşımı, Blazor WebAssembly Bu belge kümesindeki barındırılan her türlü çözüm örneği için uygundur:</span><span class="sxs-lookup"><span data-stu-id="82fd8-230">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="82fd8-231">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="82fd8-231">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="82fd8-232">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="82fd8-232">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="82fd8-233">Identity Server</span><span class="sxs-lookup"><span data-stu-id="82fd8-233">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="82fd8-234">Giriş olayına göre InputText</span><span class="sxs-lookup"><span data-stu-id="82fd8-234">InputText based on the input event</span></span>

<span data-ttu-id="82fd8-235"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Olayı yerine olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın `input` `change` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-235">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="82fd8-236">Aşağıdaki örnekte, `CustomInputText` bileşen Framework `InputText` bileşenini devralır ve olay bağlamayı ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) `oninput` olaya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="82fd8-236">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="82fd8-237">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="82fd8-237">`Shared/CustomInputText.razor`:</span></span>

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

<span data-ttu-id="82fd8-238">`CustomInputText`Bileşen her yerde kullanılabilir <xref:Microsoft.AspNetCore.Components.Forms.InputText> :</span><span class="sxs-lookup"><span data-stu-id="82fd8-238">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="82fd8-239">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="82fd8-239">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="82fd8-240">Radyo düğmeleri</span><span class="sxs-lookup"><span data-stu-id="82fd8-240">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="82fd8-241">`InputRadio` `InputRadioGroup` Radyo düğmesi grubu oluşturmak için bileşeni olan bileşenleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-241">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="82fd8-242">Aşağıdaki örnekte, Özellikler `Starship` [yerleşik Forms bileşenleri](#built-in-forms-components) bölümünde açıklanan modele eklenir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-242">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="82fd8-243">Aşağıdakileri uygulamaya ekleyin `enums` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-243">Add the following `enums` to the app.</span></span> <span data-ttu-id="82fd8-244">' İ barındıracak yeni bir dosya oluşturun `enums` veya `enums` `Starship.cs` dosyayı dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-244">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="82fd8-245">`enums` `Starship` Model ve *Starfleet başlangıçgönder veritabanı* formu için erişilebilir yapın:</span><span class="sxs-lookup"><span data-stu-id="82fd8-245">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="82fd8-246">[Yerleşik Forms bileşenleri](#built-in-forms-components) bölümünde açıklanan *Starfleet starsevkiyat veritabanı* formunu güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-246">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="82fd8-247">Üretilecek bileşenleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="82fd8-247">Add the components to produce:</span></span>

* <span data-ttu-id="82fd8-248">Sevk üreticisi için bir radyo düğmesi grubu.</span><span class="sxs-lookup"><span data-stu-id="82fd8-248">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="82fd8-249">Sevk rengi ve motoru için iç içe bir radyo düğmesi grubu.</span><span class="sxs-lookup"><span data-stu-id="82fd8-249">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="82fd8-250">`Name`Atlanırsa, `InputRadio` Bileşenler en son üst öğesine göre gruplandırılır.</span><span class="sxs-lookup"><span data-stu-id="82fd8-250">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="82fd8-251">Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-251">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="82fd8-252">Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-252">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="82fd8-253">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-253">The following example shows how to:</span></span>

* <span data-ttu-id="82fd8-254">Radyo düğmesi grubu için veri bağlamayı işleyin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-254">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="82fd8-255">Özel bir bileşen kullanarak doğrulamayı destekler `InputRadio` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-255">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="82fd8-256">Aşağıdaki, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> `InputRadio` kullanıcıdan bir derecelendirme almak ve doğrulamak için önceki bileşeni kullanır:</span><span class="sxs-lookup"><span data-stu-id="82fd8-256">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="82fd8-257">`<select>`Öğe seçeneklerini C# nesne değerlerine bağlama `null`</span><span class="sxs-lookup"><span data-stu-id="82fd8-257">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="82fd8-258">Bir `<select>` öğe seçeneği değerini C# nesne değeri olarak göstermek için herhangi bir mümkün değildir `null` , çünkü:</span><span class="sxs-lookup"><span data-stu-id="82fd8-258">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="82fd8-259">HTML özniteliklerinin değerleri olamaz `null` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-259">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="82fd8-260">HTML olarak en yakın eşdeğeri, `null` ÖĞESINDEN html özniteliğinin yokluğunda olur `value` `<option>` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-260">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="82fd8-261">`<option>`Özniteliği olmayan bir seçerken `value` , tarayıcı değeri bu öğenin *metin içeriği* olarak değerlendirir `<option>` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-261">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="82fd8-262">BlazorFramework, aşağıdakileri içereceği varsayılan davranışı bastırmak için denenmez:</span><span class="sxs-lookup"><span data-stu-id="82fd8-262">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="82fd8-263">Çerçevede özel durum geçici çözümleri zinciri oluşturma.</span><span class="sxs-lookup"><span data-stu-id="82fd8-263">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="82fd8-264">Geçerli çerçeve davranışında son değişiklikler.</span><span class="sxs-lookup"><span data-stu-id="82fd8-264">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="82fd8-265">HTML 'deki en büyük plausible `null` eşdeğeri boş bir *dizedir* `value` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-265">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="82fd8-266">BlazorÇerçeve, `null` bir değerine iki yönlü bağlama yönelik boş dize dönüştürmelerini işler `<select>` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-266">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="82fd8-267">BlazorÇerçeve, `null` iki yönlü bir değere bağlamayı denerken boş dize dönüştürmeleri için otomatik olarak işleme yapmaz `<select>` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-267">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="82fd8-268">Daha fazla bilgi için bkz. [ `<select>` null değere bağlamayı çözme (DotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span><span class="sxs-lookup"><span data-stu-id="82fd8-268">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="82fd8-269">Doğrulama desteği</span><span class="sxs-lookup"><span data-stu-id="82fd8-269">Validation support</span></span>

<span data-ttu-id="82fd8-270"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, Basamaklandırılan veri açıklamalarını kullanarak doğrulama desteğini iliştirir <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-270">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="82fd8-271">Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-271">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="82fd8-272">Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, öğesini <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> özel bir uygulamayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-272">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="82fd8-273">ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) .</span><span class="sxs-lookup"><span data-stu-id="82fd8-273">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="82fd8-274">Başvuru kaynağına yapılan önceki bağlantılar, deponun `master` dalından kod sağlar ve bu, ürün biriminin ASP.NET Core sonraki sürümü için geçerli geliştirmeyi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="82fd8-274">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="82fd8-275">Farklı bir sürümün dalını seçmek için GitHub dal seçicisini (örneğin `release/3.1` ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-275">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="82fd8-276">Blazor iki tür doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-276">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="82fd8-277">*Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-277">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="82fd8-278">Alan doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-278">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="82fd8-279">Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-279">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="82fd8-280">Model doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener.</span><span class="sxs-lookup"><span data-stu-id="82fd8-280">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="82fd8-281">Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-281">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="82fd8-282">Doğrulama özeti ve doğrulama Iletisi bileşenleri</span><span class="sxs-lookup"><span data-stu-id="82fd8-282">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="82fd8-283"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:</span><span class="sxs-lookup"><span data-stu-id="82fd8-283">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="82fd8-284">Parametresi ile belirli bir model için çıkış doğrulama iletileri `Model` :</span><span class="sxs-lookup"><span data-stu-id="82fd8-284">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="82fd8-285"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Bileşeni, [doğrulama Iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="82fd8-285">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="82fd8-286">Özniteliği ile doğrulama için alanı `For` ve model özelliğini adlandırırken bir lambda ifadesini belirtin:</span><span class="sxs-lookup"><span data-stu-id="82fd8-286">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="82fd8-287"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Ve <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> bileşenleri, rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="82fd8-287">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="82fd8-288">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-288">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="82fd8-289">Uygulamanın stil sayfasındaki (veya) doğrulama iletilerinin stilini denetleyin `wwwroot/css/app.css` `wwwroot/css/site.css` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-289">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="82fd8-290">Varsayılan `validation-message` sınıf, doğrulama iletilerinin metin rengini kırmızı olarak ayarlar:</span><span class="sxs-lookup"><span data-stu-id="82fd8-290">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="82fd8-291">Özel doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="82fd8-291">Custom validation attributes</span></span>

<span data-ttu-id="82fd8-292">Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını geçirin <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="82fd8-292">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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
> <span data-ttu-id="82fd8-293"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType>, `null` değeridir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-293"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="82fd8-294">Yönteminde doğrulama için ekleme Hizmetleri `IsValid` desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="82fd8-294">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="82fd8-295">Blazor veri ek açıklamaları doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="82fd8-295">Blazor data annotations validation package</span></span>

<span data-ttu-id="82fd8-296">, [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-296">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="82fd8-297">Paket şu anda *deneysel*.</span><span class="sxs-lookup"><span data-stu-id="82fd8-297">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="82fd8-298">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)Paketin en son sürüm *adayı* sürümü [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)adresinde bulunur. Şu anda *deneysel* yayın aday paketini kullanmaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="82fd8-298">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="82fd8-299">Paketin derlemesi, gelecekteki bir sürümdeki çerçeveye veya çalışma zamanına taşınmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-299">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="82fd8-300">Daha fazla güncelleştirme için [Duyurular GitHub deposu](https://github.com/aspnet/Announcements), [DotNet/aspnetcore GitHub deposu](https://github.com/dotnet/aspnetcore)veya bu konu başlığı bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-300">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="82fd8-301">[CompareProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="82fd8-301">[CompareProperty] attribute</span></span>

<span data-ttu-id="82fd8-302"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Doğrulama sonucunu belirli bir üyeyle ilişkilendirmediği için bileşen ile iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="82fd8-302">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="82fd8-303">Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-303">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="82fd8-304">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *Deneysel* paket, `ComparePropertyAttribute` Bu sınırlamalar etrafında çalışabilen ek bir doğrulama özniteliği sunar.</span><span class="sxs-lookup"><span data-stu-id="82fd8-304">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="82fd8-305">Bir Blazor uygulamada, `[CompareProperty]` özniteliği için doğrudan değiştirme olur [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) .</span><span class="sxs-lookup"><span data-stu-id="82fd8-305">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="82fd8-306">İç içe modeller, koleksiyon türleri ve karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="82fd8-306">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="82fd8-307">Blazor yerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-307">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="82fd8-308">Ancak, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> yalnızca koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.</span><span class="sxs-lookup"><span data-stu-id="82fd8-308">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="82fd8-309">Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için `ObjectGraphDataAnnotationsValidator` *deneysel* paket tarafından sunulan öğesini kullanın [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="82fd8-309">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="82fd8-310">İle model özelliklerine açıklama ekleyin `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-310">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="82fd8-311">Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-311">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="82fd8-312">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="82fd8-312">`Starship.cs`:</span></span>

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

<span data-ttu-id="82fd8-313">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="82fd8-313">`ShipDescription.cs`:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="82fd8-314">Form doğrulamasına göre Gönder düğmesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="82fd8-314">Enable the submit button based on form validation</span></span>

<span data-ttu-id="82fd8-315">Form doğrulamasına göre Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="82fd8-315">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="82fd8-316"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>Bileşen başlatıldığında modeli atamak için formunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-316">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="82fd8-317"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için bağlam geri aramasında formu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-317">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="82fd8-318">Yöntemi içindeki olay işleyicisinin üstünden geri dön `Dispose` .</span><span class="sxs-lookup"><span data-stu-id="82fd8-318">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="82fd8-319">Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="82fd8-319">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="82fd8-320">Kullanırken <xref:Microsoft.AspNetCore.Components.Forms.EditContext> , öğesine de bir atayın <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-320">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
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

<span data-ttu-id="82fd8-321">Yukarıdaki örnekte, şu şekilde ayarlayın `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="82fd8-321">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="82fd8-322">Form geçerli varsayılan değerlerle önceden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-322">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="82fd8-323">Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="82fd8-323">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="82fd8-324">Önceki yaklaşımın yan etkisi, <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Kullanıcı herhangi bir alanla etkileşime geçtiğinde bileşen geçersiz alanlarla doldurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="82fd8-324">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="82fd8-325">Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:</span><span class="sxs-lookup"><span data-stu-id="82fd8-325">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="82fd8-326"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Form üzerinde bir bileşen kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-326">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="82fd8-327"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Gönder düğmesi seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).</span><span class="sxs-lookup"><span data-stu-id="82fd8-327">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
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

## <a name="troubleshoot"></a><span data-ttu-id="82fd8-328">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="82fd8-328">Troubleshoot</span></span>

> <span data-ttu-id="82fd8-329">InvalidOperationException: EditForm bir model parametresi veya bir EditContext parametresi gerektiriyor, ancak her ikisini de içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="82fd8-329">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="82fd8-330">Veya olduğunu doğrulayın <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="82fd8-330">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="82fd8-331">Her ikisini de aynı form için kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="82fd8-331">Don't use both for the same form.</span></span>

<span data-ttu-id="82fd8-332">Forma atama yaparken <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> , aşağıdaki örnekte gösterildiği gibi model türünün örneği oluşturulmuş olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="82fd8-332">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
