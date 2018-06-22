---
title: ASP.NET Core ile tek sayfa uygulaması şablonlarını kullanma
author: SteveSandersonMS
description: Yükleme ve ASP.NET Core tek sayfa uygulama (SPA) proje şablonları kullanmaya başlama hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/index
ms.openlocfilehash: ab164ae5d2df47739ec04b32cd21835ffdf9f87f
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291533"
---
# <a name="use-the-single-page-application-templates-with-aspnet-core"></a>ASP.NET Core ile tek sayfa uygulaması şablonlarını kullanma

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> Yayımlanan .NET Core SDK Angular için tepki, eski proje şablonları içerir ve Redux ile tepki 2.0.x. Bu belge, bu eski proje şablonları hakkında değil. Bu belge, en son Angular için tepki, olduğundan ve ASP.NET Core 2.0 el ile yüklenebilir Redux şablonları ile tepki. Şablonlar, ASP.NET Core 2.1 ile birlikte varsayılan olarak dahil edilir.

::: moniker-end

## <a name="prerequisites"></a>Önkoşullar

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Node.js](https://nodejs.org), 6 veya sonraki sürümü

## <a name="installation"></a>Yükleme

::: moniker range=">= aspnetcore-2.1"

Şablonları zaten ASP.NET Core 2.1 ile birlikte yüklenir.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 Angular için güncelleştirilmiş ASP.NET Core şablonları yüklemek için aşağıdaki komutu çalıştırın, varsa, tepki ve Redux ile tepki:

```console
dotnet new --install Microsoft.DotNet.Web.Spa.ProjectTemplates::2.0.0
```

::: moniker-end

## <a name="use-the-templates"></a>Şablonları kullanın.

* [Açısal proje şablonu kullanın](xref:spa/angular)
* [Tepki proje şablonu kullanın](xref:spa/react)
* [Tepki Redux proje şablonu ile kullanma](xref:spa/react-with-redux)
