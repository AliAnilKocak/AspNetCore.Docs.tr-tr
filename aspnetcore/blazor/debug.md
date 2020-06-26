---
title: Hata ayıklama ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Uygulamalarda hata ayıklamayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9fe51b8c7eafdd62cc6fc1a820135d9ee5ff010e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401018"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>Hata ayıklama ASP.NET CoreBlazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssemblyuygulamalar, Kmıum tabanlı tarayıcılarda (Edge/Chrome) tarayıcı geliştirme araçları kullanılarak ayıklanamaz. Alternatif olarak, Visual Studio veya Visual Studio Code kullanarak uygulamanızda hata ayıklaması yapabilirsiniz.

Kullanılabilir senaryolar şunlardır:

* Kesme noktaları ayarlayın ve kaldırın.
* Visual Studio 'da hata ayıklama desteğiyle uygulamayı çalıştırın ve Visual Studio Code (<kbd>F5</kbd> support).
* Kod üzerinden tek adımlı (<kbd>F10</kbd>).
* Visual Studio veya Visual Studio Code 'de <kbd>F8</kbd> ile kod yürütmeyi bir tarayıcıda veya <kbd>F5</kbd> ile sürdürebilirsiniz.
* *Yereller* görünümü ' nde yerel değişkenlerin değerlerini gözlemleyin.
* JavaScript 'ten .NET 'e ve .NET 'ten JavaScript 'e gidecek çağrı zincirleri dahil olmak üzere çağrı yığınına bakın.

Şimdilik şunları *yapamazsınız*:

* İşlenmemiş özel durumların üzerine bölün.
* Uygulamanın başlatılması sırasında isabet kesme noktaları.

Yaklaşan sürümlerde hata ayıklama deneyimini iyileştirmeye devam edeceğiz.

## <a name="prerequisites"></a>Ön koşullar

Hata ayıklama aşağıdaki tarayıcılardan birini gerektirir:

* Microsoft Edge (sürüm 80 veya üzeri)
* Google Chrome (sürüm 70 veya üzeri)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Visual Studio ve Visual Studio Code için hata ayıklamayı etkinleştir

Mevcut bir uygulamada hata ayıklamayı etkinleştirmek için Blazor WebAssembly , `launchSettings.json` Başlangıç projesindeki dosyayı her bir başlatma profiline aşağıdaki özelliği içerecek şekilde güncelleştirin `inspectUri` :

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Dosya güncelleştirildikten sonra, `launchSettings.json` aşağıdaki örneğe benzer şekilde görünür:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri`Özelliği:

* IDE 'nin uygulamanın bir uygulama olduğunu algılamasını sağlar Blazor WebAssembly .
* Betik hata ayıklama altyapısına, Blazor hata ayıklama proxy 'si aracılığıyla tarayıcıya bağlanmasını söyler.

`wsProtocol`Başlatılan tarayıcıda () WebSockets Protokolü (), ana bilgisayar ( `url.hostname` ), bağlantı noktası () `url.port` ve Inspector URI 'si için yer tutucu değerleri `browserInspectUri` , Framework tarafından sağlanır.

## <a name="visual-studio"></a>Visual Studio

Blazor WebAssemblyVisual Studio 'da bir uygulamada hata ayıklamak için:

1. Yeni ASP.NET Core barındırılan bir Blazor WebAssembly uygulama oluşturun.
1. Uygulamayı hata ayıklayıcıda çalıştırmak için <kbd>F5</kbd> tuşuna basın.
1. Metodunda bir kesme noktası ayarlayın `Pages/Counter.razor` `IncrementCount` .
1. **`Counter`** Sekmesine gidin ve kesme noktasına isabet eden düğmeyi seçin:

   ![Hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. `currentCount`Yereller penceresindeki alanın değerini gözden geçirin:

   ![Yerelleri görüntüle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın.

Uygulamanızda hata ayıklarken Blazor WebAssembly , sunucu kodunuzda hata ayıklaması de yapabilirsiniz:

1. İçindeki sayfada bir kesme noktası ayarlayın `Pages/FetchData.razor` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
1. Eylem yönteminde içinde bir kesme noktası ayarlayın `WeatherForecastController` `Get` .
1. **`Fetch Data`** `FetchData` Bir http isteğini sunucuya vermeden önce, bileşendeki ilk kesme noktasına isabet etmek için sekmeye gidin:

   ![Veri getirme verilerini ayıklama](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve ardından sunucusundaki kesme noktasına gidin `WeatherForecastController` :

   ![Hata ayıklama sunucusu](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Yürütmeye devam etmek için <kbd>F5</kbd> tuşuna basın ve hava durumu tahmin tablosunun işlenmiş olduğunu görün.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code bir uygulamada hata ayıklamak için Blazor WebAssembly :
 
[C# uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` olarak ayarlandığı şekilde yükler `true` .

![Uzantıları](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![JS önizleme hata ayıklayıcısı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Tek başına hata ayıklaBlazor WebAssembly

1. Tek başına Blazor WebAssembly uygulamayı vs Code açın.

   Aşağıdaki bildirimi, hata ayıklamayı etkinleştirmek için ek kurulumun gerekli olduğunu alırsanız:
   
   * Doğru uzantıların yüklü olduğunu doğrulayın.
   * JavaScript önizlemesi hata ayıklamanın etkinleştirildiğini doğrulayın.
   * Pencereyi yeniden yükleyin.

   ![Ek kurulum gerekli](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <kbd>F5</kbd> klavye kısayolunu veya menü öğesini kullanarak hata ayıklamayı başlatın.

1. İstendiğinde, hata ayıklamayı başlatmak için ** Blazor WebAssembly Hata Ayıkla** seçeneğini belirleyin.

   ![Kullanılabilir hata ayıklama seçeneklerinin listesi](index/_static/blazor-vscode-debugtypes.png)

1. Tek başına uygulama başlatılır ve bir hata ayıklama tarayıcısı açılır.

1. Bileşendeki yöntemde bir kesme noktası ayarlayın `IncrementCount` `Counter` ve ardından kesme noktasına isabet eden düğmeyi seçin:

   ![VS Code hata ayıklama sayacı](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>Barındırılan hata ayıklamaBlazor WebAssembly

1. Barındırılan Blazor WebAssembly uygulamayı vs Code açın.

1. Proje için bir başlatma yapılandırma kümesi yoksa, aşağıdaki bildirim görüntülenir. **Evet**' i seçin.

   ![Gerekli varlıkları Ekle](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Seçim penceresinde barındırılan çözüm içinde *sunucu* projesini seçin.

`launch.json`Hata ayıklayıcıyı başlatmak için başlatma yapılandırması ile bir dosya oluşturulur.

### <a name="attach-to-an-existing-debugging-session"></a>Varolan bir hata ayıklama oturumuna Ekle

Çalışan bir uygulamaya eklemek için Blazor `launch.json` aşağıdaki yapılandırmaya sahip bir dosya oluşturun:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Bir hata ayıklama oturumuna iliştirme yalnızca tek başına uygulamalar için desteklenir. Tam yığın hata ayıklamayı kullanmak için uygulamayı VS Code başlatmanız gerekir.

### <a name="launch-configuration-options"></a>Yapılandırma seçeneklerini Başlat

Hata ayıklama türü için aşağıdaki başlatma yapılandırma seçenekleri desteklenir `blazorwasm` .

| Seçenek    | Açıklama |
| --------- | ----------- |
| `request` | `launch`Bir uygulamaya hata ayıklama oturumu başlatmak ve eklemek Blazor WebAssembly veya `attach` zaten çalışan bir uygulamaya hata ayıklama oturumu eklemek için kullanın. |
| `url`     | Hata ayıklanırken tarayıcıda açılacak URL. Varsayılan olarak olur `https://localhost:5001` . |
| `browser` | Hata ayıklama oturumu için başlatılacak tarayıcı. Ayarlanan `edge` veya `chrome`. Varsayılan olarak olur `chrome` . |
| `trace`   | JS hata ayıklayıcısından Günlükler oluşturmak için kullanılır. `true`Günlük oluşturmak için olarak ayarlayın. |
| `hosted`  | `true`Barındırılan bir uygulama başlatılırken ve hata ayıklandığında olarak ayarlanmalıdır Blazor WebAssembly . |
| `webRoot` | Web sunucusunun mutlak yolunu belirtir. Bir uygulama bir alt rotadan sunulduysa ayarlanmalıdır. |
| `timeout` | Hata ayıklama oturumunun eklenmesi için beklenecek milisaniye sayısı. Varsayılan değer 30.000 milisaniyedir (30 saniye). |
| `program` | Barındırılan uygulama sunucusunu çalıştırmak için çalıştırılabilir dosyaya bir başvuru. `hosted`İse ayarlanmalıdır `true` . |
| `cwd`     | Üzerinde uygulamayı başlatmak için çalışma dizini. `hosted`İse ayarlanmalıdır `true` . |
| `env`     | Başlatılan işleme sağlanacak ortam değişkenleri. Yalnızca `hosted` , olarak ayarlandıysa geçerlidir `true` . |

### <a name="example-launch-configurations"></a>Örnek başlatma yapılandırması

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>Tek başına bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Belirtilen URL 'de çalışan bir uygulamaya iliştirme

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>Barındırılan bir uygulamayı başlatma ve hata ayıklama Blazor WebAssembly

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>Tarayıcıda hata ayıkla

1. Geliştirme ortamında uygulamanın hata ayıklama derlemesini çalıştırın.

1. <kbd>SHIFT</kbd> + <kbd>alt</kbd> + <kbd>D</kbd>tuşuna basın.

1. Tarayıcı, uzaktan hata ayıklama etkinken çalıştırılmalıdır. Uzaktan hata ayıklama devre dışıysa, **hata ayıklanabilir Browser sekmesi** hata sayfası oluşturulur. Hata sayfası, hata ayıklama Blazor proxy 'sinin uygulamaya bağlanabilmesi için hata ayıklama bağlantı noktası açıkken tarayıcıyı çalıştırmaya yönelik yönergeler içerir. *Tüm tarayıcı örneklerini kapatın* ve belirtildiği şekilde tarayıcıyı yeniden başlatın.

Tarayıcı uzaktan hata ayıklama etkinken çalışırken hata ayıklama klavye kısayolu yeni bir hata ayıklayıcı sekmesi açar. Bir süre sonra, **kaynaklar** sekmesi uygulamadaki .net derlemelerinin listesini gösterir. Her bir derlemeyi genişletin ve `.cs` / `.razor` hata ayıklama için kullanılabilir kaynak dosyaları bulun. Kesme noktaları ayarlayın, uygulamanın sekmesine geri dönün ve kod yürütüldüğünde kesme noktaları isabet edilir. Kesme noktası isabet ettikten sonra, kod üzerinden tek adımlı (<kbd>F10</kbd><kbd>) ve</kbd>kod yürütme işlemini normal şekilde yapın.

Blazor[Chrome DevTools protokolünü](https://chromedevtools.github.io/devtools-protocol/) uygulayan ve protokolünü ile genişlettiğini içeren bir hata ayıklama proxy 'si sağlar. NET 'e özgü bilgiler. Klavye kısayoluna hata ayıklama basıldığında, Blazor Ara sunucu üzerindeki Chrome DevTools ' ı işaret eder. Proxy, hata ayıklama işlemini Aradığınız tarayıcı penceresine bağlanır (Bu nedenle, uzaktan hata ayıklamayı etkinleştirmeniz gerekir).

## <a name="browser-source-maps"></a>Tarayıcı kaynağı eşlemeleri

Tarayıcı kaynak haritaları tarayıcının derlenmiş dosyaları özgün kaynak dosyalarına geri eşlemesine ve istemci tarafı hata ayıklama için yaygın olarak kullanılmasına izin verir. Ancak, Blazor Şu anda C# ' yi doğrudan JavaScript/te olarak eşleştirmez. Bunun yerine, Blazor tarayıcı IÇINDE Il yorumu yapar, bu nedenle kaynak haritaları ilgili değildir.

## <a name="troubleshoot"></a>Sorun giderme

Hatalar halinde çalıştırıyorsanız, aşağıdaki ipuçları yardımcı olabilir:

* **Hata ayıklayıcı** sekmesinde, tarayıcınızda Geliştirici Araçları ' nı açın. Konsolunda, `localStorage.clear()` tüm kesme noktalarını kaldırmak için yürütün.
* ASP.NET Core HTTPS geliştirme sertifikasını yüklediğinizden ve güvendiğini doğrulayın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio, **ASP.NET için JavaScript hata ayıklamasını etkinleştir (Chrome, Edge ve IE)** seçeneği için **araç**  >  **seçeneklerinde**  >  **hata ayıklama**  >  **genel**' de gereklidir. Bu, Visual Studio için varsayılan ayardır. Hata ayıklama çalışmıyorsa, seçeneğinin seçili olduğundan emin olun.
