---
uid: web-forms/overview/ajax-control-toolkit/slider/databinding-the-slider-control-cs
title: "Veri bağlama kaydırıcı denetimi (C#) | Microsoft Docs"
author: wenz
description: "AJAX Denetim Araç Seti, kaydırıcı denetimi fare kullanılarak denetlenebilir bir grafik kaydırıcı sağlar. Geçerli konum bağlamak mümkündür..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: b7f77869-aa1d-4025-924f-622c57112db6
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/slider/databinding-the-slider-control-cs
msc.type: authoredcontent
ms.openlocfilehash: 2aa770bce5969a4ab57893d5ceecc127cf7f7872
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="databinding-the-slider-control-c"></a>Veri bağlama kaydırıcı denetimi (C#)
====================
tarafından [Christian Wenz](https://github.com/wenz)

[Kodu indirme](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/Slider0.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/slider0CS.pdf)

> AJAX Denetim Araç Seti, kaydırıcı denetimi fare kullanılarak denetlenebilir bir grafik kaydırıcı sağlar. Başka bir ASP.NET denetimi kaydırıcıyı geçerli konumunu bağlamak mümkündür.


## <a name="overview"></a>Genel Bakış

AJAX Denetim Araç Seti, kaydırıcı denetimi fare kullanılarak denetlenebilir bir grafik kaydırıcı sağlar. Başka bir ASP.NET denetimi kaydırıcıyı geçerli konumunu bağlamak mümkündür.

## <a name="steps"></a>Adımlar

ASP.NET AJAX ve Denetim Araç Seti işlevselliğini etkinleştirmek için `ScriptManager` denetim gerekir yerleştirmek herhangi bir yere sayfada (ancak içinde `<form>` öğesi):

[!code-aspx[Main](databinding-the-slider-control-cs/samples/sample1.aspx)]

Ardından, iki eklemek `TextBox` sayfasına denetimler. Bir grafik kaydırıcı dönüştürülür ve diğerinde kaydırıcıyı konumunu tutacaktır.

[!code-aspx[Main](databinding-the-slider-control-cs/samples/sample2.aspx)]

Sonraki adım zaten son adımdır. `SliderExtender` ASP.NET AJAX Denetim Araç Seti denetiminden ilk metin kutusunun dışında bir kaydırıcı yapar ve kaydırıcıyı getirin değiştiğinde ikinci metin kutusu otomatik olarak güncelleştirir. Çalışmak, o sırada `SliderExtender`'s `TargetControlID` özniteliği ilk metin kutusunun; Kimliğine ayarlanmalıdır `BoundControlID` özniteliği ikinci metin kutusunun Kimliğine ayarlanmalıdır.

[!code-aspx[Main](databinding-the-slider-control-cs/samples/sample3.aspx)]

Veri bağlama tarayıcıda gördüğünüz gibi her iki yönde de çalışır: metin kutusuna yeni bir değer girme kaydırıcının konumunu güncelleştirir. Salt okunur ikinci metin kutusu yaparsanız, böylece kullanıcının orada değerini el ile güncelleştirmeniz daha zayıf bir koruma metin alanı ekleyebilir.


[![Kaydırıcı ve metin kutusu eşitleme](databinding-the-slider-control-cs/_static/image2.png)](databinding-the-slider-control-cs/_static/image1.png)

Kaydırıcı ve metin kutusu eşitlenmiş ([tam boyutlu görüntüyü görüntülemek için tıklatın](databinding-the-slider-control-cs/_static/image3.png))

>[!div class="step-by-step"]
[Önceki](using-the-slider-control-with-auto-postback-cs.md)
[sonraki](using-the-slider-control-with-auto-postback-vb.md)
