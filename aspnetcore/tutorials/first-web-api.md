---
title: ASP.NET Core ve Visual Studio ile Web API'si oluşturma
author: rick-anderson
description: ASP.NET Core MVC ve Windows üzerinde Visual Studio ile bir web API'si oluşturma
ms.author: riande
ms.custom: mvc
ms.date: 05/17/2018
uid: tutorials/first-web-api
ms.openlocfilehash: eb23d5376742e04712f714263815582fddc5d18e
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450703"
---
# <a name="create-a-web-api-with-aspnet-core-and-visual-studio"></a>ASP.NET Core ve Visual Studio ile Web API'si oluşturma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Mike Wasson](https://github.com/mikewasson)

Bu öğreticide web API'si "Yapılacaklar" öğelerinin listesini yönetmek için oluşturur. Bir kullanıcı arabirimi (UI) oluşturulmaz.

Bu öğretici üç sürümü vardır:

* Windows: Web API Windows üzerinde Visual Studio (Bu öğretici, bkz: [videosu](https://www.youtube.com/watch?v=TTkhEyGBfAk))
* macOS: [Mac için Visual Studio ile Web API](xref:tutorials/first-web-api-mac)
* macOS, Linux, Windows: [Visual Studio Code ile Web API](xref:tutorials/web-api-vsc)

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

> [!NOTE]
> ASP.NET Core içindekiler tablosuna yönelik önerilmiş olan yeni bir yapının kullanılabilirliğini test ediyoruz.  Geçerli veya önerilen içindekiler tablosunda 7 farklı konuyu bulmaya ilişkin alıştırmayı denemek için vaktiniz varsa lütfen [çalışmaya katılmak için buraya tıklayın](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).

[!INCLUDE[intro to web API](../includes/webApi/intro.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE[](~/includes/net-core-prereqs-windows.md)]

## <a name="create-the-project"></a>Projeyi oluşturma

Visual Studio aşağıdaki adımları izleyin:

* Gelen **dosya** menüsünde **yeni** > **proje**.
* Seçin **ASP.NET Core Web uygulaması** şablonu. Projeyi adlandırın *TodoApi* tıklatıp **Tamam**.
* İçinde **yeni ASP.NET Core Web uygulaması - TodoApi** iletişim kutusunda, ASP.NET Core sürümünü seçin. Seçin **API** şablonu ve tıklatın **Tamam**. Yapmak **değil** seçin **Docker desteğini etkinleştir**.

### <a name="launch-the-app"></a>Uygulamayı başlatın

Visual Studio'da uygulamayı başlatmak için CTRL + F5 tuşlarına basın. Visual Studio bir tarayıcı ile başlatarak `http://localhost:<port>/api/values`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır. Chrome, Microsoft Edge ve Firefox aşağıdaki çıkışı görüntüler:

```json
["value1","value2"]
```

Internet Explorer kullanıyorsanız, kaydetmek için istenir bir *values.json* dosya.

### <a name="add-a-model-class"></a>Bir model sınıfı ekleme

Bir uygulamadaki verileri temsil eden bir nesne modelidir. Bu durumda, bir yapılacak iş öğesi tek model budur.

Çözüm Gezgini'nde projeye sağ tıklayın. Seçin **ekleme** > **yeni klasör**. Klasör adı *modelleri*.

> [!NOTE]
> Model sınıfları herhangi bir projede gidebilirsiniz. *Modelleri* klasör kural tarafından model sınıfları için kullanılır.

Çözüm Gezgini'nde sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**. Sınıf adı *Todoıtem* tıklatıp **Ekle**.

Güncelleştirme `TodoItem` aşağıdaki kodla sınıfı:

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Models/TodoItem.cs)]

Veritabanı oluşturur `Id` olduğunda bir `TodoItem` oluşturulur.

### <a name="create-the-database-context"></a>Veritabanı bağlamı oluşturur

*Veritabanı bağlamı* verilen veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır. Bu sınıf türetme tarafından oluşturulan `Microsoft.EntityFrameworkCore.DbContext` sınıfı.

Çözüm Gezgini'nde sağ *modelleri* klasörü ve select **Ekle** > **sınıfı**. Sınıf adı *TodoContext* tıklatıp **Ekle**.

Sınıfı, aşağıdaki kodla değiştirin:

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

### <a name="add-a-controller"></a>Denetleyici ekleme

Çözüm Gezgini'nde sağ *denetleyicileri* klasör. Seçin **ekleme** > **yeni öğe**. İçinde **Yeni Öğe Ekle** iletişim kutusunda **API denetleyici sınıfı** şablonu. Sınıf adı *TodoController*, tıklatıp **Ekle**.

![Yeni öğe iletişim denetleyicisiyle seçilen arama kutusu ve web API denetleyicisi Ekle](first-web-api/_static/new_controller.png)

Sınıfı, aşağıdaki kodla değiştirin:

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a>Uygulamayı başlatın

Visual Studio'da uygulamayı başlatmak için CTRL + F5 tuşlarına basın. Visual Studio bir tarayıcı ile başlatarak `http://localhost:<port>/api/values`burada `<port>` bir rastgele seçilen bağlantı noktası numarasıdır. Gidin `Todo` denetleyicisinde `http://localhost:<port>/api/todo`.

[!INCLUDE[last part of web API](../includes/webApi/end.md)]

[!INCLUDE[jQuery](../includes/webApi/add-jquery.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]
