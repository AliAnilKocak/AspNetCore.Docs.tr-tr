---
title: ASP.NET Core 'de yanıt sıkıştırması
author: guardrex
description: Yanıt sıkıştırması ve ASP.NET Core uygulamalarında yanıt sıkıştırma ara yazılımı kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/09/2019
uid: performance/response-compression
ms.openlocfilehash: e320e87179f9f1b9773a55c380684a3f3f712632
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993469"
---
# <a name="response-compression-in-aspnet-core"></a>ASP.NET Core 'de yanıt sıkıştırması

Tarafından [Luke Latham](https://github.com/guardrex)

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

Ağ bant genişliği sınırlı bir kaynaktır. Yanıt boyutunu azaltmak genellikle önemli ölçüde önemli ölçüde bir uygulamanın yanıt hızını artırır. Yük boyutlarını azaltmanın bir yolu, uygulamanın yanıtlarını sıkıştırmaktır.

## <a name="when-to-use-response-compression-middleware"></a>Yanıt sıkıştırma ara yazılımı ne zaman kullanılır?

IIS, Apache veya NGINX 'te sunucu tabanlı yanıt sıkıştırma teknolojilerini kullanın. Ara yazılım performansı büyük olasılıkla sunucu modülleriyle eşleşmemelidir. [Http. sys Server](xref:fundamentals/servers/httpsys) Server ve [Kestrel](xref:fundamentals/servers/kestrel) Server şu anda yerleşik sıkıştırma desteği sunmaz.

Şu durumlarda yanıt sıkıştırma ara yazılımını kullanın:

* Aşağıdaki sunucu tabanlı sıkıştırma teknolojileri kullanılamıyor:
  * [IIS dinamik sıkıştırma modülü](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate modülü](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [NGINX sıkıştırma ve açma](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Doğrudan barındırma:
  * [Http. sys sunucusu](xref:fundamentals/servers/httpsys) (eski adıyla WebListener)
  * [Kestrel sunucusu](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Yanıt sıkıştırma

Genellikle, yerel olarak sıkıştırılmamış herhangi bir yanıt, yanıt sıkıştırmasından faydalanabilir. Yerel olarak sıkıştırılan yanıtlar genellikle şunları içerir: CSS, JavaScript, HTML, XML ve JSON. PNG dosyaları gibi yerel olarak sıkıştırılan varlıkları sıkıştırmamanız gerekir. Yerel olarak sıkıştırılan bir yanıtı daha fazla sıkıştırmaya çalışırsanız, boyut ve iletim süresi bakımından küçük bir ek azaltma büyük olasılıkla sıkıştırmayı işlemek için geçen süre kadar fazla gölgelenir. 150-1000 bayttan daha küçük dosyaları sıkıştırmayın (dosyanın içeriğine ve sıkıştırma verimliliğine bağlı olarak). Küçük dosyaları sıkıştırma ek yükü sıkıştırılmamış dosyadan daha büyük bir sıkıştırılmış dosya üretebilir.

Bir istemci sıkıştırılmış içeriği işleyebilir, istemci, `Accept-Encoding` üst bilgiyi istekle birlikte göndererek yeteneklerini bilgilendirmelidir. Bir sunucu sıkıştırılmış içerik gönderdiğinde, sıkıştırılan yanıtın kodlanmasının `Content-Encoding` üst bilgisine bilgi içermelidir. Ara yazılım tarafından desteklenen içerik kodlama göstergeleri aşağıdaki tabloda gösterilmiştir.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding`üst bilgi değerleri | Desteklenen ara yazılım | Açıklama |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Evet (varsayılan)        | [Brotli sıkıştırılmış veri biçimi](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Hayır                   | [Sıkıştırılmış veri biçimini söndür](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Hayır                   | [W3C verimli XML değişim](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Evet                  | [Gzip dosya biçimi](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Evet                  | "Kodlama yok" tanımlayıcısı: Yanıt kodlanmamalıdır. |
| `pack200-gzip`                  | Hayır                   | [Java arşivleri için ağ aktarım biçimi](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Evet                  | Tüm kullanılabilir içerik kodlamaları açıkça istenmedi |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding`üst bilgi değerleri | Desteklenen ara yazılım | Açıklama |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Hayır                   | [Brotli sıkıştırılmış veri biçimi](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Hayır                   | [Sıkıştırılmış veri biçimini söndür](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Hayır                   | [W3C verimli XML değişim](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Evet (varsayılan)        | [Gzip dosya biçimi](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Evet                  | "Kodlama yok" tanımlayıcısı: Yanıt kodlanmamalıdır. |
| `pack200-gzip`                  | Hayır                   | [Java arşivleri için ağ aktarım biçimi](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Evet                  | Tüm kullanılabilir içerik kodlamaları açıkça istenmedi |

::: moniker-end

Daha fazla bilgi için, [IANA resmi Içerik kodlama listesine](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)bakın.

Ara yazılım, özel `Accept-Encoding` üst bilgi değerleri için ek sıkıştırma sağlayıcıları eklemenize olanak tanır. Daha fazla bilgi için aşağıdaki [özel sağlayıcılar](#custom-providers) bölümüne bakın.

Ara yazılım, sıkıştırma düzenlerini önceliklendirmek için istemci tarafından gönderildiğinde kalite değeri ( `q`qvalue,) ağırlığa yeniden davranıyor. Daha fazla bilgi için bkz [. RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Sıkıştırma algoritmaları, sıkıştırma hızı ve sıkıştırmanın verimliliği arasında bir zorunluluğunu getirir tabidir. Bu bağlamdaki *verimlilik* , sıkıştırmadan sonra çıkışın boyutunu ifade eder. En en uygun sıkıştırma, en *iyi* boyut ile elde edilir.

Sıkıştırılmış içerik isteme, gönderme, önbelleğe alma ve alma ile ilgili üstbilgiler aşağıdaki tabloda açıklanmıştır.

| Üstbilgi             | Rol |
| ------------------ | ---- |
| `Accept-Encoding`  | İstemci için kabul edilebilir içerik kodlama düzenlerini göstermek üzere istemciden sunucusuna gönderilir. |
| `Content-Encoding` | Yük içindeki içeriğin kodlamasını göstermek için sunucudan istemciye gönderilir. |
| `Content-Length`   | Sıkıştırma gerçekleştiğinde `Content-Length` , yanıt sıkıştırıldığında gövde içeriği değiştiği için başlık kaldırılır. |
| `Content-MD5`      | Sıkıştırma gerçekleştiğinde `Content-MD5` , gövde içeriği değiştiğinden ve karma artık geçerli olmadığından başlık kaldırılır. |
| `Content-Type`     | İçeriğin MIME türünü belirtir. Her yanıt, değerini `Content-Type`belirtmelidir. Ara yazılım, yanıtın sıkıştırılıp sıkıştırılmadığını belirlemede bu değeri denetler. Ara yazılım, kodlayamadığı bir dizi [varsayılan MIME türünü](#mime-types) belirtir, ancak MIME türlerini değiştirebilir veya ekleyebilirsiniz. |
| `Vary`             | Sunucu tarafından istemciler ve proxy 'ler `Accept-Encoding` için bir değere sahip olduğunda `Vary` , üst bilgi, isteğin `Accept-Encoding` üst bilgisinin değerine göre önbelleğe alma (değişiklik) gereken yanıtı istemciye veya proxy 'ye bildirir. `Vary: Accept-Encoding` Üst bilgiyle içerik döndürmesinin sonucu, hem sıkıştırılmış hem de sıkıştırılmamış yanıtların ayrı olarak önbelleğe alınma sonucudur. |

[Örnek uygulamayla](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)yanıt sıkıştırma ara yazılımı 'nın özelliklerini gezin. Örnek şunu gösterir:

* Gzip ve özel sıkıştırma sağlayıcıları kullanılarak uygulama yanıtlarının sıkıştırılması.
* Sıkıştırma için varsayılan MIME türleri listesine MIME türü ekleme.

## <a name="package"></a>Paket

::: moniker range=">= aspnetcore-3.0"

Yanıt sıkıştırma ara yazılımı, ASP.NET Core uygulamalarında örtük olarak bulunan [Microsoft. AspNetCore. ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) paketi tarafından sağlanır.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ara yazılımı bir projeye eklemek için Microsoft. AspNetCore. [ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) paketini içeren [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)öğesine bir başvuru ekleyin.

::: moniker-end

## <a name="configuration"></a>Yapılandırma

::: moniker range=">= aspnetcore-2.2"

Aşağıdaki kod, varsayılan MIME türleri ve sıkıştırma sağlayıcıları için yanıt sıkıştırma ara yazılımı 'nın nasıl etkinleştirileceğini gösterir ([Brotli](#brotli-compression-provider) ve [gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aşağıdaki kod, varsayılan MIME türleri ve [gzip sıkıştırma sağlayıcısı](#gzip-compression-provider)Için yanıt sıkıştırma ara yazılımını nasıl etkinleştireceğinizi göstermektedir:

::: moniker-end

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Notlar:

* `app.UseResponseCompression`öğesinden önce `app.UseMvc`çağrılmalıdır.
* `Accept-Encoding` İstek üst bilgisini ayarlamak ve yanıt üst bilgilerini, boyutunu ve gövdesini incelemek için [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)veya [Postman](https://www.getpostman.com/) gibi bir araç kullanın.

`Accept-Encoding` Üstbilgi olmadan örnek uygulamaya bir istek gönderir ve yanıtın sıkıştırılmamış olduğunu gözlemleyin. `Content-Encoding` Ve`Vary` başlıkları yanıtta yok.

![Accept-Encoding üst bilgisi olmadan bir isteğin sonucunu gösteren Fiddler penceresi. Yanıt sıkıştırılmaz.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

`Accept-Encoding: br` Üstbilgi (Brotli Compression) ile örnek uygulamaya bir istek gönderir ve yanıtın sıkıştırıldığını gözlemleyin. `Content-Encoding` Ve`Vary` üstbilgileri yanıtta mevcuttur.

![Accept-Encoding üst bilgisi ve br değeri olan bir isteğin sonucunu gösteren Fiddler penceresi. Değişiklik ve Içerik kodlama üstbilgileri yanıta eklenir. Yanıt sıkıştırıldı.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

`Accept-Encoding: gzip` Üstbilgi ile örnek uygulamaya bir istek gönderir ve yanıtın sıkıştırıldığını gözlemleyin. `Content-Encoding` Ve`Vary` üstbilgileri yanıtta mevcuttur.

![Accept-Encoding üst bilgisi ve gzip değeri ile bir isteğin sonucunu gösteren Fiddler penceresi. Değişiklik ve Içerik kodlama üstbilgileri yanıta eklenir. Yanıt sıkıştırıldı.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>Sağlayıcılarla

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli sıkıştırma sağlayıcısı

[Brotli sıkıştırılmış veri biçimiyle](https://tools.ietf.org/html/rfc7932)yanıtları sıkıştırmak içinöğesinikullanın.<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>

Hiçbir sıkıştırma sağlayıcısı açıkça öğesine <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>eklenmemişse:

* Brotli sıkıştırma sağlayıcısı, [gzip sıkıştırma sağlayıcısıyla](#gzip-compression-provider)birlikte, varsayılan olarak sıkıştırma sağlayıcılarının dizisine eklenir.
* Brotli sıkıştırılmış veri biçimi istemci tarafından desteklenerek sıkıştırma varsayılan olarak Brotli Compression. Brotli istemci tarafından desteklenmiyorsa, istemci gzip sıkıştırmasını destekliyorsa sıkıştırma varsayılan olarak gzip olur.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Herhangi bir sıkıştırma sağlayıcısı açıkça eklendiğinde, Brotoli sıkıştırma sağlayıcısının eklenmesi gerekir:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Sıkıştırma düzeyini ile <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>ayarlayın. Brotli sıkıştırma sağlayıcısı, en hızlı sıkıştırma düzeyi ([CompressionLevel. en hızlı](xref:System.IO.Compression.CompressionLevel)) olarak varsayılan olarak en verimli sıkıştırmayı oluşturmayabilir. En verimli sıkıştırma isteniyorsa, ara yazılımı en uygun sıkıştırma için yapılandırın.

| Sıkıştırma düzeyi | Açıklama |
| ----------------- | ----------- |
| [CompressionLevel. en hızlı](xref:System.IO.Compression.CompressionLevel) | Elde edilen çıktı en iyi şekilde sıkıştırısa bile, sıkıştırma mümkün olduğunca hızlı tamamlanır. |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | Sıkıştırma gerçekleştirilmemelidir. |
| [CompressionLevel. optimum](xref:System.IO.Compression.CompressionLevel) | Sıkıştırmanın tamamlanmasının daha uzun sürse bile yanıtlar en iyi şekilde sıkıştırılır. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

::: moniker-end

### <a name="gzip-compression-provider"></a>Gzip sıkıştırma sağlayıcısı

[Gzip dosya biçimiyle](https://tools.ietf.org/html/rfc1952)yanıtları sıkıştırmak içinöğesinikullanın.<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>

Hiçbir sıkıştırma sağlayıcısı açıkça öğesine <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>eklenmemişse:

::: moniker range=">= aspnetcore-2.2"

* Gzip sıkıştırma sağlayıcısı varsayılan olarak, [Brotli sıkıştırma sağlayıcısıyla](#brotli-compression-provider)birlikte sıkıştırma sağlayıcılarının dizisine eklenir.
* Brotli sıkıştırılmış veri biçimi istemci tarafından desteklenerek sıkıştırma varsayılan olarak Brotli Compression. Brotli istemci tarafından desteklenmiyorsa, istemci gzip sıkıştırmasını destekliyorsa sıkıştırma varsayılan olarak gzip olur.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Gzip sıkıştırma sağlayıcısı, varsayılan olarak sıkıştırma sağlayıcılarının dizisine eklenir.
* İstemci gzip sıkıştırmasını destekliyorsa, sıkıştırma varsayılan olarak gzip olur.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Herhangi bir sıkıştırma sağlayıcısı açık olarak eklendiğinde gzip sıkıştırma sağlayıcısının eklenmesi gerekir:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

Sıkıştırma düzeyini ile <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>ayarlayın. Gzip sıkıştırma sağlayıcısı, en hızlı sıkıştırma düzeyine ([CompressionLevel. en hızlı](xref:System.IO.Compression.CompressionLevel)) göre varsayılan olarak en verimli sıkıştırmayı oluşturmayabilir. En verimli sıkıştırma isteniyorsa, ara yazılımı en uygun sıkıştırma için yapılandırın.

| Sıkıştırma düzeyi | Açıklama |
| ----------------- | ----------- |
| [CompressionLevel. en hızlı](xref:System.IO.Compression.CompressionLevel) | Elde edilen çıktı en iyi şekilde sıkıştırısa bile, sıkıştırma mümkün olduğunca hızlı tamamlanır. |
| [CompressionLevel. NoCompression](xref:System.IO.Compression.CompressionLevel) | Sıkıştırma gerçekleştirilmemelidir. |
| [CompressionLevel. optimum](xref:System.IO.Compression.CompressionLevel) | Sıkıştırmanın tamamlanmasının daha uzun sürse bile yanıtlar en iyi şekilde sıkıştırılır. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Özel sağlayıcılar

İle <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>özel sıkıştırma uygulamaları oluşturun. , <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> Bunun`ICompressionProvider` ürettiği içerik kodlamasını temsil eder. Ara yazılım bu bilgileri, isteğin `Accept-Encoding` üstbilgisinde belirtilen listeye göre sağlayıcıyı seçmek için kullanır.

İstemci, örnek uygulamayı kullanarak `Accept-Encoding: mycustomcompression` üst bilgiyle bir istek gönderir. Ara yazılım özel sıkıştırma uygulamasını kullanır ve bir `Content-Encoding: mycustomcompression` üst bilgiyle yanıtı döndürür. Özel bir sıkıştırma uygulamasının çalışması için istemcinin özel kodlamayı açıp açabilmesi gerekir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

`Accept-Encoding: mycustomcompression` Üstbilgi ile örnek uygulamaya bir istek gönderir ve yanıt üst bilgilerini gözlemleyin. `Vary` Ve`Content-Encoding` üstbilgileri yanıtta mevcuttur. Yanıt gövdesi (gösterilmez) örnek tarafından sıkıştırılmaz. Örnek `CustomCompressionProvider` sınıfında bir sıkıştırma uygulamanız yok. Ancak örnek, bu tür bir sıkıştırma algoritmasını nerede uygulayacağınızı gösterir.

![Accept-Encoding üst bilgisi ve mycustomcompression değeri ile bir isteğin sonucunu gösteren Fiddler penceresi. Değişiklik ve Içerik kodlama üstbilgileri yanıta eklenir.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME türleri

Ara yazılım, sıkıştırma için varsayılan bir MIME türleri kümesi belirtir:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

MIME türlerini, yanıt sıkıştırma ara yazılım seçenekleriyle değiştirin veya ekleyin. Gibi joker karakter MIME türlerinin `text/*` desteklenmediğini unutmayın. Örnek uygulama, için `image/svg+xml` bir MIME türü ekler ve ASP.NET Core başlık görüntüsünü (*Banner. SVG*) sıkıştırır ve sunar.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>Güvenli protokolle sıkıştırma

Güvenli bağlantılar üzerinden sıkıştırılan yanıtlar, varsayılan olarak devre dışı `EnableForHttps` bırakılan seçeneğiyle denetlenebilir. Dinamik olarak oluşturulan sayfalarla sıkıştırma kullanmak, SUA ve [ihlal](https://wikipedia.org/wiki/BREACH_(security_exploit)) saldırıları gibi güvenlik [](https://wikipedia.org/wiki/CRIME_(security_exploit)) sorunlarına neden olabilir.

## <a name="adding-the-vary-header"></a>Vary üst bilgisi ekleniyor

`Accept-Encoding` Üstbilgileri temel alarak yanıtları sıkıştırırken, büyük olasılıkla birden çok sıkıştırılmış yanıt ve sıkıştırılmamış bir sürüm vardır. İstemci ve proxy 'nin birden çok sürümün var olduğunu ve depolanması gerektiğini bildirmek için, `Vary` üst bilgi bir `Accept-Encoding` değerle eklenir. ASP.NET Core 2,0 veya sonraki bir sürümde, yanıt sıkıştırıldığında `Vary` ara yazılım üstbilgiyi otomatik olarak ekler.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>NGINX ters proxy 'nin arkasında ara yazılım sorunu

Bir istek NGINX tarafından proxy oluşturulduğunda, `Accept-Encoding` üst bilgi kaldırılır. `Accept-Encoding` Üstbilgiyi kaldırmak, ara yazılımın yanıtı sıkıştırmasını önler. Daha fazla bilgi için bkz [. NGINX: Sıkıştırma ve açma](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Bu sorun, [NGINX (ASPNET/basicara yazılım \#123) için geçişli sıkıştırmayı](https://github.com/aspnet/BasicMiddleware/issues/123)düzenleyerek izlenir.

## <a name="working-with-iis-dynamic-compression"></a>IIS dinamik sıkıştırma ile çalışma

Bir uygulama için devre dışı bırakmak istediğiniz sunucu düzeyinde yapılandırılmış etkin bir IIS dinamik sıkıştırma modülünüzün varsa, modülü *Web. config* dosyasına ek olarak devre dışı bırakın. Daha fazla bilgi için bkz. [IIS modüllerini devre dışı bırakma](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Sorun giderme

[Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)veya [Postman](https://www.getpostman.com/)gibi bir araç kullanarak `Accept-Encoding` istek üst bilgisini ayarlamanıza ve yanıt üst bilgilerini, boyutunu ve gövdesini incelemeye olanak tanır. Varsayılan olarak, yanıt sıkıştırma ara yazılımı aşağıdaki koşullara uyan yanıtları sıkıştırır:

::: moniker range=">= aspnetcore-2.2"

* Üst bilgi,,, veya oluşturduğunuz özel `br`bir sıkıştırma `*`sağlayıcısıyla eşleşen özel bir kodlama değeri `gzip`ile birlikte bulunur. `Accept-Encoding` Değer `identity` , 0 (sıfır) olarak bir kalite değeri (qvalue, `q`) ayarı içermemelidir.
* MIME türü (`Content-Type`) ayarlanmalıdır ve <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>üzerinde yapılandırılmış bir MIME türüyle eşleşmelidir.
* İstek `Content-Range` üstbilgiyi içermemelidir.
* Yanıt sıkıştırma ara yazılımı seçeneklerinde güvenli Protokolü (https) yapılandırılmadığı takdirde istek güvenli olmayan protokol (http) kullanmalıdır. *Güvenli içerik sıkıştırması etkinleştirildiğinde [yukarıda açıklanan](#compression-with-secure-protocol) tehlikede göz önünde yer.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Üst bilgi, veya oluşturduğunuz özel bir sıkıştırma `gzip`sağlayıcısıyla `*`eşleşen özel bir kodlama değeriyle birlikte bulunur. `Accept-Encoding` Değer `identity` , 0 (sıfır) olarak bir kalite değeri (qvalue, `q`) ayarı içermemelidir.
* MIME türü (`Content-Type`) ayarlanmalıdır ve <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>üzerinde yapılandırılmış bir MIME türüyle eşleşmelidir.
* İstek `Content-Range` üstbilgiyi içermemelidir.
* Yanıt sıkıştırma ara yazılımı seçeneklerinde güvenli Protokolü (https) yapılandırılmadığı takdirde istek güvenli olmayan protokol (http) kullanmalıdır. *Güvenli içerik sıkıştırması etkinleştirildiğinde [yukarıda açıklanan](#compression-with-secure-protocol) tehlikede göz önünde yer.*

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla Geliştirici Ağı: Kabul etme-kodlama](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 Bölüm 3.1.2.1: İçerik Işbirliği](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 Bölüm 4.2.3: Gzip kodlaması](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP dosya biçimi belirtimi sürüm 4,3](https://www.ietf.org/rfc/rfc1952.txt)
