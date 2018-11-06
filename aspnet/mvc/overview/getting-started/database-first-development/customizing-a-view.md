---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: 'İlk ASP.NET MVC ile EF veritabanında: bir görünümü özelleştirme | Microsoft Docs'
author: Rick-Anderson
description: MVC, Entity Framework ve ASP.NET iskeleti oluşturma kullanarak mevcut bir veritabanı için bir arabirim sunan bir web uygulaması oluşturabilirsiniz. Bu öğretici seri...
ms.author: riande
ms.date: 10/01/2014
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: f66e097d53514ab3842e04cd545ca626c652478a
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021216"
---
<a name="ef-database-first-with-aspnet-mvc-customizing-a-view"></a><span data-ttu-id="5648e-104">İlk ASP.NET MVC ile EF veritabanında: bir görünümünü özelleştirme</span><span class="sxs-lookup"><span data-stu-id="5648e-104">EF Database First with ASP.NET MVC: Customizing a View</span></span>
====================
<span data-ttu-id="5648e-105">tarafından [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="5648e-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="5648e-106">MVC, Entity Framework ve ASP.NET iskeleti oluşturma kullanarak mevcut bir veritabanı için bir arabirim sunan bir web uygulaması oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5648e-106">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="5648e-107">Bu öğretici serisinde, otomatik olarak kullanıcıların görüntüleme, düzenleme, oluşturma olanak sağlayan bir kod oluşturmak ve bir veritabanı tablosu, bulunan verileri silmek gösterilir.</span><span class="sxs-lookup"><span data-stu-id="5648e-107">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="5648e-108">Oluşturulan kod, veritabanı tablosundaki sütunlara karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="5648e-108">The generated code corresponds to the columns in the database table.</span></span>
> 
> <span data-ttu-id="5648e-109">Bu serinin sunu artırmak için otomatik olarak oluşturulan görünümler değişen odaklanır.</span><span class="sxs-lookup"><span data-stu-id="5648e-109">This part of the series focuses on changing the automatically-generated views to enhance the presentation.</span></span>


## <a name="add-enrolled-courses-to-student-details"></a><span data-ttu-id="5648e-110">Kayıtlı kursları Öğrenci ayrıntıları ekleyin</span><span class="sxs-lookup"><span data-stu-id="5648e-110">Add enrolled courses to student details</span></span>

<span data-ttu-id="5648e-111">Oluşturulan kod, uygulamanız için iyi bir başlangıç noktası sağlar, ancak bunu mutlaka tüm uygulamanızda ihtiyacınız olan işlevleri sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="5648e-111">The generated code provides a good starting point for your application but it does not necessarily provide all of the functionality that you need in your application.</span></span> <span data-ttu-id="5648e-112">Kod, uygulamanızın belirli gereksinimlerini karşılayacak şekilde özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5648e-112">You can customize the code to meet the particular requirements of your application.</span></span> <span data-ttu-id="5648e-113">Şu anda, uygulamanız için seçilen Öğrenci kayıtlı kursları görüntülemez.</span><span class="sxs-lookup"><span data-stu-id="5648e-113">Currently, your application does not display the enrolled courses for the selected student.</span></span> <span data-ttu-id="5648e-114">Bu bölümde, her öğrencinin için için kayıtlı kursları ekleyeceksiniz **ayrıntıları** Öğrenci için görünümü.</span><span class="sxs-lookup"><span data-stu-id="5648e-114">In this section, you will add the enrolled courses for each student to the **Details** view for the student.</span></span>

<span data-ttu-id="5648e-115">Açık **Students/Details.cshtml**ve son aşağıda &lt;/dl&gt; sekmesi, ancak kapatmadan önce &lt;/div&gt; etiketi, aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5648e-115">Open **Students/Details.cshtml**, and below the last &lt;/dl&gt; tab, but before the closing &lt;/div&gt; tag, add the following code.</span></span>

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

<span data-ttu-id="5648e-116">Bu kod, her kayıt için bir satır için seçilen Öğrenci kayıt tabloda görüntüleyen bir tablo oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5648e-116">This code creates a table that displays a row for each record in the Enrollment table for the selected student.</span></span> <span data-ttu-id="5648e-117">**Görünen** yöntemi HTML ifadeyi temsil eden nesne için (ModelItem) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5648e-117">The **Display** method renders HTML for the object (modelItem) that represents the expression.</span></span> <span data-ttu-id="5648e-118">Görüntüleme yöntemi kullanın (yerine yalnızca özellik değeri, kod ekleme) değeri, doğru türü ve şablon türüne göre biçimlendirildiğinde emin olmak için.</span><span class="sxs-lookup"><span data-stu-id="5648e-118">You use the Display method (rather than simply embedding the property value in the code) to make sure the value is formatted correctly based on its type and the template for that type.</span></span> <span data-ttu-id="5648e-119">Bu örnekte, her ifade tek bir özellik döngüsünde geçerli kaydı döndürür ve metin olarak işlenir, ilkel türler değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="5648e-119">In this example, each expression returns a single property from the current record in the loop, and the values are primitive types which are rendered as text.</span></span>

<span data-ttu-id="5648e-120">Öğrenciler/dizin görünüme tekrar göz atın ve seçim **ayrıntıları** Öğrenciler biri için.</span><span class="sxs-lookup"><span data-stu-id="5648e-120">Browse to the Students/Index view again and select **Details** for one of the students.</span></span> <span data-ttu-id="5648e-121">Kayıtlı kursları Görünümü'nde dahil edilmiş görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="5648e-121">You will see the enrolled courses have been included in the view.</span></span>

![Öğrenci kaydı](customizing-a-view/_static/image1.png)

> [!div class="step-by-step"]
> <span data-ttu-id="5648e-123">[Önceki](changing-the-database.md)
> [İleri](enhancing-data-validation.md)</span><span class="sxs-lookup"><span data-stu-id="5648e-123">[Previous](changing-the-database.md)
[Next](enhancing-data-validation.md)</span></span>
