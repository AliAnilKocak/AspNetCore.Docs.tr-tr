---
title: ASP.NET Core statik varlıkları paketleyin ve azın
author: scottaddie
description: Bir ASP.NET Core Web uygulamasındaki statik kaynakları paketleme ve küçültmeye yönelik teknikleri uygulayarak nasıl iyileştirileyeceğinizi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: 4523ba299d5c5e50a442f84acadf06bf57c69c5d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770959"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core statik varlıkları paketleyin ve azın

[Scott Ade](https://twitter.com/Scott_Addie) ve [David çam](https://twitter.com/davidpine7) tarafından

Bu makalede, bu özelliklerin ASP.NET Core Web uygulamalarıyla nasıl kullanılabileceği da dahil olmak üzere paketleme ve küçültmeye yönelik uygulama avantajları açıklanmaktadır.

## <a name="what-is-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre

Paketleme ve küçültme, bir Web uygulamasında uygulayabileceğiniz iki ayrı performans iyileştirmesidir. Birlikte kullanıldığında, paketleme ve minbirleştirime, sunucu isteklerinin sayısını azaltarak ve istenen statik varlıkların boyutunu azaltarak performansı geliştirir.

Paketleme ve minbirleştirmesi birincil olarak ilk sayfa isteği yükleme süresini geliştirir. Bir Web sayfası istendiğinde, tarayıcı statik varlıkları (JavaScript, CSS ve görüntüler) önbelleğe alır. Sonuç olarak, aynı sayfada veya sayfalarda aynı varlıkları talep eden aynı siteye veya sayfalara istekte bulunduğunda, paketleme ve minlıme performansı artırmayın. Süre sonu üst bilgisi varlıklarda doğru ayarlanmamışsa ve paketleme ve küçültme kullanılmıyorsa, tarayıcının yenilik buluşsal yöntemleri, varlıkları birkaç günden daha eski bir süre sonra işaretler. Ayrıca, tarayıcı her varlık için bir doğrulama isteği gerektirir. Bu durumda, paketleme ve minbirleşme, ilk sayfa isteğinden sonra bile bir performans geliştirmesi sağlar.

### <a name="bundling"></a>Paketleme

Paketleme, birden çok dosyayı tek bir dosya halinde birleştirir. Paketleme, Web sayfası gibi bir Web varlığını işlemek için gerekli olan sunucu isteği sayısını azaltır. Özellikle CSS, JavaScript vb. için istediğiniz sayıda ayrı paket oluşturabilirsiniz. Daha az dosya tarayıcıdan sunucuya veya uygulamanızı sağlayan hizmetten daha az HTTP isteğinin olması anlamına gelir. Bu, geliştirilmiş ilk sayfa yükü performansına neden olur.

### <a name="minification"></a>Küçültme

Minbirleşme işlevleri işlevselliği değiştirmeden koddan gereksiz karakterleri kaldırır. Sonuç, istenen varlıklarda (CSS, görüntüler ve JavaScript dosyaları gibi) önemli bir boyut azalmasıyla sonuçlanır. Yaygın olarak kullanılan yan etkileri, değişken adlarını tek bir karaktere kısaltmayı ve açıklamaları ve gereksiz boşlukları kaldırmayı içerir.

Aşağıdaki JavaScript işlevini göz önünde bulundurun:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

Minbirleşme işlevi şu şekilde azaltır:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

Açıklamaları ve gereksiz boşlukları kaldırmanın yanı sıra aşağıdaki parametre ve değişken adları şu şekilde yeniden adlandırıldı:

Özgün | İsim
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Paketleme ve küçültmeye yönelik etkileri

Aşağıdaki tabloda, tek tek yükleme varlıkları ve paketleme ve küçültme kullanımı arasındaki farklılıklar özetlenmektedir:

Eylem | B/M ile | B/M olmadan | Değiştir
--- | :---: | :---: | :---:
Dosya Istekleri  | 7   | 18     | %157
KB aktarıldı | 156 | 264,68 | %70
Yükleme süresi (MS) | 885 | 2360   | %167

Tarayıcılar HTTP istek üst bilgileriyle ilgili oldukça ayrıntılıdır. Gönderilen toplam bayt ölçümü, paketleme sırasında önemli bir düşüş gördük. Yükleme zamanı önemli bir geliştirme gösterir, ancak bu örnek yerel olarak çalışır. Ağ üzerinden aktarılan varlıklarla paketleme ve küçültmeye karşı kullanım sırasında daha fazla performans artışı gerçekleştirilir.

## <a name="choose-a-bundling-and-minification-strategy"></a>Bir paketleme ve küçültmeye karşı bir strateji seçin

MVC ve Razor Pages proje şablonları, bir JSON yapılandırma dosyasından oluşan paketleme ve küçültmeye yönelik bir çözüm sağlar. [Grdalar](xref:client-side/using-grunt) görev Çalıştırıcısı gibi üçüncü taraf araçlar, aynı görevleri biraz daha karmaşıklıkla yerine getirmiş. Geliştirme iş akışınız, bağlama ve görüntü iyileştirmesi&mdash;gibi paket oluşturma ve küçültmeye karşı işleme gerektirdiğinde, üçüncü taraf bir araç harika bir araçtır. Tasarım zamanı paketleme ve küçültme kullanarak, küçültülmüş dosyalar uygulamanın dağıtımından önce oluşturulur. Dağıtımdan önce paketleme ve küçültme, azaltılmış sunucu yükünün avantajlarından faydalanabilmenizi sağlar. Bununla birlikte, tasarım zamanı paketleme ve küçültme, derleme karmaşıklığını artırır ve yalnızca statik dosyalarla birlikte kullanılabilir.

## <a name="configure-bundling-and-minification"></a>Paketlemeyi ve küçültmeye göre yapılandırma

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core 2,0 veya önceki sürümlerde, MVC ve Razor Pages proje şablonları her bir paket için seçenekleri tanımlayan bir *paketleme liconfig. JSON* yapılandırma dosyası sağlar:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2,1 veya sonraki sürümlerde, MVC veya Razor Pages proje köküne *paketleme liconfig. JSON*adlı yenı bir JSON dosyası ekleyin. Aşağıdaki JSON 'yi bir başlangıç noktası olarak bu dosyaya ekleyin:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*Paketleme liconfig. JSON* dosyası her bir paket için seçenekleri tanımlar. Yukarıdaki örnekte, özel JavaScript (*Wwwroot/js/site. js*) ve stil sayfası (*Wwwroot/CSS/site. css*) dosyaları için tek bir paket yapılandırması tanımlanmıştır.

Yapılandırma seçenekleri şunlardır:

* `outputFileName`: Çıkış yapılacak paket dosyasının adı. , *Paketleme liconfig. JSON* dosyasından göreli bir yol içerebilir. **Gerekli**
* `inputFiles`: Birlikte paketedilecek dosya dizisi. Bunlar yapılandırma dosyasına yönelik göreli yollardır. **isteğe bağlı*** boş bir değer boş bir çıktı dosyasıyla sonuçlanır. [Glob](https://www.tldp.org/LDP/abs/html/globbingref.html) desenleri desteklenir.
* `minify`: Çıkış türü için minbirleşme seçenekleri. **isteğe bağlı**, *varsayılan `minify: { enabled: true }` -*
  * Yapılandırma seçenekleri çıkış dosyası türü başına kullanılabilir.
    * [CSS minifier](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript minifier](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML minifier](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Oluşturulan dosyalar proje dosyasına eklenip eklenmeyeceğini belirten bayrak. **isteğe bağlı**, *Varsayılan-yanlış*
* `sourceMap`: Paketlenmiş dosya için bir kaynak eşlemesi oluşturulup oluşturulmayacağını belirten bayrak. **isteğe bağlı**, *Varsayılan-yanlış*
* `sourceMapRootPath`: Oluşturulan kaynak eşleme dosyasını depolamak için kök yolu.

## <a name="add-files-to-workflow"></a>İş akışına dosya ekleme

Aşağıdakilere benzer bir ek *özel. css* dosyası eklendiğini bir örnek düşünün:

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

*Custom. css* dosyasını küçültmeye ve *site. css* ' yi bir *site. min. css* dosyasına paketlemek için, ilgili yolu *paketleme liconfig. JSON*öğesine ekleyin:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Alternatif olarak, aşağıdaki glob deseninin kullanılması gerekir:
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Bu glob model tüm CSS dosyalarıyla eşleşir ve küçültülmüş dosya modelini dışlar.

Uygulamayı derleyin. *Site. min. css* dosyasını açın ve *Custom. css* içeriğinin dosyanın sonuna ekleneceğini unutmayın.

## <a name="environment-based-bundling-and-minification"></a>Ortam tabanlı paketleme ve minbirleşme

En iyi uygulama olarak, uygulamanızın paketlenmiş ve küçültülmüş dosyaları bir üretim ortamında kullanılmalıdır. Geliştirme sırasında, özgün dosyalar uygulamanın hata ayıklamasını daha kolay hale getirir.

Görünümlerinizde [ortam etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) kullanarak sayfalarınıza hangi dosyaların ekleneceğini belirleyin. Ortam etiketi Yardımcısı yalnızca belirli [ortamlarda](xref:fundamentals/environments)çalışırken içeriğini işler.

Aşağıdaki `environment` etiket, `Development` ortamda çalışırken işlenmemiş CSS dosyalarını işler:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

Aşağıdaki `environment` etiket, dışında `Development`bir ortamda çalışırken paketlenmiş ve küçültülmüş CSS dosyalarını işler. Örneğin, içinde `Production` çalışan veya `Staging` bu stil sayfalarını işlemeyi tetikleyen:

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Gulp adresinden paketleme liconfig. JSON kullanın

Uygulamanın paketleme ve küçültmeye yönelik iş akışının ek işlem gerektirdiği durumlar vardır. Örnek görüntü iyileştirmesi, önbellek performansı ve CDN varlık işleme sayılabilir. Bu gereksinimleri karşılamak için, paketleme ve küçültmeye yönelik iş akışını Gulp kullanacak şekilde dönüştürebilirsiniz.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Gulp kullanmak için paketleme ve küçültmeye yönelik iş akışını el ile dönüştürün

Aşağıdaki `devDependencies`gibi, proje köküne bir *Package. JSON* dosyası ekleyin:

> [!WARNING]
> `gulp-uglify` Modül ECMASCRIPT (ES) 2015/ES6 ve üstünü desteklemez. ES2015/ES6 veya üstünü kullanmak `gulp-uglify` için yerine [Gulp-Terser](https://www.npmjs.com/package/gulp-terser) 'yi yükler.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Aşağıdaki komutu *Package. JSON*ile aynı düzeyde çalıştırarak bağımlılıkları yükler:

```console
npm i
```

Gulp CLı 'yı genel bağımlılık olarak yükler:

```console
npm i -g gulp-cli
```

Aşağıdaki *gulpfile. js* dosyasını proje köküne kopyalayın:

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Gulp görevlerini Çalıştır

Proje Visual Studio 'da yapılandırmadan önce Gulp minbirleşme görevini tetiklemek için, *. csproj dosyasına aşağıdaki [MSBuild hedefini](/visualstudio/msbuild/msbuild-targets) ekleyin:

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Bu örnekte, `MyPreCompileTarget` hedef içinde tanımlanan tüm görevler önceden tanımlanmış `Build` hedeften önce çalışır. Visual Studio 'nun çıkış penceresinde aşağıdakine benzer bir çıktı görüntülenir:

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Grunt kullanma](xref:client-side/using-grunt)
* [Birden çok ortam kullanma](xref:fundamentals/environments)
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
