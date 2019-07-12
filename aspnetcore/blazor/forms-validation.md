---
title: ASP.NET Core Blazor formlar ve doğrulama
author: guardrex
description: Formlar ve alan doğrulama senaryoları Blazor içinde kullanmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/forms-validation
ms.openlocfilehash: e1b7de6e31adae8102bbefba5d08418c4daac687
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855783"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor formlar ve doğrulama

Tarafından [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

Formlar ve doğrulama Blazor içinde desteklenen kullanarak [veri ek açıklamaları](xref:mvc/models/validation).

> [!NOTE]
> Formlar ve doğrulama senaryoları her Önizleme sürümü ile değiştirme olasılığı düşüktür.

Aşağıdaki `ExampleModel` doğrulama mantığını veri ek açıklamalarını kullanma türünü tanımlar:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Bir form kullanılarak tanımlanan `EditForm` bileşeni. Aşağıdaki formu tipik öğeleri, bileşenler ve Razor kod gösterir:

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

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

* Form uygulamasında kullanıcı girdisi doğrulama `name` tanımlanan doğrulama kullanarak alan `ExampleModel` türü. Model bileşenin içinde oluşturulur `@code` engelleme ve özel bir alanda tutulan (`exampleModel`). Alana atanan `Model` özniteliği `<EditForm>` öğesi.
* `DataAnnotationsValidator` Bileşen veri ek açıklamalarını kullanma doğrulama desteği ekler.
* `ValidationSummary` Bileşeni doğrulama iletilerinin özetler.
* `HandleValidSubmit` formu başarıyla (Pass doğrulama) gönderdiğinde tetiklenir.

Bir dizi yerleşik giriş bileşenlerini almak ve kullanıcı girişini doğrulama kullanılabilir. Girişler, bunlar değiştirilme zamanı ve bir form gönderildiğinde doğrulanır. Aşağıdaki tabloda kullanılabilir giriş bileşenleri gösterilmektedir.

| Giriş bileşeni | Çizilir&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Tüm giriş bileşenlerin dahil olmak üzere `EditForm`, rastgele öznitelikler destekler. Bir bileşen parametresi eşleşmeyen herhangi bir öznitelik için işlenen HTML öğesine eklenir.

Giriş bileşenleri düzenlendiğinde doğrulama ve değiştirme alanı durumunu yansıtacak şekilde onların CSS sınıfı için varsayılan davranış sağlar. Bazı bileşenler yararlı ayrıştırma mantığı içerir. Örneğin, `InputDate` ve `InputNumber` ayrıştırılamayan değerler işleme düzgün bir şekilde doğrulama hataları kaydederek. Null değerlerini kabul edebilir türleri de hedef alan NULL atanabilirliği destekler (örneğin, `int?`).

Aşağıdaki `Starship` türünü tanımlayan özellikleri ve veri ek açıklamaları daha büyük bir önceki değerinden kullanarak Doğrulama mantığı `ExampleModel`:

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

Önceki örnekte `Description` hiçbir veri ek açıklama olmadığı için isteğe bağlıdır.

Aşağıdaki formu içinde tanımlanan doğrulama kullanan kullanıcı girişi doğrulama `Starship` modeli:

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea Id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate Id="productionDate" @bind-Value="starship.ProductionDate" />
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

`EditForm` Oluşturur bir `EditContext` olarak bir [basamaklı değeri](xref:blazor/components#cascading-values-and-parameters) düzenleme işlemi, hangi alanların değiştirilmiş dahil olmak üzere hakkındaki meta verileri ve geçerli doğrulama iletileri izler. `EditForm` De uygun olayları sağlar için geçerli ve geçersiz gönderir (`OnValidSubmit`, `OnInvalidSubmit`). Alternatif olarak, `OnSubmit` doğrulamayı tetiklemek için ve özel doğrulama kodu ile alan değerlerini denetleyin.

`DataAnnotationsValidator` Bileşeni art arda için veri ek açıklamalarını kullanma doğrulama desteği ekler `EditContext`. Bu açık hareket veri ek açıklamaları şu anda kullanarak doğrulama için desteği etkinleştirme gerektirir, ancak bu daha sonra geçersiz varsayılan davranışı yapmadan düşündüğümüz. Veri ek açıklamaları farklı doğrulama sistemdekinden kullanmak için değiştirin `DataAnnotationsValidator` özel bir uygulama. ASP.NET Core uygulaması için başvuru kaynağı incelemesinin kullanılabilir: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs). *ASP.NET Core uygulaması Önizleme yayın süresince hızlı güncelleştirmeler tabidir.*

`ValidationSummary` Bileşeni benzer olduğu tüm doğrulama iletilerinin özetler [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).

`ValidationMessage` Bileşen görüntüler için benzer bir özel alan doğrulama iletilerinin [doğrulama iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Alan ile doğrulama için belirttiğiniz `For` özniteliğini ve model özelliğine adlandırma bir lambda ifadesi:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

`ValidationMessage` Ve `ValidationSummary` bileşenleri isteğe bağlı öznitelikleri destekler. Bir bileşen parametresi eşleşmeyen herhangi bir öznitelik eklenir oluşturulan `<div>` veya `<ul>` öğesi.
