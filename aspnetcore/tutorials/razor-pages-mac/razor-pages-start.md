---
title: "ASP.NET Core Mac üzerinde Razor sayfalarında ile çalışmaya başlama"
author: rick-anderson
description: "Mac için Visual Studio kullanarak ASP.NET Core Razor sayfalarında ile çalışmaya başlama"
manager: wpickett
ms.author: riande
ms.date: 07/27/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: 9e7d1db47e4cc9d753b1629e20345ca1f4403b2f
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/30/2018
---
# <a name="getting-started-with-razor-pages-in-aspnet-core-with-visual-studio-for-mac"></a>Mac için Visual Studio ile ASP.NET Core Razor sayfalarında ile Başlarken

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğretici, bir ASP.NET Core Razor sayfalarının web uygulaması oluşturmanın temel öğretir. Gözden geçirmenizi öneririz [Razor sayfalarının giriş](xref:mvc/razor-pages/index) bu öğreticiye başlamadan önce. Razor sayfalarının ASP.NET Core web uygulamaları için kullanıcı Arabirimi oluşturmak için önerilen yöntem olduğu.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki yükleyin:

* [.NET core 2.0.0 SDK](https://www.microsoft.com/net/core) veya daha yenisi
* [Mac için Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="create-a-razor-web-app"></a>Bir Razor web uygulaması oluşturma

Terminal durumundan, aşağıdaki komutları çalıştırın:

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

Yukarıdaki komutları kullanım [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/dotnet) oluşturun ve Razor sayfalarının proje çalıştırın. Http://localhost: 5000 uygulamayı görüntülemek için bir tarayıcı açın.

![Giriş veya dizin sayfası](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE[razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a>Projeyi açın

Uygulamayı kapatın CTRL + C tuşlarına basın.

Visual Studio'dan seçin **Dosya > Aç**ve ardından *RazorPagesMovie.csproj* dosya.

### <a name="launch-the-app"></a>Uygulamayı başlatın

Visual Studio'da seçin **Çalıştır > hata ayıklama olmadan Başlat** uygulamayı başlatmak için. Visual Studio başlatır [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview), bir tarayıcı başlatılır ve gider `http://localhost:5000`.

Sonraki öğreticide biz projenize bir model ekleyin.

>[!div class="step-by-step"]
[Sonraki: bir modeli ekleme](xref:tutorials/razor-pages-mac/model)
