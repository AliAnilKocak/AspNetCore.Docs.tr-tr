---
uid: webhooks/source
title: ASP.NET Web Kancalarını kaynak kodu ve NuGet paketleri | Microsoft Docs
author: rick-anderson
description: ASP.NET Web Kancalarını kaynak kodu ve NuGet paketleri bağlantılar
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/17/2012
ms.topic: article
ms.assetid: 91a62bfa-ea3a-41f9-a2e1-e90d2c8fc8ca
ms.technology: ''
ms.prod: .net-framework
ms.openlocfilehash: 733a0839c77bcfc96214bdf235ce8fe22ee2d3cf
ms.sourcegitcommit: 2d23ea501e0213bbacf65298acf1c8bd17209540
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/09/2018
ms.locfileid: "27709976"
---
# <a name="aspnet-webhooks-source-code-and-nuget-packages"></a><span data-ttu-id="dfe86-103">ASP.NET Web Kancalarını kaynak kodu ve NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="dfe86-103">ASP.NET WebHooks source code and NuGet packages</span></span>

<span data-ttu-id="dfe86-104">Microsoft ASP.NET WebHooks modülleri Microsoft ASP.NET ailesinin bir parçasıdır ve olarak barındırılan bir [github'da açık kaynaklı proje](https://github.com/aspnet/WebHooks).</span><span class="sxs-lookup"><span data-stu-id="dfe86-104">Microsoft ASP.NET WebHooks is part of the Microsoft ASP.NET family of modules and is hosted as an [Open Source Project on GitHub](https://github.com/aspnet/WebHooks).</span></span> <span data-ttu-id="dfe86-105">Bu size Katkıları kabul ancak lütfen bakmak anlamına gelir [katkı yönergeleri](https://github.com/aspnet/Home/blob/master/CONTRIBUTING.md) bir çekme isteği göndermeden önce.</span><span class="sxs-lookup"><span data-stu-id="dfe86-105">This means that we accept contributions, but please look at the [Contribution Guidelines](https://github.com/aspnet/Home/blob/master/CONTRIBUTING.md) before submitting a pull request.</span></span>

<span data-ttu-id="dfe86-106">Okumakta olduğunuz bu çevrimiçi belgeleri şimdi de olarak barındırılan [github'da açık kaynaklı](http://docs.asp.net/en/latest/contribute/style-guide.html#style-guide) ve ayrıca Katkıları kabul eder.</span><span class="sxs-lookup"><span data-stu-id="dfe86-106">This online documentation which you are reading now is also hosted as [Open Source on GitHub](http://docs.asp.net/en/latest/contribute/style-guide.html#style-guide) and also accepts contributions.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="dfe86-107">NuGet paketleri</span><span class="sxs-lookup"><span data-stu-id="dfe86-107">NuGet packages</span></span>

<span data-ttu-id="dfe86-108">[NuGet paketlerini](https://nuget.org/packages?q=Microsoft.AspNet.WebHooks) üç bölüme ayrılır:</span><span class="sxs-lookup"><span data-stu-id="dfe86-108">The [NuGet packages](https://nuget.org/packages?q=Microsoft.AspNet.WebHooks) are divided into three parts:</span></span>

* <span data-ttu-id="dfe86-109">[Ortak](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Common): göndericiler ile alıcılar arasında paylaşılan ortak bir paket.</span><span class="sxs-lookup"><span data-stu-id="dfe86-109">[Common](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Common): A common package that is shared between senders and receivers.</span></span>

* <span data-ttu-id="dfe86-110">[Gönderen](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Custom): kendi Web Kancalarını başkalarına göndermek amacıyla destekleyen paketler kümesi.</span><span class="sxs-lookup"><span data-stu-id="dfe86-110">[Sender](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Custom): A set of packages supporting sending your own WebHooks to others.</span></span> <span data-ttu-id="dfe86-111">Web kancası göndermek için işlevselliği, daha ayrıntılı olarak açıklandığı [gönderme Kancalarını](sending/index.md).</span><span class="sxs-lookup"><span data-stu-id="dfe86-111">The functionality for sending WebHooks is described in more detail in [Sending WebHooks](sending/index.md).</span></span>

* <span data-ttu-id="dfe86-112">[Alıcıları](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Receivers): bir Web kancası diğer bilgisayarlardan almasını destekleyen paketler kümesi.</span><span class="sxs-lookup"><span data-stu-id="dfe86-112">[Receivers](https://www.nuget.org/packages?q=Microsoft.AspNet.WebHooks.Receivers): A set of packages supporting receiving WebHooks from others.</span></span> <span data-ttu-id="dfe86-113">Web kancası alma işlevselliği, daha ayrıntılı olarak açıklanan [alma Web Kancalarını](receiving/index.md).</span><span class="sxs-lookup"><span data-stu-id="dfe86-113">The functionality for receiving WebHooks is described in more detail in [Receiving WebHooks](receiving/index.md).</span></span>
