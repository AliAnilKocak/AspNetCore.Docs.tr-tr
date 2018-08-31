---
title: ASP.NET Core ve Azure ile DevOps | Araçlar ve indirmeler
author: CamSoper
description: Azure'da barındırılan bir ASP.NET Core uygulaması için bir DevOps işlem hattı oluşturmaya uçtan uca yönergeler sağlar. bir kılavuz.
ms.author: casoper
ms.date: 08/07/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: a63e97d9ab9eb0ed2fbd30e8c2e033f0c048d33e
ms.sourcegitcommit: ecf2cd4e0613569025b28e12de3baa21d86d4258
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43312307"
---
# <a name="tools-and-downloads"></a>Araçlar ve indirmeler

Azure, sağlama ve kaynakları gibi yönetmek için çeşitli arabirimlerin sahip [Azure portalında](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/cli/azure/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [Azure bulut Kabuk](https://shell.azure.com/bash)ve Visual Studio. Bu kılavuz, minimalist bir yaklaşım ve Azure Cloud Shell'i mümkün olduğunca azaltmak gerekli adımlar kullanır. Ancak, Azure portalında bazı kısımları için kullanılmalıdır.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki abonelikler gereklidir:

* Azure &mdash; bir hesabınız yoksa, [ücretsiz bir deneme sürümü edinin](https://azure.microsoft.com/free/).
* Visual Studio Team Services (VSTS) &mdash; bölüm 4'te bu hesabı oluşturulur.
* GitHub &mdash; bir hesabınız yoksa, [ücretsiz olarak kaydolun](https://github.com/join).

Aşağıdaki araçları gereklidir:

* [Git](https://git-scm.com/downloads) &mdash; Git temel bir anlayış, bu kılavuz için önerilir. Gözden geçirme [Git belgeleri](https://git-scm.com/doc), özellikle [git uzak](https://git-scm.com/docs/git-remote) ve [git gönderimi](https://git-scm.com/docs/git-push).
* [.NET core SDK'sı](https://www.microsoft.com/net/download/) &mdash; 2.1.300 sürümü veya üzeri derlemek ve örnek uygulamayı çalıştırmak için gereklidir. Visual Studio ile yüklü değilse **.NET Core çoklu platform geliştirme** iş yükü, .NET Core SDK'sı zaten yüklü.

    .NET Core SDK'yı yüklemenizi doğrulayın. Bir komut kabuğunu açın ve aşağıdaki komutu çalıştırın:

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Önerilen Araçlar (yalnızca Windows)

* [Visual Studio](https://www.visualstudio.com/)kullanıcının güçlü Azure araçları sağlar bir GUI için bu kılavuzda açıklanan işlevselliğinin büyük kısmını. Ücretsiz Visual Studio Community sürümü dahil olmak üzere herhangi bir Visual Studio sürümünü çalışır. Öğreticiler, geliştirme, dağıtma ve DevOps hem ile hem de Visual Studio olmadan göstermek için yazılır.

  Aşağıdaki Visual Studio onaylayın [iş yükleri](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) yüklü:

  * ASP.NET ve web geliştirme
  * Azure geliştirme
  * .NET core platformlar arası geliştirme
