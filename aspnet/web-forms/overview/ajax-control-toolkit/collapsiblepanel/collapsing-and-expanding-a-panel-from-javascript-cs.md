---
uid: web-forms/overview/ajax-control-toolkit/collapsiblepanel/collapsing-and-expanding-a-panel-from-javascript-cs
title: JavaScript (C#) panelinden genişletme ve daraltma | Microsoft Docs
author: wenz
description: ASP.NET AJAX Denetim Araç Seti CollapsiblePanel denetiminde bir panel genişletir ve içeriğini daraltmak ve genişletmek yeteneği sağlar bir...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: de5500be-75e5-461a-8064-b70ae52ea6a4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/collapsiblepanel/collapsing-and-expanding-a-panel-from-javascript-cs
msc.type: authoredcontent
ms.openlocfilehash: 7baa3be7144946bde7d11afd9b1cb5f14ad9dede
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
<a name="collapsing-and-expanding-a-panel-from-javascript-c"></a>JavaScript (C#) panelinden genişletme ve daraltma
====================
tarafından [Christian Wenz](https://github.com/wenz)

[Kodu indirme](http://download.microsoft.com/download/8/a/a/8aab3c3e-de6f-463f-805c-5fda567eef6e/CollapsiblePanel1.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/collapsiblepanel1CS.pdf)

> ASP.NET AJAX Denetim Araç Seti CollapsiblePanel denetiminde bir panel genişletir ve içeriğini daraltın ve tekrar genişletme olanağı sağlar. Bu iki eylem ayrıca özel JavaScript kodundan tetiklenebilir.


## <a name="overview"></a>Genel Bakış

ASP.NET AJAX Denetim Araç Seti CollapsiblePanel denetiminde bir panel genişletir ve içeriğini daraltın ve tekrar genişletme olanağı sağlar. Bu iki eylem ayrıca özel JavaScript kodundan tetiklenebilir.

## <a name="steps"></a>Adımlar

İlk olarak, yeni bir ASP.NET sayfası oluşturmak ve dahil etmek `ScriptManager` bir içinde `<form>` öğesi. Bu Denetim Araç Seti tarafından gerekli olan ASP.NET AJAX kitaplığı yükler:

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample1.aspx)]

Ardından, bir panel bazı metinle oluşturun, böylece daraltma ve genişletme etkisi görülebilir:

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample2.aspx)]

Gördüğünüz gibi bölmenin burada gösterilen (ve temel bir arka plan rengi ve bölmenin genişliğini tanımlar) bir CSS sınıfı başvuruyor:

[!code-css[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample3.css)]

`CollapsiblePanelExtender` Denetimi gerektirir `TargetControlID` daraltın veya istek üzerine genişletmek için hangi Masası Araç Seti bilir böylece özniteliği:

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample4.aspx)]

Ne yazık ki, genişletici şu anda belirli bir API daraltma veya paneli genişletme için kullanıma sunmuyor, ancak bazı belgelenmemiş yöntemleri yapar. Öncelikle, üç HTML düğmesi, ardından daraltmak veya bölmenin içeriği genişletmek için istemci tarafı JavaScript tetikleyecek sayfasına ekleyin:

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample5.aspx)]

İstemci tarafı JavaScript kodu (kullanmaya `<script type="text/javascript">`), `$find()` yönteminin gerekir kullanılacak erişimi `CollapsiblePanelExtender`. `$find("cpe")` bir başvuru döndürür. Üzerinde buradan belirli yöntemler elinizdeki çözüm.

Yöntemi (genişletme) açmak için bölmenin adlı `_doOpen()`; aşağıdaki kod uygulayan `doOpen()` işlevini çağırdı ilk düğme tıklatıldığında:

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample6.js)]

Kapatma veya paneli daraltma için `_doClose()` yöntemi yürütülmesi gerekiyor. Bu nedenle kullanıcı ikinci düğmesine tıkladığında, şu JavaScript kodunu çağrılır:

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample7.js)]

Üçüncü düğme paneli durumunu değiştirir: gelen için genişletilmiş, daraltılmış ve tersi. `CollapsiblePanelExtender` Sunan `toggle()` tam olarak, yapan yöntemi: paneli durumunu tersine çevirir. Ancak bulunmaktadır başka bir yaklaşım (dahili olarak kullanılan tarafından `toggle()` yöntemi): `get_Collapsed()` yöntemi `CollapsiblePanelExtender()` bize panel daraltılmış durumda değil söyler. Ya da Genişletilmiş sonra bu işlevin dönüş değerini bağlı olarak, panosudur (`_doOpen()` yöntemi) veya daraltılmış (`_doClose()`) yöntemi:

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample8.js)]


[![Üçüncü düğme paneli durumunu değiştirir: gelen genişletilmiş ve geri daraltılmış](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image2.png)](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image1.png)

Üçüncü düğme paneli durumunu değiştirir: gelen genişletilmiş ve geri daraltılmış ([tam boyutlu görüntüyü görüntülemek için tıklatın](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image3.png))

> [!div class="step-by-step"]
> [Next](collapsing-and-expanding-a-panel-from-javascript-vb.md)
