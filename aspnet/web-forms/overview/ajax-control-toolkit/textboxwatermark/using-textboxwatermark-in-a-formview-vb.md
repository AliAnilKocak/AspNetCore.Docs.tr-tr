---
uid: web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-in-a-formview-vb
title: TextBoxWatermark bir FormView (VB) kullanarak | Microsoft Docs
author: wenz
description: "Böylece bir metin kutusu içinde görüntülenir AJAX Denetim Araç Seti TextBoxWatermark denetiminde bir metin kutusu genişletir. Bir kullanıcı kutunun içine tıkladığında, ı..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 41497361-7fba-4825-b36c-f58d79522a88
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/textboxwatermark/using-textboxwatermark-in-a-formview-vb
msc.type: authoredcontent
ms.openlocfilehash: ad75d9729c068f7d512cf076b2ae156291fe76ec
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="using-textboxwatermark-in-a-formview-vb"></a>TextBoxWatermark bir FormView (VB) kullanma
====================
tarafından [Christian Wenz](https://github.com/wenz)

[Kodu indirme](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/TextBoxWatermark1.vb.zip) veya [PDF indirin](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/textboxwatermark1VB.pdf)

> Böylece bir metin kutusu içinde görüntülenir AJAX Denetim Araç Seti TextBoxWatermark denetiminde bir metin kutusu genişletir. Bir kullanıcı kutunun içine tıkladığında boşaltılır. Kullanıcı kutunun metin girmeden ayrılırsa, doldurulmuş metin görüntülenir. İçinde bir FormView denetimi de mümkündür.


## <a name="overview"></a>Genel Bakış

`TextBoxWatermark` AJAX Denetim Araç Seti denetiminde genişleten bir metin kutusu böylece bir metin kutusu içinde görüntülenir. Bir kullanıcı kutunun içine tıkladığında boşaltılır. Kullanıcı kutunun metin girmeden ayrılırsa, doldurulmuş metin görüntülenir. Bu aynı zamanda içinde mümkündür bir `FormView` denetim.

## <a name="steps"></a>Adımlar

Öncelikle, bir veri kaynağı gereklidir. Bu örnekte, AdventureWorks veritabanını ve Microsoft SQL Server 2005 Express Edition kullanır. Veritabanı (express edition dahil) bir Visual Studio yükleme isteğe bağlı bir parçasıdır ve ayrıca altında ayrı bir yükleme olarak kullanılabilir [https://go.microsoft.com/fwlink/?LinkId=64064](https://go.microsoft.com/fwlink/?LinkId=64064). AdventureWorks veritabanını SQL Server 2005 örnekler ve örnek veritabanları parçasıdır (adresinden [https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang = tr](https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang=en)). En kolay yolu veritabanını için Microsoft SQL Server Management Studio Express kullanmaktır ([https://www.microsoft.com/downloads/details.aspx? FamilyID c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796 =&amp;DisplayLang = tr](https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang=en)) ve attach `AdventureWorks.mdf` veritabanı dosyası.

Bu örnek için SQL Server 2005 Express Edition'ın örneğini çağrıldığından emin olan varsayıyoruz `SQLEXPRESS` ve web sunucusu; ile aynı makinede bulunan varsayılan kurulum de budur. Kurulumunuzu farklıysa, veritabanı için bağlantı bilgilerini uymak zorunda.

ASP.NET AJAX ve Denetim Araç Seti işlevselliğini etkinleştirmek için `ScriptManager` denetim gerekir yerleştirmek herhangi bir yere sayfada (ancak içinde `<form>` öğesi):

[!code-aspx[Main](using-textboxwatermark-in-a-formview-vb/samples/sample1.aspx)]

Ardından, bir veri kaynağı destekleyen sayfasına ekleme `DELETE`, `INSERT` ve `UPDATE` SQL deyimlerini. Veri kaynağı oluşturmak için Visual Studio Yardımcısı'nı kullanıyorsanız, geçerli sürümde hata tablo adı öneki değil şunları aklınızda (`Vendor`) ile `Purchasing`. Aşağıdaki biçimlendirmede doğru sözdizimi gösterilmektedir:

[!code-aspx[Main](using-textboxwatermark-in-a-formview-vb/samples/sample2.aspx)]

Adı unutmayın (`ID`) veri kaynağının içinde kullanılacağından `DataSourceID` özelliği `FormView` denetim. `<InsertItemTemplate>` Bölümünü `FormView` tarafından genişletilmiş bir metin kutusu içeren `TextBoxWatermarkExtender` denetim. Genişletici şablon içinde ve dışında bulunduğundan emin olun.

[!code-aspx[Main](using-textboxwatermark-in-a-formview-vb/samples/sample3.aspx)]

Artık kullanıcı değiştiğinde INSERT moduna `FormView` denetlemek, metin alanı yeni satıcı teşekkürler doldurulmuş için `TextBoxWatermarkExtender` denetim. Metin kutusu içinde bir tıklatma kayboluyor dolgusu metin sağlar.


[![Filigran alanında genişletici gelir](using-textboxwatermark-in-a-formview-vb/_static/image2.png)](using-textboxwatermark-in-a-formview-vb/_static/image1.png)

Filigran alanında genişletici gelir ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-textboxwatermark-in-a-formview-vb/_static/image3.png))

>[!div class="step-by-step"]
[Önceki](using-textboxwatermark-with-validation-controls-cs.md)
[sonraki](using-textboxwatermark-with-validation-controls-vb.md)
