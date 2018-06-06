---
title: Bireysel kullanıcı hesapları ile oluşturulan ASP.NET Core projeleri göre makaleleri
author: rick-anderson
description: Bireysel kullanıcı hesapları ile oluşturulan ASP.NET Core projeleri göre makaleleri bulur.
manager: wpickett
ms.author: riande
ms.date: 11/30/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/authentication/individual
ms.openlocfilehash: 699def0133f53b922477ac294f70db41998945ef
ms.sourcegitcommit: 43bd79667bbdc8a07bd39fb4cd6f7ad3e70212fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34729556"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="12f2e-103">Bireysel kullanıcı hesapları ile oluşturulan ASP.NET Core projeleri göre makaleleri</span><span class="sxs-lookup"><span data-stu-id="12f2e-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="12f2e-104">ASP.NET Core kimlik "Tek tek kullanıcı hesaplarını" seçeneği ile Visual Studio Proje şablonları dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="12f2e-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="12f2e-105">Kimlik doğrulama şablonları .NET Core CLI ile kullanılabilir `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="12f2e-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="12f2e-106">Aşağıdaki makaleler bireysel kullanıcı hesapları kullanan ASP.NET Core şablonlarında oluşturulan kodu kullanmayı göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="12f2e-106">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* [<span data-ttu-id="12f2e-107">SMS ile iki öğeli kimlik doğrulama</span><span class="sxs-lookup"><span data-stu-id="12f2e-107">Two-factor authentication with SMS</span></span>](xref:security/authentication/2fa)
* [<span data-ttu-id="12f2e-108">Hesap doğrulama ve ASP.NET Core parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="12f2e-108">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="12f2e-109">Kullanıcı veri yetkilendirme tarafından korunan bir ASP.NET Core uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="12f2e-109">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
