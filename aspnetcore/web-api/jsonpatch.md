---
title: ASP.NET Core Web API 'sinde JsonPatch
author: rick-anderson
description: ASP.NET Core Web API 'sindeki JSON Patch isteklerini nasıl işleyeceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: e57556e4b3fba55c6c187092593ffab4e31ee2d9
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727111"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="682ba-103">ASP.NET Core Web API 'sinde JsonPatch</span><span class="sxs-lookup"><span data-stu-id="682ba-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="682ba-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Kirk larkabağı](https://github.com/serpent5) tarafından</span><span class="sxs-lookup"><span data-stu-id="682ba-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="682ba-105">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="682ba-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="682ba-106">Paket yüklemesi</span><span class="sxs-lookup"><span data-stu-id="682ba-106">Package installation</span></span>

<span data-ttu-id="682ba-107">`Microsoft.AspNetCore.Mvc.NewtonsoftJson` paketi kullanılarak JsonPatch desteği etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="682ba-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="682ba-108">Bu özelliği etkinleştirmek için uygulamaların şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="682ba-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="682ba-109">[Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="682ba-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="682ba-110">Projenin `Startup.ConfigureServices` yöntemini `AddNewtonsoftJson`çağrısı içerecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="682ba-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="682ba-111">`AddNewtonsoftJson`, MVC hizmeti kayıt yöntemleriyle uyumludur:</span><span class="sxs-lookup"><span data-stu-id="682ba-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="682ba-112">JsonPatch, AddNewtonsoftJson ve System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="682ba-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="682ba-113">`AddNewtonsoftJson`, **Tüm** JSON içeriğini biçimlendirmek için kullanılan `System.Text.Json` tabanlı giriş ve çıkış formatlarını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="682ba-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="682ba-114">`Newtonsoft.Json`kullanarak `JsonPatch` için destek eklemek için, diğer formatlayıcıları değişmeden bırakarak, projenin `Startup.ConfigureServices` aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="682ba-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="682ba-115">Yukarıdaki kod, [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) öğesine ve aşağıdaki using deyimlerine yönelik bir başvuru gerektirir:</span><span class="sxs-lookup"><span data-stu-id="682ba-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="682ba-116">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="682ba-116">PATCH HTTP request method</span></span>

<span data-ttu-id="682ba-117">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="682ba-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="682ba-118">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="682ba-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="682ba-119">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="682ba-119">JSON Patch</span></span>

<span data-ttu-id="682ba-120">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="682ba-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="682ba-121">JSON yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="682ba-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="682ba-122">Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="682ba-123">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="682ba-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="682ba-124">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="682ba-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="682ba-125">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="682ba-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="682ba-126">Önceki JSON 'da:</span><span class="sxs-lookup"><span data-stu-id="682ba-126">In the preceding JSON:</span></span>

* <span data-ttu-id="682ba-127">`op` özelliği işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="682ba-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="682ba-128">`path` özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="682ba-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="682ba-129">`value` özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="682ba-130">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="682ba-130">Resource after patch</span></span>

<span data-ttu-id="682ba-131">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="682ba-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="682ba-132">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="682ba-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="682ba-133">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="682ba-133">Path syntax</span></span>

<span data-ttu-id="682ba-134">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="682ba-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="682ba-135">Örneğin: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="682ba-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="682ba-136">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="682ba-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="682ba-137">`addresses` dizisinin ilk öğesi `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="682ba-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="682ba-138">Bir dizinin sonuna `add` için dizin numarası yerine bir tire (-) kullanın: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="682ba-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="682ba-139">İşlemler</span><span class="sxs-lookup"><span data-stu-id="682ba-139">Operations</span></span>

<span data-ttu-id="682ba-140">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="682ba-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="682ba-141">Çalışma</span><span class="sxs-lookup"><span data-stu-id="682ba-141">Operation</span></span>  | <span data-ttu-id="682ba-142">Notlar</span><span class="sxs-lookup"><span data-stu-id="682ba-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="682ba-143">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="682ba-143">Add a property or array element.</span></span> <span data-ttu-id="682ba-144">Var olan özellik için: set değeri.</span><span class="sxs-lookup"><span data-stu-id="682ba-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="682ba-145">Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="682ba-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="682ba-146">Aynı konumdaki `add` ve ardından `remove` ile aynı.</span><span class="sxs-lookup"><span data-stu-id="682ba-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="682ba-147">Kaynaktaki değeri kullanarak `add` ve ardından hedefteki `remove` ile aynı.</span><span class="sxs-lookup"><span data-stu-id="682ba-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="682ba-148">Kaynaktaki değeri kullanarak hedefle `add` aynı.</span><span class="sxs-lookup"><span data-stu-id="682ba-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="682ba-149">`path` = değeri `value`sağlanmışsa başarı durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="682ba-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="682ba-150">ASP.NET Core 'de JsonPatch</span><span class="sxs-lookup"><span data-stu-id="682ba-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="682ba-151">JSON düzeltme ekinin ASP.NET Core uygulanması, [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="682ba-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="682ba-152">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="682ba-152">Action method code</span></span>

<span data-ttu-id="682ba-153">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="682ba-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="682ba-154">`HttpPatch` özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="682ba-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="682ba-155">Genellikle `[FromBody]`ile bir `JsonPatchDocument<T>`kabul eder.</span><span class="sxs-lookup"><span data-stu-id="682ba-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="682ba-156">Değişiklikleri uygulamak için düzeltme eki belgesinde `ApplyTo` çağırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="682ba-157">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="682ba-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="682ba-158">Örnek uygulamadaki Bu kod, aşağıdaki `Customer` modeliyle çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="682ba-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="682ba-159">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="682ba-159">The sample action method:</span></span>

* <span data-ttu-id="682ba-160">Bir `Customer`oluşturur.</span><span class="sxs-lookup"><span data-stu-id="682ba-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="682ba-161">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="682ba-161">Applies the patch.</span></span>
* <span data-ttu-id="682ba-162">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="682ba-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="682ba-163">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="682ba-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="682ba-164">Model durumu</span><span class="sxs-lookup"><span data-stu-id="682ba-164">Model state</span></span>

<span data-ttu-id="682ba-165">Önceki eylem yöntemi örneği, parametrelerinden biri olarak model durumunu alan `ApplyTo` aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="682ba-166">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="682ba-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="682ba-167">Aşağıdaki örnek, bir `test` işlemi için 400 Hatalı Istek yanıtı gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="682ba-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="682ba-168">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="682ba-168">Dynamic objects</span></span>

<span data-ttu-id="682ba-169">Aşağıdaki eylem yöntemi örneği, dinamik bir nesne için bir düzeltme ekinin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="682ba-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="682ba-170">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-170">The add operation</span></span>

* <span data-ttu-id="682ba-171">`path` bir dizi öğesine işaret ediyorsa: `path`tarafından belirtiden önce yeni bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="682ba-172">`path` bir özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="682ba-173">`path` varolmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="682ba-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="682ba-174">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="682ba-175">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="682ba-176">Aşağıdaki örnek düzeltme eki belgesi, `CustomerName` değerini ayarlar ve `Orders` dizisinin sonuna bir `Order` nesnesi ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="682ba-177">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-177">The remove operation</span></span>

* <span data-ttu-id="682ba-178">`path` bir dizi öğesine işaret ediyorsa: öğeyi kaldırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="682ba-179">`path` bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="682ba-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="682ba-180">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="682ba-181">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="682ba-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="682ba-182">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="682ba-183">Özellik null atanamaz ise, `default<T>`olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="682ba-184">Aşağıdaki örnek düzeltme eki belgesi null olarak `CustomerName` ve `Orders[0]`siler.</span><span class="sxs-lookup"><span data-stu-id="682ba-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="682ba-185">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-185">The replace operation</span></span>

<span data-ttu-id="682ba-186">Bu işlem, işlevsel olarak bir `remove` ve ardından bir `add`.</span><span class="sxs-lookup"><span data-stu-id="682ba-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="682ba-187">Aşağıdaki örnek düzeltme eki belgesi `CustomerName` değerini ayarlar ve `Orders[0]`yeni bir `Order` nesnesiyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="682ba-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="682ba-188">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-188">The move operation</span></span>

* <span data-ttu-id="682ba-189">`path` bir dizi öğesine işaret ediyorsa: `from` öğesini `path` öğesinin konumuna kopyalar, sonra `from` öğesinde bir `remove` işlemi çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="682ba-190">`path` bir özelliğe işaret ediyorsa: `from` özelliğinin değerini `path` özelliğine kopyalar, sonra `from` özelliğinde bir `remove` işlemi çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="682ba-191">`path` varolmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="682ba-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="682ba-192">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="682ba-193">Düzeltme Eki uygulanacak kaynak dinamik bir nesnedir: `from` özelliğini `path`belirtilen konuma kopyalar, sonra `from` özelliğinde `remove` işlemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="682ba-194">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="682ba-194">The following sample patch document:</span></span>

* <span data-ttu-id="682ba-195">`Orders[0].OrderName` değerini `CustomerName`olarak kopyalar.</span><span class="sxs-lookup"><span data-stu-id="682ba-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="682ba-196">`Orders[0].OrderName` null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="682ba-197">`Orders[0]`önce `Orders[1]` ' a gider.</span><span class="sxs-lookup"><span data-stu-id="682ba-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="682ba-198">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-198">The copy operation</span></span>

<span data-ttu-id="682ba-199">Bu işlem, son `remove` adımı olmadan bir `move` işlemiyle aynı işleve sahiptir.</span><span class="sxs-lookup"><span data-stu-id="682ba-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="682ba-200">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="682ba-200">The following sample patch document:</span></span>

* <span data-ttu-id="682ba-201">`Orders[0].OrderName` değerini `CustomerName`olarak kopyalar.</span><span class="sxs-lookup"><span data-stu-id="682ba-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="682ba-202">`Orders[0]`önce `Orders[1]` bir kopyasını ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="682ba-203">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-203">The test operation</span></span>

<span data-ttu-id="682ba-204">`path` tarafından belirtilen konumdaki değer `value`belirtilen değerden farklıysa, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="682ba-205">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="682ba-206">`test` işlem, yaygın olarak bir eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="682ba-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="682ba-207">`CustomerName` başlangıçtaki değeri "John" ise ve test başarısız olduğu için aşağıdaki örnek düzeltme eki belgesi etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="682ba-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="682ba-208">Kodu alın</span><span class="sxs-lookup"><span data-stu-id="682ba-208">Get the code</span></span>

<span data-ttu-id="682ba-209">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="682ba-209">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="682ba-210">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="682ba-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="682ba-211">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="682ba-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="682ba-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="682ba-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="682ba-213">HTTP yöntemi: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="682ba-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="682ba-214">Üst bilgi: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="682ba-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="682ba-215">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="682ba-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="682ba-216">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="682ba-216">Additional resources</span></span>

* [<span data-ttu-id="682ba-217">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="682ba-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="682ba-218">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="682ba-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="682ba-219">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="682ba-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="682ba-220">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="682ba-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="682ba-221">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="682ba-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="682ba-222">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="682ba-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="682ba-223">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="682ba-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="682ba-224">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="682ba-224">PATCH HTTP request method</span></span>

<span data-ttu-id="682ba-225">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="682ba-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="682ba-226">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="682ba-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="682ba-227">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="682ba-227">JSON Patch</span></span>

<span data-ttu-id="682ba-228">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="682ba-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="682ba-229">JSON yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="682ba-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="682ba-230">Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="682ba-231">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="682ba-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="682ba-232">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="682ba-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="682ba-233">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="682ba-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="682ba-234">Önceki JSON 'da:</span><span class="sxs-lookup"><span data-stu-id="682ba-234">In the preceding JSON:</span></span>

* <span data-ttu-id="682ba-235">`op` özelliği işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="682ba-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="682ba-236">`path` özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="682ba-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="682ba-237">`value` özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="682ba-238">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="682ba-238">Resource after patch</span></span>

<span data-ttu-id="682ba-239">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="682ba-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="682ba-240">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="682ba-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="682ba-241">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="682ba-241">Path syntax</span></span>

<span data-ttu-id="682ba-242">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="682ba-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="682ba-243">Örneğin: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="682ba-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="682ba-244">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="682ba-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="682ba-245">`addresses` dizisinin ilk öğesi `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="682ba-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="682ba-246">Bir dizinin sonuna `add` için dizin numarası yerine bir tire (-) kullanın: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="682ba-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="682ba-247">İşlemler</span><span class="sxs-lookup"><span data-stu-id="682ba-247">Operations</span></span>

<span data-ttu-id="682ba-248">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="682ba-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="682ba-249">Çalışma</span><span class="sxs-lookup"><span data-stu-id="682ba-249">Operation</span></span>  | <span data-ttu-id="682ba-250">Notlar</span><span class="sxs-lookup"><span data-stu-id="682ba-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="682ba-251">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="682ba-251">Add a property or array element.</span></span> <span data-ttu-id="682ba-252">Var olan özellik için: set değeri.</span><span class="sxs-lookup"><span data-stu-id="682ba-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="682ba-253">Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="682ba-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="682ba-254">Aynı konumdaki `add` ve ardından `remove` ile aynı.</span><span class="sxs-lookup"><span data-stu-id="682ba-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="682ba-255">Kaynaktaki değeri kullanarak `add` ve ardından hedefteki `remove` ile aynı.</span><span class="sxs-lookup"><span data-stu-id="682ba-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="682ba-256">Kaynaktaki değeri kullanarak hedefle `add` aynı.</span><span class="sxs-lookup"><span data-stu-id="682ba-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="682ba-257">`path` = değeri `value`sağlanmışsa başarı durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="682ba-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="682ba-258">ASP.NET Core 'de JsonPatch</span><span class="sxs-lookup"><span data-stu-id="682ba-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="682ba-259">JSON düzeltme ekinin ASP.NET Core uygulanması, [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="682ba-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="682ba-260">Paket, [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) metapackage 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="682ba-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="682ba-261">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="682ba-261">Action method code</span></span>

<span data-ttu-id="682ba-262">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="682ba-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="682ba-263">`HttpPatch` özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="682ba-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="682ba-264">Genellikle `[FromBody]`ile bir `JsonPatchDocument<T>`kabul eder.</span><span class="sxs-lookup"><span data-stu-id="682ba-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="682ba-265">Değişiklikleri uygulamak için düzeltme eki belgesinde `ApplyTo` çağırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="682ba-266">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="682ba-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="682ba-267">Örnek uygulamadaki Bu kod, aşağıdaki `Customer` modeliyle çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="682ba-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="682ba-268">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="682ba-268">The sample action method:</span></span>

* <span data-ttu-id="682ba-269">Bir `Customer`oluşturur.</span><span class="sxs-lookup"><span data-stu-id="682ba-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="682ba-270">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="682ba-270">Applies the patch.</span></span>
* <span data-ttu-id="682ba-271">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="682ba-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="682ba-272">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="682ba-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="682ba-273">Model durumu</span><span class="sxs-lookup"><span data-stu-id="682ba-273">Model state</span></span>

<span data-ttu-id="682ba-274">Önceki eylem yöntemi örneği, parametrelerinden biri olarak model durumunu alan `ApplyTo` aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="682ba-275">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="682ba-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="682ba-276">Aşağıdaki örnek, bir `test` işlemi için 400 Hatalı Istek yanıtı gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="682ba-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="682ba-277">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="682ba-277">Dynamic objects</span></span>

<span data-ttu-id="682ba-278">Aşağıdaki eylem yöntemi örneği, dinamik bir nesne için bir düzeltme ekinin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="682ba-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="682ba-279">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-279">The add operation</span></span>

* <span data-ttu-id="682ba-280">`path` bir dizi öğesine işaret ediyorsa: `path`tarafından belirtiden önce yeni bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="682ba-281">`path` bir özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="682ba-282">`path` varolmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="682ba-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="682ba-283">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="682ba-284">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="682ba-285">Aşağıdaki örnek düzeltme eki belgesi, `CustomerName` değerini ayarlar ve `Orders` dizisinin sonuna bir `Order` nesnesi ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="682ba-286">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-286">The remove operation</span></span>

* <span data-ttu-id="682ba-287">`path` bir dizi öğesine işaret ediyorsa: öğeyi kaldırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="682ba-288">`path` bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="682ba-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="682ba-289">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="682ba-290">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="682ba-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="682ba-291">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="682ba-292">Özellik null atanamaz ise, `default<T>`olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="682ba-293">Aşağıdaki örnek düzeltme eki belgesi null olarak `CustomerName` ve `Orders[0]`siler.</span><span class="sxs-lookup"><span data-stu-id="682ba-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="682ba-294">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-294">The replace operation</span></span>

<span data-ttu-id="682ba-295">Bu işlem, işlevsel olarak bir `remove` ve ardından bir `add`.</span><span class="sxs-lookup"><span data-stu-id="682ba-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="682ba-296">Aşağıdaki örnek düzeltme eki belgesi `CustomerName` değerini ayarlar ve `Orders[0]`yeni bir `Order` nesnesiyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="682ba-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="682ba-297">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-297">The move operation</span></span>

* <span data-ttu-id="682ba-298">`path` bir dizi öğesine işaret ediyorsa: `from` öğesini `path` öğesinin konumuna kopyalar, sonra `from` öğesinde bir `remove` işlemi çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="682ba-299">`path` bir özelliğe işaret ediyorsa: `from` özelliğinin değerini `path` özelliğine kopyalar, sonra `from` özelliğinde bir `remove` işlemi çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="682ba-300">`path` varolmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="682ba-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="682ba-301">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="682ba-302">Düzeltme Eki uygulanacak kaynak dinamik bir nesnedir: `from` özelliğini `path`belirtilen konuma kopyalar, sonra `from` özelliğinde `remove` işlemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="682ba-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="682ba-303">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="682ba-303">The following sample patch document:</span></span>

* <span data-ttu-id="682ba-304">`Orders[0].OrderName` değerini `CustomerName`olarak kopyalar.</span><span class="sxs-lookup"><span data-stu-id="682ba-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="682ba-305">`Orders[0].OrderName` null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="682ba-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="682ba-306">`Orders[0]`önce `Orders[1]` ' a gider.</span><span class="sxs-lookup"><span data-stu-id="682ba-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="682ba-307">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-307">The copy operation</span></span>

<span data-ttu-id="682ba-308">Bu işlem, son `remove` adımı olmadan bir `move` işlemiyle aynı işleve sahiptir.</span><span class="sxs-lookup"><span data-stu-id="682ba-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="682ba-309">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="682ba-309">The following sample patch document:</span></span>

* <span data-ttu-id="682ba-310">`Orders[0].OrderName` değerini `CustomerName`olarak kopyalar.</span><span class="sxs-lookup"><span data-stu-id="682ba-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="682ba-311">`Orders[0]`önce `Orders[1]` bir kopyasını ekler.</span><span class="sxs-lookup"><span data-stu-id="682ba-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="682ba-312">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="682ba-312">The test operation</span></span>

<span data-ttu-id="682ba-313">`path` tarafından belirtilen konumdaki değer `value`belirtilen değerden farklıysa, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="682ba-314">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="682ba-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="682ba-315">`test` işlem, yaygın olarak bir eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="682ba-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="682ba-316">`CustomerName` başlangıçtaki değeri "John" ise ve test başarısız olduğu için aşağıdaki örnek düzeltme eki belgesi etkisizdir:</span><span class="sxs-lookup"><span data-stu-id="682ba-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="682ba-317">Kodu alın</span><span class="sxs-lookup"><span data-stu-id="682ba-317">Get the code</span></span>

<span data-ttu-id="682ba-318">[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="682ba-318">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="682ba-319">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="682ba-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="682ba-320">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="682ba-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="682ba-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="682ba-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="682ba-322">HTTP yöntemi: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="682ba-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="682ba-323">Üst bilgi: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="682ba-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="682ba-324">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="682ba-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="682ba-325">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="682ba-325">Additional resources</span></span>

* [<span data-ttu-id="682ba-326">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="682ba-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="682ba-327">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="682ba-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="682ba-328">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="682ba-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="682ba-329">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="682ba-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="682ba-330">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="682ba-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="682ba-331">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="682ba-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
