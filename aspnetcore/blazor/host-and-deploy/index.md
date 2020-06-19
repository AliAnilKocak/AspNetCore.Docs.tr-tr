---
title: ASP.NET Core barındırma ve dağıtmaBlazor
author: guardrex
description: Uygulamaları nasıl barındırılacağını ve dağıtacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 3a3c5ab5365e5b4312dd3fd516f4906155911cc9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103817"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>ASP.NET Core barındırma ve dağıtmaBlazor

, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından

## <a name="publish-the-app"></a>Uygulamayı yayımlama

Uygulamalar yayın yapılandırmasında dağıtım için yayımlanır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Gezinti çubuğundan **Build**  >  **Publish {APPLICATION}** öğesini seçin.
1. *Yayımla hedefini*seçin. Yerel olarak yayımlamak için **klasör**' ü seçin.
1. **Klasör seçin** alanında varsayılan konumu kabul edin veya farklı bir konum belirtin. **Yayımla** düğmesini seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Derleme**  >  **yayımlama klasörü**' nü seçin.
1. Yayınlanan varlıkların alınacağı klasörü onaylayın ve **Yayımla**' yı seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Uygulamayı bir sürüm yapılandırmasıyla yayımlamak için [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu kullanın:

```dotnetcli
dotnet publish -c Release
```

---

Uygulamanın yayımlanması, projenin bağımlılıklarını [geri yüklemeyi](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur](/dotnet/core/tools/dotnet-build) . Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan Yöntemler ve derlemeler kaldırılır.

Yayımlama konumları:

* BlazorWebAssembly
  * Tek başına: uygulama */BIN/Release/{Target Framework}/Publish/Wwwroot* klasörüne yayımlanır. Uygulamayı statik bir site olarak dağıtmak için *Wwwroot* klasörünün içeriğini statik site konağına kopyalayın.
  * Barındırılan: istemci Blazor webassembly uygulaması sunucu uygulamasının */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe, sunucu uygulamasının diğer statik Web varlıklarıyla birlikte yayımlanır. *Yayımla* klasörünün içeriğini konağa dağıtın.
* BlazorSunucu: uygulama */BIN/Release/{Target Framework}/Publish* klasörüne yayımlandı. *Yayımla* klasörünün içeriğini konağa dağıtın.

Klasördeki varlıklar Web sunucusuna dağıtılır. Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir süreç olabilir.

## <a name="app-base-path"></a>Uygulama temel yolu

*Uygulama temel yolu* , UYGULAMANıN kök URL yoludur. Aşağıdaki ASP.NET Core uygulamayı ve alt uygulamayı göz önünde bulundurun Blazor :

* ASP.NET Core uygulaması şu şekilde adlandırılır `MyApp` :
  * Uygulama fiziksel olarak *d:/MyApp*konumunda bulunur.
  * İstekleri tarihinde alınır `https://www.contoso.com/{MYAPP RESOURCE}` .
* BlazorAdlı bir uygulama `CoolApp` , öğesinin bir alt uygulamasıdır `MyApp` :
  * Alt uygulama fiziksel olarak *d:/MyApp/CoolApp*konumunda bulunur.
  * İstekleri tarihinde alınır `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

İçin ek yapılandırma belirtmeden `CoolApp` , Bu senaryodaki alt uygulama, sunucuda nerede bulunduğu konusunda bilgi sahibi değildir. Örneğin, uygulama ilgili URL yolunda bulunduğunu bilmeden kaynaklarına doğru göreli URL 'Ler oluşturamıyoruz `/CoolApp/` .

Uygulamanın temel yolu için yapılandırma sağlamak üzere Blazor `https://www.contoso.com/CoolApp/` , `<base>` etiketin `href` özniteliği *sayfa/_Host. cshtml* dosyası ( Blazor sunucu) veya *Wwwroot/index.html* dosyasındaki ( Blazor webassembly) göreli kök yoluna ayarlanır:

```html
<base href="/CoolApp/">
```

BlazorSunucu uygulamaları Ayrıca, <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> uygulamanın istek ardışık düzeninde çağırarak sunucu tarafı taban yolunu ayarlar `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL 'Ler oluşturabilir. Farklı dizin yapısı düzeylerindeki bileşenler, uygulama genelinde konumlardaki diğer kaynakların bağlantılarını oluşturabilir. Uygulama temel yolu, `href` bağlantının hedefinin uygulama temel yolu URI alanı içinde olduğu seçili köprüleri ele almak için de kullanılır. BlazorYönlendirici iç gezinmeyi işler.

Birçok barındırma senaryosunda, uygulamanın göreli URL yolu uygulamanın köküdür. Bu durumlarda, uygulamanın göreli URL taban yolu `<base href="/" />` , bir uygulamanın varsayılan yapılandırması olan bir eğik çizgi () olur Blazor . GitHub sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu, sunucunun uygulamanın göreli URL 'SI yolu olarak ayarlanmalıdır.

Uygulamanın temel yolunu ayarlamak için, `<base>` `<head>` *sayfa/_Host. cshtml* dosyası ( Blazor sunucu) veya *Wwwroot/index.html* dosyasının (webassembly) etiket öğeleri içindeki etiketi güncelleştirin Blazor . `href`Öznitelik değerini olarak ayarlayın `/{RELATIVE URL PATH}/` (sondaki eğik çizgi gereklidir), burada `{RELATIVE URL PATH}` UYGULAMANıN tam göreli URL yoludur.

BlazorKök olmayan GÖRELI URL yoluna (örneğin,) sahip bir webassembly uygulaması için `<base href="/CoolApp/">` , uygulama *yerel olarak çalıştırıldığında*kaynaklarını bulamaz. Yerel geliştirme ve test sırasında bu sorunu aşmak için, *path base* `href` `<base>` çalışma zamanında etiketinin değeriyle eşleşen bir yol temel bağımsız değişkeni sağlayabilirsiniz. Sondaki eğik çizgi eklemeyin. Uygulamayı yerel olarak çalıştırırken yol temel bağımsız değişkenini geçirmek için, `dotnet run` komutu uygulamanın dizininden çalıştırın, `--pathbase` seçeneği:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

BlazorGÖRELI URL yolu () olan bir webassembly uygulaması için `/CoolApp/` , `<base href="/CoolApp/">` komut şu şekilde olur:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

BlazorWebassembly uygulaması tarihinde yerel olarak yanıt verir `http://localhost:port/CoolApp` .

## <a name="deployment"></a>Dağıtım

Dağıtım Kılavuzu için aşağıdaki konulara bakın:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
