---
title: ASP.NET Çekirdeği ve Visual Studio Code ile Web API oluşturma
author: rick-anderson
description: MacOS, Linux veya Windows ASP.NET Core MVC ve Visual Studio Code ile web API'si oluşturma
manager: wpickett
ms.author: riande
ms.date: 09/22/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/web-api-vsc
ms.openlocfilehash: 705524a62018b9f0fbd8c40fa1b70d4c62ee236e
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-web-api-with-aspnet-core-and-visual-studio-code"></a>ASP.NET Çekirdeği ve Visual Studio Code ile Web API oluşturma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [CAN Wasson](https://github.com/mikewasson)

Bu öğreticide, "Yapılacaklar" öğeleri listesini yönetmek için bir web API'si oluşturma. Bir kullanıcı Arabirimi oluşturulan değil.

Bu öğretici 3 sürümü vardır:

* macOS, Linux, Windows: Visual Studio Code (Bu öğretici) ile Web API
* macOS: [Mac için Visual Studio ile Web API](xref:tutorials/first-web-api-mac)
* Windows: [Web API'si Visual Studio ile Windows için](xref:tutorials/first-web-api)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE [template files](../includes/webApi/intro.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [](~/includes/net-core-prereqs-vscode.md)]

## <a name="create-the-project"></a>Projeyi oluşturma

Bir konsoldan, aşağıdaki komutları çalıştırın:

```console
mkdir TodoApi
cd TodoApi
dotnet new webapi
```

Açık *TodoApi* Visual Studio Code (VS Code) ve select klasöründe *haline* dosya.

- Seçin **Evet** için **uyar** "derleme ve hata ayıklamak için gerekli varlıklar 'TodoApi' eksik. ileti Bunları eklensin mi?"
- Seçin **geri** için **bilgisi** "Çözümlenmemiş bağımlılıkları bulunur" iletisi.

<!-- uid: tutorials/first-mvc-app-xplat/start-mvc uses the pic below. If you change it, make sure it's consistent -->

![Derleme ve hata ayıklama VS koduyla gerekli uyar varlıklar 'TodoApi' eksik. Bunları eklensin mi? Şimdi değil Evet sorma](web-api-vsc/_static/vsc_restore.png)

Tuşuna **hata ayıklama** derlemek ve programı çalıştırmak için (F5). Bir tarayıcıda gidin http://localhost:5000/api/values . Aşağıdaki görüntülenir:

`["value1","value2"]`

Bkz: [Visual Studio Code Yardım](#visual-studio-code-help) VS Code kullanma hakkında ipuçları için.

## <a name="add-support-for-entity-framework-core"></a>Entity Framework Çekirdek desteği ekleme

.NET Core 2. 0 ' yeni bir proje oluşturma ekler 'Microsoft.AspNetCore.All' Sağlayıcısında *TodoApi.csproj* dosya. Yüklemek için gerek yoktur [Entity Framework Çekirdek Inmemory](https://docs.microsoft.com/ef/core/providers/in-memory/) sağlayıcı ayrı olarak veritabanı. Bu veritabanı sağlayıcısı bir bellek içi veritabanı ile kullanılacak Entity Framework Çekirdek sağlar.

[!code-xml[](web-api-vsc/sample/TodoApi/TodoApi.csproj?highlight=12)]

## <a name="add-a-model-class"></a>Bir model sınıfı ekleme

Uygulamanızdaki verileri temsil eden bir nesne modelidir. Bu durumda, yalnızca bir Yapılacaklar öğesi modelidir.

Adlı bir klasör ekleme *modelleri*. Model sınıfları projenizde, herhangi bir yere koyabilirsiniz ancak *modelleri* klasörü, kural tarafından kullanılır.

Ekleme bir `TodoItem` aşağıdaki kodla sınıfı:

[!code-csharp[](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

Veritabanı oluşturur `Id` zaman bir `TodoItem` oluşturulur.

## <a name="create-the-database-context"></a>Veritabanı bağlamı oluşturma

*Veritabanı bağlamı* verilen veri modeli için Entity Framework işlevselliği koordinatları ana sınıftır. Bu sınıf türetme tarafından oluşturduğunuz `Microsoft.EntityFrameworkCore.DbContext` sınıfı.

Ekleme bir `TodoContext` sınıfını *modelleri* klasörü:

[!code-csharp[](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE [Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a>Denetleyici ekleme

İçinde *denetleyicileri* klasörünü adlı bir sınıf oluşturun `TodoController`. Aşağıdaki kodu ekleyin:

[!INCLUDE [code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>Uygulamayı başlatın

VS Code'da uygulamayı başlatmak için F5 tuşuna basın. Gidin http://localhost:5000/api/todo ( `Todo` yeni oluşturduğumuz denetleyicisi).

[!INCLUDE [last part of web API](../includes/webApi/end.md)]

## <a name="visual-studio-code-help"></a>Visual Studio Code help

- [Başlarken](https://code.visualstudio.com/docs)
- [Hata Ayıklama](https://code.visualstudio.com/docs/editor/debugging)
- [Tümleşik terminal](https://code.visualstudio.com/docs/editor/integrated-terminal)
- [Klavye kısayolları](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference)

  - [macOS klavye kısayolları](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)
  - [Linux klavye kısayolları](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf)
  - [Windows klavye kısayolları](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

[!INCLUDE [next steps](../includes/webApi/next.md)]

