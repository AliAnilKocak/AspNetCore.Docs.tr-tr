---
title: ASP.NET Core Blazor hata ayıklama
author: guardrex
description: Blazor uygulamalarında hata ayıklama hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/debug
ms.openlocfilehash: 6d71296417c57f01e675bdbb31a0d4fe2fd7db63
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500431"
---
# <a name="debug-aspnet-core-blazor"></a>ASP.NET Core Blazor hata ayıklama

[Daniel Roth](https://github.com/danroth27)

*Erken* destek var. WebAssembly chrome'da çalışan Blazor istemci-tarafı uygulamalarındaki hataları ayıklamak için.

Hata ayıklayıcı özellikleri sınırlıdır. Kullanılabilir senaryolar şunlardır:

* Ayarlayın ve kesme noktalarını kaldırın.
* Tek adımlı (`F10`) aracılığıyla kod veya sürdürme (`F8`) kod yürütme.
* İçinde *Yereller* görüntülemek, tür yerel değişkenlerin değerlerini gözlemleyin `int`, `string`, ve `bool`.
* .NET içinde JavaScript ve .NET, JavaScript için Git çağrı zincirleri dahil olmak üzere çağrı yığını bakın.

*Olamaz*:

* Değerlerin değil herhangi bir yerel öğeler gözlemleyin bir `int`, `string`, veya `bool`.
* Herhangi bir sınıf özelliklerini veya alanlarını değerlerini gözlemleyin.
* Değerleri görmek için değişkenleri gelin.
* Konsolunda ifadeleri değerlendirin.
* Zaman uyumsuz çağrıları boyunca ilerleyin.
* Çoğu diğer normal hata ayıklama senaryoları gerçekleştirin.

Daha fazla hata ayıklama senaryoları biri geliştirme bir devam eden mühendislik ekibinin biridir.

## <a name="procedure"></a>Yordam

Chrome Blazor istemci-tarafı uygulamada hata ayıklamak için:

* Blazor uygulama yapı içinde `Debug` yapılandırma (yayımlanmamış uygulamalar için varsayılan).
* (Sürüm 70 veya sonrası) Chrome'da Blazor uygulamayı çalıştırın.
* Uygulamasında (daha az karmaşık bir hata ayıklama deneyimi için büyük olasılıkla kapatmalısınız değil geliştirici araçları paneli,) klavye odağı ile aşağıdaki Blazor özgü klavye kısayolunu seçin:
  * `Shift+Alt+D` Windows/Linux üzerinde
  * `Shift+Cmd+D` MacOS üzerinde

## <a name="enable-remote-debugging"></a>Uzaktan hata ayıklamayı etkinleştirme

Uzaktan hata ayıklama devre dışı ise bir **hata ayıklanabilir tarayıcı sekmesinde bulunamıyor** hata sayfası, Chrome tarafından oluşturulur. Hata sayfası Blazor hata ayıklama proxy'si uygulamasına bağlanabilmesi Chrome ile hata ayıklama bağlantı noktası açık çalıştırmak için yönergeler içerir. *Tüm Chrome örneklerini kapatın* ve Chrome belirtildiği gibi yeniden başlatın.

## <a name="debug-the-app"></a>Uygulamasında hata ayıklama

Uzaktan hata ayıklama etkin olan Chrome çalıştırıldıktan sonra hata ayıklama klavye kısayolunu yeni bir hata ayıklayıcı sekmesi açılır. Kısa bir süre sonra **kaynakları** sekmesi, uygulamada .NET derlemelerin bir listesini gösterir. Her derleme genişletin ve bulma *.cs*/ *.razor* kaynak dosyaları, hata ayıklama için kullanılabilir. Kod yürütüldüğünde kesme noktaları belirleyin, uygulamanın sekmesine dönün ve kesme noktaları isabet. Sonra bir kesme noktası İsabeti, tek adımlı olduğu (`F10`) aracılığıyla kod veya sürdürme (`F8`) yürütme normalde kod.

Blazor uygulayan bir hata ayıklama proxy'si sağlar [Chrome DevTools Protokolü](https://chromedevtools.github.io/devtools-protocol/) ve protokolü ile artırmaktadır. NET özgü bilgileri. Hata ayıklama klavye kısayolu basıldığında Blazor Chrome DevTools proxy işaret eder. Hata ayıklamak için aradığınız tarayıcı penceresine proxy bağlar (Bu nedenle uzaktan hata ayıklamayı etkinleştirmek için gereken).

## <a name="browser-source-maps"></a>Tarayıcı kaynak eşlemeleri

Tarayıcı kaynak eşlemeleri derlenmiş dosyalar geri orijinal kaynak dosyalarına eşlemek tarayıcısına izin ver ve istemci tarafı hata ayıklama için yaygın olarak kullanılır. Ancak, Blazor şu anda eşlemiyor C# JavaScript/WASM için doğrudan. Bunun yerine, kaynak eşlemeleri uygun olmayan şekilde Blazor IL yorumlayıcısı içinde tarayıcı yapar.

## <a name="troubleshooting-tip"></a>Sorun giderme İpucu

Hatalarla karşılaşırsanız çalıştırıyorsanız, aşağıdaki ipucu yardımcı olabilir:

İçinde **hata ayıklayıcı** sekmesinde, tarayıcınızda geliştirici araçlarını açın. Konsolda, yürütme `localStorage.clear()` tüm kesme noktalarını kaldırmak için.
