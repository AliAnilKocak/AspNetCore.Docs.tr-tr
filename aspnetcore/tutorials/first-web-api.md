---
title: "Windows için ASP.NET Core ve Visual Studio ile Web API oluşturma"
author: rick-anderson
description: "ASP.NET Core MVC ve Windows için Visual Studio ile web API'si oluşturma"
ms.author: riande
manager: wpickett
ms.date: 08/15/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-web-api
ms.openlocfilehash: bfa6ae4b04628a4c0b868a054446843ee3401f8a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2018
---
#<a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-windows"></a>ASP.NET Core ve Windows için Visual Studio ile web API oluşturma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [CAN Wasson](https://github.com/mikewasson)

Bu öğretici, "Yapılacaklar" öğeleri listesini yönetmek için bir web API oluşturur. Bir kullanıcı arabirimi (UI) oluşturulmaz.

Bu öğretici 3 sürümü vardır:

* Windows: Web API ile Windows için Visual Studio (Bu öğretici)
* macOS: [Mac için Visual Studio ile Web API](xref:tutorials/first-web-api-mac)
* macOS, Linux, Windows: [Visual Studio Code ile Web API](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE[install 2.0](../includes/install2.0.md)]

Bkz: [bu PDF](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/first-web-api/_static/_webAPI.pdf) ASP.NET Core 1.1 sürümünün.

## <a name="create-the-project"></a>Projeyi oluşturma

Visual Studio'dan seçin **dosya** menüsünde > **yeni** > **proje**.

Seçin **ASP.NET çekirdek Web uygulaması (.NET Core)** proje şablonu. Proje adı `TodoApi` seçip **Tamam**.

![Yeni Proje iletişim kutusu](first-web-api/_static/new-project.png)

İçinde **yeni ASP.NET çekirdek Web uygulaması - TodoApi** iletişim kutusunda **Web API** şablonu. Seçin **Tamam**. Yapmak **değil** seçin **Docker desteğini etkinleştir**.

![ASP.NET Core şablonlardan seçili Web API projesi şablonuyla yeni ASP.NET Web uygulaması iletişim kutusu](first-web-api/_static/web-api-project.png)

### <a name="launch-the-app"></a>Uygulamayı başlatın

Visual Studio'da uygulamayı başlatmak için CTRL + F5 tuşuna basın. Visual Studio bir tarayıcı ile başlatarak `http://localhost:port/api/values`, burada *bağlantı noktası* rastgele seçilen bağlantı noktası numarasıdır. Chrome, Microsoft Edge ve Firefox aşağıdaki çıkış görüntüler:

```
["value1","value2"]
```

### <a name="add-a-model-class"></a>Bir model sınıfı ekleme

Uygulama verileri temsil eden bir nesne modelidir. Bu durumda, yalnızca bir Yapılacaklar öğesi modelidir.

"Modelleri" adlı bir klasör ekleyin. Çözüm Gezgini'nde projeye sağ tıklayın. Seçin **ekleme** > **yeni klasör**. Klasör adı *modelleri*.

Not: Modeli sınıfları projede herhangi bir yere gidin. *Modelleri* klasörü kural tarafından model sınıfları için kullanılır.

Ekleme bir `TodoItem` sınıfı. Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**. Sınıf adını `TodoItem` seçip **Ekle**.

Güncelleştirme `TodoItem` aşağıdaki kodla sınıfı:

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoItem.cs)]

Veritabanı oluşturur `Id` zaman bir `TodoItem` oluşturulur.

### <a name="create-the-database-context"></a>Veritabanı bağlamı oluşturma

*Veritabanı bağlamı* verilen veri modeli için Entity Framework işlevselliği koordinatları ana sınıftır. Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.

Ekleme bir `TodoContext` sınıfı. Sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**. Sınıf adını `TodoContext` seçip **Ekle**.

Sınıfı aşağıdaki kodla değiştirin:

[!code-csharp[Main](first-web-api/sample/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a>Denetleyici ekleme

Çözüm Gezgini'nde sağ *denetleyicileri* klasör. Seçin **ekleme** > **yeni öğe**. İçinde **Yeni Öğe Ekle** iletişim kutusunda **Web API denetleyicisi sınıfı** şablonu. Sınıf adını `TodoController`.

![Yeni öğe iletişim denetleyicisiyle seçili arama kutusu ve web API denetleyicisi ekleyin](first-web-api/_static/new_controller.png)

Sınıfı aşağıdaki kodla değiştirin:

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>Uygulamayı başlatın

Visual Studio'da uygulamayı başlatmak için CTRL + F5 tuşuna basın. Visual Studio bir tarayıcı ile başlatarak `http://localhost:port/api/values`, burada *bağlantı noktası* rastgele seçilen bağlantı noktası numarasıdır. Gidin `Todo` denetleyicisinde `http://localhost:port/api/todo`.

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]

