---
uid: web-forms/overview/ajax-control-toolkit/confirmbutton/using-a-confirmbutton-in-a-repeater-cs
title: Bir ConfirmButton bir yineleyici (C#) kullanarak | Microsoft Docs
author: wenz
description: AJAX Denetim araç setindeki ConfirmButton genişletici Evet oluşturur/Kullanıcı bir düğmesine tıkladığında herhangi bir açılır pencere (LinkButton denetim dahil). Yalnızca Evet ise...
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: a973ed3e-400c-4925-ace2-0b086b479301
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/confirmbutton/using-a-confirmbutton-in-a-repeater-cs
msc.type: authoredcontent
ms.openlocfilehash: 7a0717083a3c1e285f0c8c4cb8503d2bbe42153b
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30871525"
---
<a name="using-a-confirmbutton-in-a-repeater-c"></a>Bir ConfirmButton kullanarak bir yineleyici (C#)
====================
tarafından [Christian Wenz](https://github.com/wenz)

[Kodu indirme](http://download.microsoft.com/download/8/6/d/86dea6c6-bb92-4fa6-aa14-f8c0f82100f5/ConfirmButton1.cs.zip) veya [PDF indirin](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/confirmbutton1CS.pdf)

> AJAX Denetim araç setindeki ConfirmButton genişletici Evet oluşturur/Kullanıcı bir düğmesine tıkladığında herhangi bir açılır pencere (LinkButton denetim dahil). Yalnızca Evet tıklandığında, düğmenin eylemini, aksi takdirde iptal yürütülür. Bu aynı zamanda bir yineleyici mümkündür.


## <a name="overview"></a>Genel Bakış

AJAX Denetim araç setindeki ConfirmButton genişletici Evet oluşturur/Kullanıcı bir düğmesine tıkladığında herhangi bir açılır pencere (LinkButton denetim dahil). Yalnızca Evet tıklandığında, düğmenin eylemini, aksi takdirde iptal yürütülür. Bu aynı zamanda bir yineleyici mümkündür.

## <a name="steps"></a>Adımlar

Öncelikle, bir veri kaynağı gereklidir. Bu örnekte, AdventureWorks veritabanını ve Microsoft SQL Server 2005 Express Edition kullanır. Veritabanı (express edition dahil) bir Visual Studio yükleme isteğe bağlı bir parçasıdır ve ayrıca altında ayrı bir yükleme olarak kullanılabilir [ https://go.microsoft.com/fwlink/?LinkId=64064 ](https://go.microsoft.com/fwlink/?LinkId=64064). AdventureWorks veritabanını SQL Server 2005 örnekler ve örnek veritabanları parçasıdır (adresinden [ https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e &amp;DisplayLang = tr](https://www.microsoft.com/downloads/details.aspx?FamilyID=e719ecf7-9f46-4312-af89-6ad8702e4e6e&amp;DisplayLang=en)). En kolay yolu veritabanını için Microsoft SQL Server Management Studio Express kullanmaktır ([https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang = tr](https://www.microsoft.com/downloads/details.aspx?FamilyID=c243a5ae-4bd1-4e3d-94b8-5a0f62bf7796&amp;DisplayLang=en)) ve attach `AdventureWorks.mdf` veritabanı dosyası.

Bu örnek için SQL Server 2005 Express Edition'ın örneğini çağrıldığından emin olan varsayıyoruz `SQLEXPRESS` ve web sunucusu; ile aynı makinede bulunan varsayılan kurulum de budur. Kurulumunuzu farklıysa, veritabanı için bağlantı bilgilerini uymak zorunda.

ASP.NET AJAX ve Denetim Araç Seti işlevselliğini etkinleştirmek için `ScriptManager` denetim gerekir yerleştirmek herhangi bir yere sayfada (ancak içinde `<form>` öğesi):

[!code-aspx[Main](using-a-confirmbutton-in-a-repeater-cs/samples/sample1.aspx)]

Ardından, bir veri kaynağı gereklidir. Basitleştirmek amacıyla, yalnızca ilk beş girişlerini AdventureWorks satıcılar tablosundaki alınır. Veri kaynağı, tablo adı oluşturmak için Visual Studio Sihirbazı'nı kullanırken dikkat edin (`Vendors`) şu anda doğru ile eklenmedi `Purchasing`. Aşağıdaki biçimlendirmede doğru olur:

[!code-aspx[Main](using-a-confirmbutton-in-a-repeater-cs/samples/sample2.aspx)]

Bu veri kaynağı yineleyici içinde kullanılabilir. Her zamanki gibi `DataBinder.Eval()` yöntemi veri kaynağından veri alır. `ConfirmButtonExtender` Denetim sonra yerleştirilmelidir içinde `<ItemTemplate>` olan her veri kaynağı girişi için görünmesi yineleyici bölümü.

[!code-aspx[Main](using-a-confirmbutton-in-a-repeater-cs/samples/sample3.aspx)]


[![Her giriş veri kaynağından yanındaki Onayla düğmesi görünür](using-a-confirmbutton-in-a-repeater-cs/_static/image2.png)](using-a-confirmbutton-in-a-repeater-cs/_static/image1.png)

Her giriş veri kaynağından yanındaki Onayla düğmesi görünür ([tam boyutlu görüntüyü görüntülemek için tıklatın](using-a-confirmbutton-in-a-repeater-cs/_static/image3.png))

> [!div class="step-by-step"]
> [Next](using-a-confirmbutton-in-a-repeater-vb.md)
