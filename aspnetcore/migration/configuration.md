---
title: "Geçirme yapılandırma"
author: ardalis
description: 
keywords: "ASP.NET Çekirdeği"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 8468d859-ff32-4a92-9e62-08c4a9e36594
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/configuration
ms.openlocfilehash: d20235feec9d66c371b8ce0b7c66fb424fb261d5
ms.sourcegitcommit: 8f42ab93402c1b8044815e1e48d0bb84c81f8b59
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/29/2017
---
# <a name="migrating-configuration"></a><span data-ttu-id="6a43b-103">Geçirme yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6a43b-103">Migrating Configuration</span></span>

<span data-ttu-id="6a43b-104">Tarafından [Steve Smith](https://ardalis.com/) ve [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="6a43b-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="6a43b-105">Önceki makalede, biz başlangıcından [ASP.NET Core MVC için ASP.NET MVC projesinde geçirme](mvc.md).</span><span class="sxs-lookup"><span data-stu-id="6a43b-105">In the previous article, we began [migrating an ASP.NET MVC project to ASP.NET Core MVC](mvc.md).</span></span> <span data-ttu-id="6a43b-106">Bu makalede, yapılandırmasını geçir.</span><span class="sxs-lookup"><span data-stu-id="6a43b-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="6a43b-107">[Görüntülemek veya karşıdan örnek kod](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples) ([nasıl indirileceğini](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a43b-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="6a43b-108">Kurulum yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6a43b-108">Setup Configuration</span></span>

<span data-ttu-id="6a43b-109">ASP.NET Core artık kullanan *Global.asax* ve *web.config* ASP.NET önceki sürümlerinde kullanılan dosyaları.</span><span class="sxs-lookup"><span data-stu-id="6a43b-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="6a43b-110">Uygulama başlangıç mantığı yerleştirilen ASP.NET önceki sürümlerde bir `Application_StartUp` yöntemi içinde *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="6a43b-110">In earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="6a43b-111">Daha sonra ASP.NET MVC, bir *haline* dosya; proje kök dizininde dahil ve uygulama başlatıldığında çağrıldı.</span><span class="sxs-lookup"><span data-stu-id="6a43b-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="6a43b-112">ASP.NET Core geliştirmiştir bu yaklaşım tamamen tüm başlangıç mantığı koyarak *haline* dosya.</span><span class="sxs-lookup"><span data-stu-id="6a43b-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="6a43b-113">*Web.config* dosya ASP.NET Core de değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6a43b-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="6a43b-114">Yapılandırma kendisini artık yapılandırılabilir, açıklanan uygulaması başlangıç yordamını bir parçası olarak *haline*.</span><span class="sxs-lookup"><span data-stu-id="6a43b-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="6a43b-115">Yapılandırma hala XML dosyalarını kullanan, ancak genellikle ASP.NET Core projeleri yapılandırma değerlerini bir JSON biçimli dosyasında gibi yerleştirir *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6a43b-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="6a43b-116">ASP.NET Core'nın yapılandırma sistemi ortama özgü değerleri için daha güvenli ve sağlam bir konum sağlayabilir ortam değişkenlerini de kolayca erişebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6a43b-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a more secure and robust location for environment-specific values.</span></span> <span data-ttu-id="6a43b-117">Bu, özellikle bağlantı dizeleri ve kaynak denetimine işaretlenmemelidir API anahtarları gibi gizli anahtarları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="6a43b-117">This is especially true for secrets like connection strings and API keys that should not be checked into source control.</span></span> <span data-ttu-id="6a43b-118">Bkz: [yapılandırma](xref:fundamentals/configuration/index) ASP.NET çekirdek yapılandırması hakkında daha fazla bilgi edinmek için.</span><span class="sxs-lookup"><span data-stu-id="6a43b-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="6a43b-119">Bu makalede, biz kısmen geçirilen ASP.NET Core projeden ile başlıyorsanız [önceki makaleyi](mvc.md).</span><span class="sxs-lookup"><span data-stu-id="6a43b-119">For this article, we are starting with the partially-migrated ASP.NET Core project from [the previous article](mvc.md).</span></span> <span data-ttu-id="6a43b-120">Yapılandırma kurulumu, aşağıdaki oluşturucusu ve özelliğini eklemek için *haline* proje kök dizininde bulunan dosyası:</span><span class="sxs-lookup"><span data-stu-id="6a43b-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[Main](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-21)]

<span data-ttu-id="6a43b-121">Unutmayın, bu noktada, *haline* dosya derlenmez, biz aşağıdaki eklemek gerek duyduğunuz `using` deyimi:</span><span class="sxs-lookup"><span data-stu-id="6a43b-121">Note that at this point, the *Startup.cs* file will not compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="6a43b-122">Ekleme bir *appsettings.json* uygun öğeyi şablonunu kullanarak projenin kök dosyaya:</span><span class="sxs-lookup"><span data-stu-id="6a43b-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![AppSettings JSON ekleme](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="6a43b-124">Web.config yapılandırma ayarlarını geçirme</span><span class="sxs-lookup"><span data-stu-id="6a43b-124">Migrate Configuration Settings from web.config</span></span>

<span data-ttu-id="6a43b-125">ASP.NET MVC Projemizin gerekli veritabanı bağlantı dizesine dahil *web.config*, `<connectionStrings>` öğesi.</span><span class="sxs-lookup"><span data-stu-id="6a43b-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="6a43b-126">ASP.NET Core Projemizin Biz bu bilgileri depolamak için kalacaklarını *appsettings.json* dosya.</span><span class="sxs-lookup"><span data-stu-id="6a43b-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="6a43b-127">Açık *appsettings.json*, aşağıdakileri içeren, not edin:</span><span class="sxs-lookup"><span data-stu-id="6a43b-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[Main](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]


<span data-ttu-id="6a43b-128">Vurgulanan satırda, yukarıda gösterilen veritabanından adını değiştirmek **_CHANGE_ME** veritabanınızın adını.</span><span class="sxs-lookup"><span data-stu-id="6a43b-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="6a43b-129">Özet</span><span class="sxs-lookup"><span data-stu-id="6a43b-129">Summary</span></span>

<span data-ttu-id="6a43b-130">ASP.NET Core tüm başlangıç mantığı uygulama için bir tek, bağımlılıklar ve gerekli hizmetleri tanımlanan yapılandırılmış ve dosyasına yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="6a43b-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="6a43b-131">Yerini *web.config* dosya bir esnek yapılandırma özelliğiyle dosya biçimlerinde, ortam değişkenleri yanı sıra, JSON gibi çeşitli yararlanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6a43b-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
