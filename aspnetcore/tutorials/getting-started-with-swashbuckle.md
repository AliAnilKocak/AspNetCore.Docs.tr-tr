---
title: Swashbuckle ve ASP.NET Core kullanmaya başlayın
author: zuckerthoben
description: Swagger Kullanıcı arabirimini bütünleştirmek için ASP.NET Core Web API Projenize swashbuckle ekleme hakkında bilgi edinin.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/21/2019
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: ec40b98d756e4b34bf3fed52ac31f7c58a377a78
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68863004"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Swashbuckle ve ASP.NET Core kullanmaya başlayın

, [Shayne Boyer](https://twitter.com/spboyer) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

Swashbuckle için üç ana bileşen vardır:

* [Swashbuckle. aspnetcore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): nesneleri JSON uç noktaları olarak göstermek `SwaggerDocument` için Swagger nesne modeli ve ara yazılımı.

* [Swashbuckle. aspnetcore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): `SwaggerDocument` nesneleri doğrudan rotalarınız, Denetleyicilerinizden ve modellerden oluşturan bir Swagger Oluşturucu. Swagger JSON 'yi otomatik olarak göstermek için genellikle Swagger uç nokta ara yazılımı ile birleştirilir.

* [Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger Kullanıcı arabirimi aracının gömülü bir sürümü. Web API işlevlerini açıklamak için bir zengin ve özelleştirilebilir deneyim oluşturmak üzere Swagger JSON 'u yorumlar. Ortak yöntemler için yerleşik test gücünden içerir.

## <a name="package-installation"></a>Paket yüklemesi

Aşağıdaki yaklaşımlar ile swashbuckle eklenebilir:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Paket Yöneticisi konsol** penceresinde:
  *  > **Diğer**Windowspaket > **Yöneticisi konsolunu** görüntüle ' ye git
  * *TodoApi. csproj* dosyasının bulunduğu dizine gidin
  * Aşağıdaki komutu yürütün:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0-rc2
    ```

* **NuGet Paketlerini Yönet** iletişim kutusunda:
  * **Çözüm Gezgini** > **NuGet Paketlerini Yönet** ' de projeye sağ tıklayın
  * **Paket kaynağını** "NuGet.org" olarak ayarlayın
  * "Ön sürümü dahil et" seçeneğinin etkinleştirildiğinden emin olun
  * Arama kutusuna "swashbuckle. AspNetCore" yazın
  * **Araştır** sekmesinden en son "swashbuckle. aspnetcore" paketini seçin ve sonra da **yüklensin** ' e tıklayın.

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Çözüm bölmesi** paket > **Ekle...** ' da paketler klasörüne sağ tıklayın.
* **Paket Ekle** penceresinin **kaynak** açılan penceresini "NuGet.org" olarak ayarlayın
* "Yayın öncesi paketleri göster" seçeneğinin etkin olduğundan emin olun
* Arama kutusuna "swashbuckle. AspNetCore" yazın
* Sonuçlar bölmesinden en son "swashbuckle. AspNetCore" paketini seçin ve **paket Ekle** ' ye tıklayın.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Tümleşik terminalden**aşağıdaki komutu çalıştırın:

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0-rc2
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Şu komutu çalıştırın:

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0-rc2
```

---

## <a name="add-and-configure-swagger-middleware"></a>Swagger ara yazılım ekleme ve yapılandırma

Sınıfında, `OpenApiInfo` sınıfını kullanmak için aşağıdaki ad alanını içeri aktarın: `Startup`

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

`Startup.ConfigureServices` Yöntemdeki Services koleksiyonuna Swagger oluşturucuyu ekleyin:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

`Startup.Configure` Yönteminde, oluşturulan JSON belgesine ve Swagger Kullanıcı arabirimine hizmet veren ara yazılımı etkinleştirin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

Önceki `UseSwaggerUI` Yöntem çağrısı [statik dosya ara yazılımını](xref:fundamentals/static-files)sunar. .NET Framework veya .NET Core 1. x 'i hedefliyorsanız, projeye [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet paketini ekleyin.

Uygulamayı başlatın ve adresine `http://localhost:<port>/swagger/v1/swagger.json`gidin. Uç noktaları tanımlayan oluşturulan belge, [Swagger belirtiminde (Swagger. JSON)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)gösterildiği gibi görünür.

Swagger Kullanıcı arabirimi adresinde `http://localhost:<port>/swagger`bulunabilir. Swagger Kullanıcı arabirimi aracılığıyla API 'YI keşfet ve diğer programlarda birleştirme.

> [!TIP]
> Swagger Kullanıcı arabirimine uygulamanın kökünde (`http://localhost:<port>/`) hizmeti sağlamak için, `RoutePrefix` özelliğini boş bir dizeye ayarlayın:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

IIS veya ters proxy ile dizin kullanıyorsanız, Swagger uç noktasını, `./` öneki kullanılarak göreli bir yol olarak ayarlayın. Örneğin: `./swagger/v1/swagger.json`. Kullanarak `/swagger/v1/swagger.json` , uygulamanın URL 'nin gerçek kökünde json dosyasını aramasını söyler (Ayrıca kullanılıyorsa rota öneki). Örneğin, `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`yerine kullanın.

## <a name="customize-and-extend"></a>Özelleştirme ve genişletme

Swagger, nesne modelini belgeleme ve Kullanıcı arabirimini temanızla eşleşecek şekilde özelleştirme seçenekleri sağlar.

Başlangıç sınıfında, aşağıdaki ad alanlarını ekleyin:[!code-csharp[](~/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_PreReqNamespaces)]

### <a name="api-info-and-description"></a>API bilgisi ve açıklaması

`AddSwaggerGen` Yöntemine geçirilen yapılandırma eylemi yazar, lisans ve açıklama gibi bilgileri ekler:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Swagger Kullanıcı arabirimi, sürümün bilgilerini görüntüler:

![Sürüm bilgileriyle Swagger Kullanıcı arabirimi: Açıklama, yazar ve daha fazla bağlantı görüntüle](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML açıklamaları

XML açıklamaları aşağıdaki yaklaşımlar ile etkinleştirilebilir:

#### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* **Çözüm Gezgini** projeye sağ tıklayın ve **düzenle < Project_Name >. csproj**' yı seçin.
* Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Projeye sağ **Çözüm Gezgini** seçip **özellikleri**.
* **Build** sekmesinin **output** bölümünün altındaki **XML belge dosyası** kutusunu işaretleyin.

::: moniker-end

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* *Çözüm bölmesi*, **Denetim** ' e basın ve proje adına tıklayın. **Araçlar** > **dosya düzenleme**sayfasına gidin.
* Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **Derleme** derleyicisi>proje> seçenekleri iletişim kutusunu açın
* **Genel Seçenekler** bölümünün altındaki **XML oluştur belge** kutusunu işaretleyin

::: moniker-end

#### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

XML açıklamalarını etkinleştirmek, belgelenmemiş ortak türler ve Üyeler için hata ayıklama bilgileri sağlar. Belgelenmemiş türler ve Üyeler uyarı iletisiyle belirtilir. Örneğin, aşağıdaki ileti 1591 uyarı kodunu ihlal eder:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Uyarıları proje genelinde gizlemek için, proje dosyasında yoksayılacak uyarı kodlarının noktalı virgülle ayrılmış bir listesini tanımlayın. Uyarı kodlarının `$(NoWarn);` eklenmesi [ C# varsayılan değerleri](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) de uygular.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Yalnızca belirli Üyeler için uyarıları gizlemek için, kodu [#pragma uyarı](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) Önişlemci yönergeleri arasına alın. Bu yaklaşım, API belgeleri aracılığıyla sunulmaması gereken kod için yararlıdır. Aşağıdaki örnekte, tüm `Program` sınıf için uyarı kodu CS1591 yok sayılır. Uyarı kodu zorlaması sınıf tanımının kapandığına geri yüklenir. Virgülle ayrılmış bir liste ile birden çok uyarı kodu belirtin.

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

Swagger 'yi yukarıdaki yönergelerle oluşturulan XML dosyasını kullanacak şekilde yapılandırın. Linux veya Windows dışı işletim sistemleri için dosya adları ve yolları büyük/küçük harfe duyarlı olabilir. Örneğin, *TodoApi. xml* dosyası Windows üzerinde geçerlidir ancak CentOS değildir.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

Yukarıdaki kodda, [yansıma](/dotnet/csharp/programming-guide/concepts/reflection) , Web API projesi ile eşleşen bir XML dosya adı oluşturmak için kullanılır. [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) ÖZELLIĞI, XML dosyasının yolunu oluşturmak için kullanılır. Bazı Swagger özellikleri (örneğin, bir XML belge dosyası kullanılmadan, giriş parametrelerinin veya HTTP yöntemlerinin ve yanıt kodlarının) bir bölümü çalışır. Çoğu özellik için, yöntem özetleri ve parametrelerin ve yanıt kodlarının açıklamaları, bir XML dosyası kullanımı zorunludur.

Bir eyleme Üçlü eğik çizgi açıklamaları eklemek, Bölüm üstbilgisine açıklama ekleyerek Swagger Kullanıcı arabirimini geliştirir. Eylemin`Delete` üstüne bir [ \<Summary >](/dotnet/csharp/programming-guide/xmldoc/summary) öğesi ekleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Swagger Kullanıcı arabirimi, önceki kodun `<summary>` öğesinin iç metnini görüntüler:

![XML açıklamasını gösteren Swagger Kullanıcı arabirimi, belirli bir TodoItem siler. DELETE yöntemi için](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

Kullanıcı arabirimi, oluşturulan JSON şeması tarafından çalıştırılır:

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

Eylem yöntemi belgelerine bir [ \<açıklama >](/dotnet/csharp/programming-guide/xmldoc/remarks) öğesi ekleyin. `Create` `<summary>` Öğesinde belirtilen bilgileri tamamlar ve daha sağlam bir Swagger Kullanıcı arabirimi sağlar. `<remarks>` Öğe içeriği metin, JSON veya XML içerebilir.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Bu ek açıklamalarla UI geliştirmelerini göz unutmayın:

![Ek açıklamaların gösterildiği Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Veri açıklamaları

[System. ComponentModel. datanot](/dotnet/api/system.componentmodel.dataannotations) ad alanında bulunan ve bu modeli, Swagger Kullanıcı Arabirimi bileşenlerini kullanmanıza yardımcı olacak şekilde görüntüler.

`[Required]` Özniteliğini sınıfının`TodoItem` özelliğine ekleyin: `Name`

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

Bu özniteliğin varlığı, Kullanıcı arabirimi davranışını değiştirir ve temel alınan JSON şemasını değiştirir:

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

`[Produces("application/json")]` Özniteliği API denetleyicisine ekleyin. Amaç, denetleyicinin eylemlerinin bir *uygulama/JSON*yanıt içerik türünü desteklediğini bildirsağlamaktır:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

**Yanıt Içerik türü** açılan liste, denetleyicinin al eylemleri için varsayılan olarak bu içerik türünü seçer:

![Varsayılan yanıt içerik türüyle Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Web API 'sindeki veri ek açıklamaların kullanımı arttıkça, UI ve API Yardım sayfaları daha açıklayıcı ve yararlı hale gelir.

### <a name="describe-response-types"></a>Yanıt türlerini açıkla

Bir Web API 'si kullanan geliştiriciler, özellikle yanıt türleri ve hata&mdash;kodları (Standart değilse) döndürülmesiyle ilgilidir. Yanıt türleri ve hata kodları XML açıklamaları ve veri ek açıklamalarında gösterilir.

`Create` Eylem başarılı olduğunda bir http 201 durum kodu döndürür. Postalanan istek gövdesi null olduğunda bir HTTP 400 durum kodu döndürülür. Swagger Kullanıcı arabiriminde doğru belgeler olmadan, tüketici beklenen bu sonuçlar hakkında bilgi sahibi yoktur. Aşağıdaki örneğe vurgulanan satırları ekleyerek bu sorunu giderebilirsiniz:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Swagger Kullanıcı arabirimi artık beklenen HTTP yanıt kodlarını açıkça belgelemektedir:

![Yanıt Iletileri ' ndeki durum kodu ve nedeni için, POST Response sınıfının Description ', yeni oluşturulan Todo öğesini döndürür ve ' 400-öğesi null ise '](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

ASP.NET Core 2,2 veya üzeri sürümlerde kurallar, ile `[ProducesResponseType]`tek tek eylemleri açıkça dekorasyon alternatifi olarak kullanılabilir. Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Kullanıcı arabirimini özelleştirme

Hisse senedi Kullanıcı arabirimi hem işlevsel hem de edileni. Ancak, API belge sayfaları markanızı veya temanızı temsil etmelidir. Marka, swashbuckle bileşenleri, statik dosyalara ve bu dosyaları barındırmak için klasör yapısını oluşturmaya yönelik kaynakların eklenmesini gerektirir.

.NET Framework veya .NET Core 1. x 'i hedefliyorsanız, [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet paketini projeye ekleyin:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Önceki NuGet paketi, .NET Core 2. x hedefleniyorsa ve [metapackage](xref:fundamentals/metapackage)kullanılarak zaten yüklüdür.

Statik dosya ara yazılımını etkinleştir:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

[Swagger Kullanıcı arabirimi GitHub deposundan](https://github.com/swagger-api/swagger-ui/tree/master/dist) *Dist* klasörünün içeriğini alın. Bu klasör, Swagger Kullanıcı arabirimi sayfası için gerekli varlıkları içerir.

Bir *Wwwroot/Swagger/UI* klasörü oluşturun ve bunu *Dist* klasörünün içeriğine kopyalayın.

Sayfa üstbilgisini özelleştirmek için aşağıdaki CSS ile *Wwwroot/Swagger/UI*içinde *özel bir. css* dosyası oluşturun:

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Diğer CSS dosyalarından sonra *index. html* dosyasındaki *Custom. css* dosyasına başvurun:

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

Konumundaki`http://localhost:<port>/swagger/ui/index.html` *index. html* sayfasına gidin. Üstbilginin `http://localhost:<port>/swagger/v1/swagger.json` metin kutusuna girip **keşfet** düğmesine tıklayın. Elde edilen sayfa şu şekilde görünür:

![Özel üstbilgi başlıklı Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/custom-header.png)

Sayfada yapabileceğiniz çok daha fazla şey vardır. [Swagger Kullanıcı arabirimi GitHub DEPOSUNDAKI](https://github.com/swagger-api/swagger-ui)UI kaynakları için tam yeteneklere bakın.
