---
title: ASP.NET Core 'de model bağlama
author: rick-anderson
description: ASP.NET Core model bağlamasının nasıl çalıştığını ve davranışını nasıl özelleştireceğinizi öğrenin.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/21/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 823d92c279454fc6c744eebbecf4268412774eba
ms.sourcegitcommit: a104ba258ae7c0b3ee7c6fa7eaea1ddeb8b6eb73
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74478715"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="987f0-103">ASP.NET Core 'de model bağlama</span><span class="sxs-lookup"><span data-stu-id="987f0-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="987f0-104">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="987f0-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="987f0-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="987f0-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="987f0-106">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="987f0-106">What is Model binding</span></span>

<span data-ttu-id="987f0-107">Denetleyiciler ve Razor sayfaları, HTTP isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="987f0-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="987f0-108">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="987f0-109">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="987f0-110">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="987f0-110">Model binding automates this process.</span></span> <span data-ttu-id="987f0-111">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="987f0-111">The model binding system:</span></span>

* <span data-ttu-id="987f0-112">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="987f0-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="987f0-113">Yöntem parametrelerinde ve genel özelliklerde bulunan denetleyicilere ve Razor sayfalarına verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="987f0-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="987f0-114">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="987f0-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="987f0-115">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="987f0-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="987f0-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="987f0-116">Example</span></span>

<span data-ttu-id="987f0-117">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="987f0-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="987f0-118">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="987f0-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="987f0-119">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="987f0-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="987f0-120">`id`adlı bir tamsayı olan `GetByID`ilk parametresini bulur.</span><span class="sxs-lookup"><span data-stu-id="987f0-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="987f0-121">HTTP isteğindeki kullanılabilir kaynakları arar ve yönlendirme verilerinde `id` = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="987f0-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="987f0-122">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="987f0-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="987f0-123">`dogsOnly`adlı bir Boole değeri olan `GetByID`sonraki parametresini bulur.</span><span class="sxs-lookup"><span data-stu-id="987f0-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="987f0-124">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="987f0-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="987f0-125">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="987f0-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="987f0-126">"True" dizesini Boole `true`dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="987f0-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="987f0-127">Daha sonra Framework, `id` parametresi için 2 ' ye geçerek ve `dogsOnly` parametresi için `true` `GetById` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="987f0-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="987f0-128">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="987f0-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="987f0-129">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="987f0-130">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="987f0-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="987f0-131">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="987f0-132">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="987f0-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="987f0-133">Hedefler</span><span class="sxs-lookup"><span data-stu-id="987f0-133">Targets</span></span>

<span data-ttu-id="987f0-134">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="987f0-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="987f0-135">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="987f0-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="987f0-136">Bir isteğin yönlendirildiği Razor Pages işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="987f0-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="987f0-137">Bir denetleyicinin veya `PageModel` sınıfının öznitelikler tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="987f0-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="987f0-138">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="987f0-138">[BindProperty] attribute</span></span>

<span data-ttu-id="987f0-139">Bir denetleyicinin veya `PageModel` sınıfın ortak özelliğine, model bağlamasının bu özelliği hedeflemesini sağlamak için uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="987f0-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="987f0-140">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="987f0-140">[BindProperties] attribute</span></span>

<span data-ttu-id="987f0-141">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="987f0-142">Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya `PageModel` sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="987f0-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="987f0-143">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="987f0-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="987f0-144">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="987f0-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="987f0-145">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="987f0-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="987f0-146">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="987f0-147">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="987f0-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="987f0-148">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliğini `true`olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="987f0-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="987f0-149">Ğına</span><span class="sxs-lookup"><span data-stu-id="987f0-149">Sources</span></span>

<span data-ttu-id="987f0-150">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="987f0-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="987f0-151">Form alanları</span><span class="sxs-lookup"><span data-stu-id="987f0-151">Form fields</span></span>
1. <span data-ttu-id="987f0-152">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="987f0-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="987f0-153">Verileri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="987f0-153">Route data</span></span>
1. <span data-ttu-id="987f0-154">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="987f0-154">Query string parameters</span></span>
1. <span data-ttu-id="987f0-155">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="987f0-155">Uploaded files</span></span>

<span data-ttu-id="987f0-156">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="987f0-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="987f0-157">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="987f0-157">There are a few exceptions:</span></span>

* <span data-ttu-id="987f0-158">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="987f0-159">Karşıya yüklenen dosyalar yalnızca `IFormFile` veya `IEnumerable<IFormFile>`uygulayan hedef türlere bağlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="987f0-160">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="987f0-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="987f0-161">[[Fromquery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="987f0-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="987f0-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="987f0-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="987f0-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="987f0-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="987f0-164">[[Frombody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -istek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="987f0-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="987f0-165">[[Fromheader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -http başlıklarından değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="987f0-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="987f0-166">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="987f0-166">These attributes:</span></span>

* <span data-ttu-id="987f0-167">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="987f0-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="987f0-168">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="987f0-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="987f0-169">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="987f0-170">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="987f0-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="987f0-171">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="987f0-171">[FromBody] attribute</span></span>

<span data-ttu-id="987f0-172">Bir HTTP isteğinin gövdesinden özelliklerini doldurmak için `[FromBody]` özniteliğini bir parametreye uygulayın.</span><span class="sxs-lookup"><span data-stu-id="987f0-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="987f0-173">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="987f0-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="987f0-174">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="987f0-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="987f0-175">Karmaşık bir tür parametresine `[FromBody]` uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="987f0-176">Örneğin, aşağıdaki `Create` eylemi `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="987f0-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="987f0-177">`Pet` sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="987f0-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="987f0-178">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="987f0-178">In the preceding example:</span></span>

* <span data-ttu-id="987f0-179">`[FromQuery]` özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="987f0-180">`Breed` özelliği bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="987f0-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="987f0-181">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="987f0-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="987f0-182">Gövdede uygun bir değer bulunursa, bu değer `Breed` özelliğini doldurmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="987f0-183">Eylem yöntemi başına birden fazla parametreye `[FromBody]` uygulamayın.</span><span class="sxs-lookup"><span data-stu-id="987f0-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="987f0-184">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer `[FromBody]` parametrelerini bağlamak için artık bir daha okunamaz.</span><span class="sxs-lookup"><span data-stu-id="987f0-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="987f0-185">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="987f0-185">Additional sources</span></span>

<span data-ttu-id="987f0-186">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara*göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="987f0-187">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="987f0-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="987f0-188">Örneğin, tanımlama bilgileri veya oturum durumu verilerini isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="987f0-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="987f0-189">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="987f0-189">To get data from a new source:</span></span>

* <span data-ttu-id="987f0-190">`IValueProvider`uygulayan bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="987f0-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="987f0-191">`IValueProviderFactory`uygulayan bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="987f0-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="987f0-192">Factory sınıfını `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="987f0-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="987f0-193">Örnek uygulama, tanımlama bilgilerinden değerler alan bir [değer sağlayıcısı](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) ve [Factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) örneği içerir.</span><span class="sxs-lookup"><span data-stu-id="987f0-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="987f0-194">`Startup.ConfigureServices`kayıt kodu aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="987f0-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="987f0-195">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="987f0-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="987f0-196">Listenin ilk olması için, `Add`yerine `Insert(0, new CookieValueProviderFactory())` çağırın.</span><span class="sxs-lookup"><span data-stu-id="987f0-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="987f0-197">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="987f0-197">No source for a model property</span></span>

<span data-ttu-id="987f0-198">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="987f0-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="987f0-199">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="987f0-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="987f0-200">Null yapılabilir basit türler `null`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="987f0-201">Null yapılamayan değer türleri `default(T)`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="987f0-202">Örneğin, `int id` parametresi 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="987f0-203">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="987f0-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="987f0-204">Diziler, `byte[]` dizilerinin `null`olarak ayarlandığı durumlar dışında `Array.Empty<T>()`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="987f0-205">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [[Bindrequired] özniteliğini](#bindrequired-attribute)kullanın.</span><span class="sxs-lookup"><span data-stu-id="987f0-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="987f0-206">Bu `[BindRequired]` davranışının, bir istek gövdesinde JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="987f0-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="987f0-207">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="987f0-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="987f0-208">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="987f0-208">Type conversion errors</span></span>

<span data-ttu-id="987f0-209">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="987f0-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="987f0-210">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="987f0-211">`[ApiController]` özniteliğine sahip bir API denetleyicisinde, geçersiz model durumu otomatik HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="987f0-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="987f0-212">Razor sayfasında, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="987f0-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="987f0-213">İstemci tarafı doğrulama, aksi durumda Razor Pages bir forma gönderilemeyen hatalı verileri yakalar.</span><span class="sxs-lookup"><span data-stu-id="987f0-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="987f0-214">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="987f0-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="987f0-215">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="987f0-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="987f0-216">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="987f0-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="987f0-217">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="987f0-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="987f0-218">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="987f0-219">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="987f0-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="987f0-220">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="987f0-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="987f0-221">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="987f0-222">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="987f0-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="987f0-223">Basit türler</span><span class="sxs-lookup"><span data-stu-id="987f0-223">Simple types</span></span>

<span data-ttu-id="987f0-224">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="987f0-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="987f0-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="987f0-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="987f0-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="987f0-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="987f0-227">Char</span><span class="sxs-lookup"><span data-stu-id="987f0-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="987f0-228">Hem</span><span class="sxs-lookup"><span data-stu-id="987f0-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="987f0-229">Türünde</span><span class="sxs-lookup"><span data-stu-id="987f0-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="987f0-230">Kategori</span><span class="sxs-lookup"><span data-stu-id="987f0-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="987f0-231">Çift</span><span class="sxs-lookup"><span data-stu-id="987f0-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="987f0-232">Yardımının</span><span class="sxs-lookup"><span data-stu-id="987f0-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="987f0-233">'İni</span><span class="sxs-lookup"><span data-stu-id="987f0-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="987f0-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="987f0-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="987f0-235">Sunuculu</span><span class="sxs-lookup"><span data-stu-id="987f0-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="987f0-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="987f0-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="987f0-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="987f0-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="987f0-238">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="987f0-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="987f0-239">Sürüm</span><span class="sxs-lookup"><span data-stu-id="987f0-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="987f0-240">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="987f0-240">Complex types</span></span>

<span data-ttu-id="987f0-241">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="987f0-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="987f0-242">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="987f0-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="987f0-243">Karmaşık türün her özelliği için model bağlama, ad modeli ön eki için kaynakları arar *. property_name*.</span><span class="sxs-lookup"><span data-stu-id="987f0-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="987f0-244">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="987f0-245">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="987f0-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="987f0-246">`PageModel` public özelliğine bağlama için, önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="987f0-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="987f0-247">Bazı özniteliklerin, parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan `Prefix` bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="987f0-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="987f0-248">Örneğin, karmaşık türün aşağıdaki `Instructor` sınıfı olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="987f0-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="987f0-249">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="987f0-249">Prefix = parameter name</span></span>

<span data-ttu-id="987f0-250">Bağlanacak model `instructorToUpdate`adlı bir parametredir:</span><span class="sxs-lookup"><span data-stu-id="987f0-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="987f0-251">Model bağlama, anahtar `instructorToUpdate.ID`kaynaklara bakarak başlar.</span><span class="sxs-lookup"><span data-stu-id="987f0-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="987f0-252">Bu bulunamazsa, öneki olmayan `ID` arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="987f0-253">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="987f0-253">Prefix = property name</span></span>

<span data-ttu-id="987f0-254">Bağlanacak model, denetleyicinin veya `PageModel` sınıfının `Instructor` adlı bir özelliktir:</span><span class="sxs-lookup"><span data-stu-id="987f0-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="987f0-255">Model bağlama, anahtar `Instructor.ID`kaynaklara bakarak başlar.</span><span class="sxs-lookup"><span data-stu-id="987f0-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="987f0-256">Bu bulunamazsa, öneki olmayan `ID` arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="987f0-257">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="987f0-257">Custom prefix</span></span>

<span data-ttu-id="987f0-258">Bağlanacak model `instructorToUpdate` adlı bir parametredir ve `Bind` özniteliği önek olarak `Instructor` belirtir:</span><span class="sxs-lookup"><span data-stu-id="987f0-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="987f0-259">Model bağlama, anahtar `Instructor.ID`kaynaklara bakarak başlar.</span><span class="sxs-lookup"><span data-stu-id="987f0-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="987f0-260">Bu bulunamazsa, öneki olmayan `ID` arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="987f0-261">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="987f0-261">Attributes for complex type targets</span></span>

<span data-ttu-id="987f0-262">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="987f0-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="987f0-263">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="987f0-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="987f0-264">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları 'nı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="987f0-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="987f0-265">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="987f0-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="987f0-266">Ayrıca bkz. [model doğrulamasında](xref:mvc/models/validation#required-attribute)`[Required]` özniteliği tartışması.</span><span class="sxs-lookup"><span data-stu-id="987f0-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="987f0-267">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="987f0-267">[BindRequired] attribute</span></span>

<span data-ttu-id="987f0-268">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="987f0-269">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="987f0-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="987f0-270">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="987f0-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="987f0-271">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="987f0-271">[BindNever] attribute</span></span>

<span data-ttu-id="987f0-272">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="987f0-273">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="987f0-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="987f0-274">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="987f0-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="987f0-275">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="987f0-275">[Bind] attribute</span></span>

<span data-ttu-id="987f0-276">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="987f0-277">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="987f0-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="987f0-278">Aşağıdaki örnekte, herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca `Instructor` modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="987f0-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="987f0-279">Aşağıdaki örnekte, `OnPost` yöntemi çağrıldığında yalnızca `Instructor` modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="987f0-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="987f0-280">`[Bind]` özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="987f0-281">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="987f0-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="987f0-282">Fazla nakline karşı savunma için, `[Bind]` özniteliği yerine, görüntüleme modelleri önerilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="987f0-283">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="987f0-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="987f0-284">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="987f0-284">Collections</span></span>

<span data-ttu-id="987f0-285">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name*ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="987f0-286">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="987f0-287">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="987f0-287">For example:</span></span>

* <span data-ttu-id="987f0-288">Bağlanacak parametrenin `selectedCourses`adlı bir dizi olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="987f0-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="987f0-289">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="987f0-289">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="987f0-290">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="987f0-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="987f0-291">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametresine geçirir:</span><span class="sxs-lookup"><span data-stu-id="987f0-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="987f0-292">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="987f0-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="987f0-293">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="987f0-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="987f0-294">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="987f0-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="987f0-295">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="987f0-296">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="987f0-297">Sözlüğü</span><span class="sxs-lookup"><span data-stu-id="987f0-297">Dictionaries</span></span>

<span data-ttu-id="987f0-298">`Dictionary` hedefler için, model bağlama *parameter_name* veya *property_name*eşleşmelerini arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="987f0-299">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="987f0-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="987f0-300">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="987f0-300">For example:</span></span>

* <span data-ttu-id="987f0-301">Hedef parametrenin `selectedCourses`adlı bir `Dictionary<int, string>` olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="987f0-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="987f0-302">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="987f0-302">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="987f0-303">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin sözlüğünü `selectedCourses` parametresine geçirir:</span><span class="sxs-lookup"><span data-stu-id="987f0-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="987f0-304">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="987f0-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="987f0-305">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="987f0-305">selectedCourses["2000"]="Economics"</span></span>

## <a name="special-data-types"></a><span data-ttu-id="987f0-306">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="987f0-306">Special data types</span></span>

<span data-ttu-id="987f0-307">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="987f0-307">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="987f0-308">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="987f0-308">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="987f0-309">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="987f0-309">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="987f0-310">Ayrıca, birden çok dosya için `IEnumerable<IFormFile>` desteklenir.</span><span class="sxs-lookup"><span data-stu-id="987f0-310">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="987f0-311">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="987f0-311">CancellationToken</span></span>

<span data-ttu-id="987f0-312">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-312">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="987f0-313">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="987f0-313">FormCollection</span></span>

<span data-ttu-id="987f0-314">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="987f0-314">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="987f0-315">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="987f0-315">Input formatters</span></span>

<span data-ttu-id="987f0-316">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-316">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="987f0-317">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-317">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="987f0-318">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="987f0-318">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="987f0-319">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="987f0-319">You can add other formatters for other content types.</span></span>

<span data-ttu-id="987f0-320">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="987f0-320">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="987f0-321">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="987f0-321">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="987f0-322">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="987f0-322">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="987f0-323">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="987f0-323">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="987f0-324">`Startup.ConfigureServices`<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> veya <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>çağırın.</span><span class="sxs-lookup"><span data-stu-id="987f0-324">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="987f0-325">`Consumes` özniteliğini, istek gövdesinde XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="987f0-325">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="987f0-326">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="987f0-326">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="987f0-327">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="987f0-327">Exclude specified types from model binding</span></span>

<span data-ttu-id="987f0-328">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="987f0-328">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="987f0-329">[Mvcoptions. Modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek `ModelMetadata` özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="987f0-329">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="987f0-330">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-330">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="987f0-331">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için `Startup.ConfigureServices`bir <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> ekleyin.</span><span class="sxs-lookup"><span data-stu-id="987f0-331">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="987f0-332">Örneğin, `System.Version`türündeki tüm modeller üzerinde model bağlamayı devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="987f0-332">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="987f0-333">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için `Startup.ConfigureServices`<xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> ekleyin.</span><span class="sxs-lookup"><span data-stu-id="987f0-333">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="987f0-334">Örneğin, `System.Guid`türündeki özelliklerde doğrulamayı devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="987f0-334">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="987f0-335">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="987f0-335">Custom model binders</span></span>

<span data-ttu-id="987f0-336">Özel bir model cildi yazarak ve belirli bir hedef için seçmek üzere `[ModelBinder]` özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="987f0-336">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="987f0-337">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="987f0-337">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="987f0-338">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="987f0-338">Manual model binding</span></span>

<span data-ttu-id="987f0-339">Model bağlama, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> yöntemi kullanılarak el ile çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="987f0-339">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="987f0-340">Yöntemi hem `ControllerBase` hem de `PageModel` sınıflarında tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="987f0-340">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="987f0-341">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="987f0-341">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="987f0-342">Model bağlama başarısız olursa Yöntem `false` döndürür.</span><span class="sxs-lookup"><span data-stu-id="987f0-342">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="987f0-343">Örnek buradadır:</span><span class="sxs-lookup"><span data-stu-id="987f0-343">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="987f0-344">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="987f0-344">[FromServices] attribute</span></span>

<span data-ttu-id="987f0-345">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="987f0-345">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="987f0-346">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="987f0-346">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="987f0-347">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="987f0-347">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="987f0-348">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="987f0-348">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="987f0-349">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="987f0-349">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
