---
title: Razor bileşenleri düzenleri
author: guardrex
description: Razor bileşenleri uygulamalar için yeniden kullanılabilir Düzen bileşenlerinin nasıl oluşturulacağını öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/layouts
ms.openlocfilehash: 31ed940ce40e3ae6e3744418cf241d396308f4fe
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068112"
---
# <a name="razor-components-layouts"></a>Razor bileşenleri düzenleri

By [Rainer Stropek](https://www.timecockpit.com)

Uygulamalar genellikle birden fazla bileşeni içerir. Düzen öğeleri, logolar, menüler ve telif hakkı iletiler gibi tüm bileşenlerini bulunması gerekir. Tüm uygulama bileşenlerinin bu düzen öğelerin kodunu kopyalama, etkili bir yaklaşım değildir. Böyle çoğaltma korumak zor olabilir ve büyük olasılıkla zaman içinde tutarsız içeriği yol açar. *Düzenleri* bu sorunu çözer.

Teknik olarak, bir düzen başka bir bileşendir. Bir düzen Razor şablonu veya tanımlanan C# içerebilir ve kod [veri bağlama](xref:razor-components/components#data-binding), [bağımlılık ekleme](xref:razor-components/dependency-injection)ve diğer bileşenleri sıradan özellikleri.

İki ek yönleri etkinleştirmek bir *bileşen* içine bir *düzeni*

* Düzen bileşen devralmalıdır `LayoutComponentBase`. `LayoutComponentBase` tanımlayan bir `Body` içinde düzenini işlenmek üzere içeriği özelliği.
* Düzen bileşen `Body` özelliği gövde içeriği olması gereken yerde belirtmek için Razor sözdizimi kullanılarak oluşturulması `@Body`. İşleme sırasında `@Body` düzeni içerik ile değiştirilir.

Aşağıdaki kod örneği, Razor şablonu Düzen bileşeninin gösterir. Kullanımına dikkat edin `LayoutComponentBase` ve `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a>Bir bileşenin bir düzen kullanın

Razor yönergesi kullanan `@layout` bileşene bir düzen uygulamak için. Bu yönerge içine derleyici dönüştürür bir `LayoutAttribute`, bileşen sınıfa uygulanır.

Aşağıdaki kod örneği kavramı gösterir. Bu bileşen içeriği eklendiği *MasterLayout* konumunda `@Body`:

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a>Merkezi Yerleşim Seçimi

Her bir klasör, bir uygulama, isteğe bağlı olarak adlandırılmış bir şablon dosyası içerebilir *_viewımports.cshtml*. Derleyici, Razor şablonları aynı klasörde yer alan ve yinelemeli olarak tüm alt klasörlerindeki tüm görünümü içeri aktarmalar dosyasında belirtilen yönergeleri içerir. Bu nedenle, bir *_viewımports.cshtml* dosyasını içeren `@layout MainLayout` klasör kullanımda bileşenlerinin tümünü sağlar *MainLayout* düzeni. Sürekli olarak eklemeniz gerekmez `@layout` tüm *.razor* dosyaları.

Varsayılan şablon kullanan Not *_viewımports.cshtml* Düzen seçim mekanizması. Yeni oluşturulan bir uygulamayı içeren *_viewımports.cshtml* dosyası *bileşenleri/sayfaları* klasör.

## <a name="nested-layouts"></a>İç içe geçmiş düzenleri

Uygulamalar, iç içe geçmiş yerleşimlerinin oluşabilir. Başka bir düzen sırayla başvuran bir düzen bir bileşene başvuruda bulunabilir. Örneğin, iç içe geçme düzenleri, çok düzeyli menü yapısını yansıtmak için kullanılabilir.

Aşağıdaki kod örnekleri, iç içe geçmiş düzenlerini kullanmayı göstermektedir. *EpisodesComponent.cshtml* göstermek için bileşeni dosyasıdır. Bileşen düzenini başvuran Not `MasterListLayout`.

*EpisodesComponent.cshtml*:

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

*MasterListLayout.cshtml* dosyası sağlar `MasterListLayout`. Başka bir düzen düzenini başvuran `MasterLayout`, burada katıştırılmış geçiyor.

*MasterListLayout.cshtml*:

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

Son olarak, `MasterLayout` üstbilgi, altbilgi ve ana menüsü gibi üst düzey Düzen öğeleri içerir.

*MasterLayout.cshtml*:

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
