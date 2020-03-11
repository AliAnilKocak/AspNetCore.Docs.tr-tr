---
title: ASP.NET Core Web API 'sindeki özel formatıcılar
author: rick-anderson
description: ASP.NET Core Web API 'Leri için özel biçimleri oluşturma ve kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: dd25cda460ba758cd07de094eaadd1f2d8c28657
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667673"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>ASP.NET Core Web API 'sindeki özel formatıcılar

[Tom Dykstra](https://github.com/tdykstra) tarafından

ASP.NET Core MVC, giriş ve çıkış formatlayıcıları kullanılarak Web API 'Lerinde veri değişimini destekler. Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır. Çıkış biçimleri, [yanıtları biçimlendirmek](xref:web-api/advanced/formatting)için kullanılır.

Framework, JSON ve XML için yerleşik giriş ve çıkış biçimleri sağlar. Düz metin için yerleşik bir çıkış biçimlendiricisi sağlar, ancak düz metin için bir giriş biçimlendiricisi sağlamaz.

Bu makalede, özel formatlayıcılar oluşturarak ek biçimler için nasıl destek ekleneceği gösterilmektedir. Düz metin için özel bir giriş biçimlendirici örneği için GitHub 'da [Textplainınputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Özel formatlayıcılar ne zaman kullanılır?

[İçerik anlaşma](xref:web-api/advanced/formatting#content-negotiation) işleminin yerleşik biçimleri tarafından desteklenmeyen bir içerik türünü desteklemesini istediğinizde, özel bir biçimlendirici kullanın.

Örneğin, Web API 'niz için bazı istemciler [prototip](https://github.com/google/protobuf) biçimini işleyebildiğinden, daha verimli olduğundan bu istemcilerle prototip kullanmak isteyebilirsiniz. Ya da Web API 'nizin kişi adlarını ve adresleri, kişi verilerini değiş tokuş için sık kullanılan bir biçimde, [vCard](https://wikipedia.org/wiki/VCard) biçiminde göndermesini isteyebilirsiniz. Bu makalede belirtilen örnek uygulama, basit bir vCard biçimlendiricisi uygular.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Özel biçimlendirici kullanma konusuna genel bakış

Özel bir biçimlendirici oluşturma ve kullanma adımları aşağıda verilmiştir:

* İstemciye göndermek için veri serileştirmek istiyorsanız çıkış biçimlendirici sınıfı oluşturun.
* İstemciden alınan verilerin serisini kaldırmak istiyorsanız bir giriş biçimlendirici sınıfı oluşturun.
* [Mvcoptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)içindeki `InputFormatters` ve `OutputFormatters` koleksiyonlara formatlamalarınızın örneklerini ekleyin.

Aşağıdaki bölümlerde, bu adımların her biri için rehberlik ve kod örnekleri sağlanmaktadır.

## <a name="how-to-create-a-custom-formatter-class"></a>Özel bir biçimlendirici sınıfı oluşturma

Bir biçimlendirici oluşturmak için:

* Sınıfı uygun temel sınıftan türet.
* Oluşturucuda geçerli medya türleri ve kodlamalar belirtin.
* `CanReadType`/`CanWriteType` yöntemleri geçersiz kıl
* `ReadRequestBodyAsync`/`WriteResponseBodyAsync` yöntemleri geçersiz kıl
  
### <a name="derive-from-the-appropriate-base-class"></a>Uygun taban sınıftan türet

Metin medya türleri (örneğin, vCard) için, [Textinputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) veya [Textoutputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) temel sınıfından türetirsiniz.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

İkili türler için [inputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) veya [outputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) temel sınıfından türetirsiniz.

### <a name="specify-valid-media-types-and-encodings"></a>Geçerli medya türlerini ve kodlamaları belirtin

Oluşturucuda, `SupportedMediaTypes` ve `SupportedEncodings` koleksiyonlara ekleyerek geçerli medya türlerini ve kodlamaları belirtin.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> Bir biçimlendirici sınıfında Oluşturucu bağımlılığı ekleme yapamazsınız. Örneğin, oluşturucuya bir günlükçü parametresi ekleyerek günlükçü alamazsınız. Hizmetlere erişmek için yöntemlerinize geçirilen bağlam nesnesini kullanmanız gerekir. [Aşağıdaki](#read-write) kod örneği bunun nasıl yapılacağını gösterir.

### <a name="override-canreadtypecanwritetype"></a>CanReadType/CanWriteType geçersiz kıl

`CanReadType` veya `CanWriteType` yöntemlerini geçersiz kılarak içinden seri durumdan çıkarabilen veya seri hale getirilecek bir tür belirtin. Örneğin, yalnızca bir `Contact` türünden vCard metni oluşturabileceğiniz gibi, tam tersi de olabilir.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>CanWriteResult yöntemi

Bazı senaryolarda `CanWriteType`yerine `CanWriteResult` geçersiz kılmanız gerekir. Aşağıdaki koşullar doğruysa `CanWriteResult` kullanın:

* Eylem yönteminiz bir model sınıfı döndürür.
* Çalışma zamanında döndürülebilecek türetilmiş sınıflar var.
* Eylem tarafından türetilen sınıfın döndürüldüğü çalışma zamanında bilmeniz gerekir.

Örneğin, eylem yöntemi imzanızın `Person` bir tür döndürdüğünden, ancak `Person`türetilen bir `Student` veya `Instructor` türü döndürebildiğini varsayalım. Biçimlendiricinin yalnızca `Student` nesneleri işlemesini istiyorsanız, `CanWriteResult` yöntemine sunulan bağlam nesnesindeki [nesne](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) türünü denetleyin. Eylem yöntemi `IActionResult`döndürdüğünde `CanWriteResult` kullanılması gerekmediğini unutmayın; Bu durumda `CanWriteType` yöntemi çalışma zamanı türünü alır.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>ReadRequestBodyAsync/WriteResponseBodyAsync geçersiz kıl

`ReadRequestBodyAsync` veya `WriteResponseBodyAsync`serisini kaldırma veya serileştirmede gerçek işi yapabilirsiniz. Aşağıdaki örnekte vurgulanan satırlarda, bağımlılık ekleme kapsayıcısından hizmetlerin nasıl alınacağı gösterilmektedir (Bu parametreleri Oluşturucu parametrelerinden alamazsınız).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Bir giriş biçimlendirici örneği için bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>MVC 'yi özel bir biçimlendirici kullanacak şekilde yapılandırma

Özel bir biçimlendirici kullanmak için, `InputFormatters` veya `OutputFormatters` koleksiyonuna bir biçimlendirici sınıfının bir örneğini ekleyin.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Biçimlendiriciler, eklediğiniz sırada değerlendirilir. Birincisi bir öncelik alır.

## <a name="next-steps"></a>Sonraki adımlar

* [Bu belge için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)basit vCard giriş ve çıkış biçimleri uygulayan örnek uygulama. Uygulamalar aşağıdaki örnekteki gibi görünen vCard 'ları okur ve yazar:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

VCard çıktısını görmek için, uygulamayı çalıştırın ve `http://localhost:63313/api/contacts/` (Visual Studio 'dan çalışırken) veya `http://localhost:5000/api/contacts/` (komut satırından çalışırken) için kabul üst bilgisi "Text/vCard" olan bir get isteği gönderin.

Bir vCard 'ı bellek içi kişiler koleksiyonuna eklemek için, Içerik türü üst bilgisi "metin/vCard" ile aynı URL 'ye bir post isteği gönderin ve gövdesinde örnek olarak biçimlendirilen vCard metni yazın.
