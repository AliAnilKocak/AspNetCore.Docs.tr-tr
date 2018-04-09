---
title: ASP.NET Core bir Razor sayfasına dosyaları karşıya yükleme
author: guardrex
description: Bir Razor sayfasına dosyaları karşıya yükleme hakkında bilgi edinin.
manager: wpickett
ms.author: riande
ms.date: 09/12/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/uploading-files
ms.openlocfilehash: 6f229ef625b1c7ddaffb9cb3bc7945cc31e5263c
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2018
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="1e2ce-103">ASP.NET Core bir Razor sayfasına dosyaları karşıya yükleme</span><span class="sxs-lookup"><span data-stu-id="1e2ce-103">Upload files to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="1e2ce-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1e2ce-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1e2ce-105">Bu bölümde, Razor sayfasını içeren dosyaları karşıya yükleme gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-105">In this section, uploading files with a Razor Page is demonstrated.</span></span>

<span data-ttu-id="1e2ce-106">[Razor sayfalarının film örnek uygulaması](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) dosyaları karşıya yükleme bağlama Bu öğretici kullanan basit modelde çalıştığı iyi küçük dosyaları yüklemek için.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-106">The [Razor Pages Movie sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) in this tutorial uses simple model binding to upload files, which works well for uploading small files.</span></span> <span data-ttu-id="1e2ce-107">Büyük dosyaları akış hakkında daha fazla bilgi için bkz: [akış ile büyük dosyalar](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="1e2ce-107">For information on streaming large files, see [Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span></span>

<span data-ttu-id="1e2ce-108">Aşağıdaki adımlarda, bir filmi zamanlama dosya karşıya yükleme özelliği örnek uygulaması'na eklenir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-108">In the following steps, a movie schedule file upload feature is added to the sample app.</span></span> <span data-ttu-id="1e2ce-109">Bir filmi zamanlama tarafından temsil edilen bir `Schedule` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-109">A movie schedule is represented by a `Schedule` class.</span></span> <span data-ttu-id="1e2ce-110">Sınıfı, zamanlama iki sürümünü içerir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-110">The class includes two versions of the schedule.</span></span> <span data-ttu-id="1e2ce-111">Bir sürüm müşterilere sağlanan `PublicSchedule`.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-111">One version is provided to customers, `PublicSchedule`.</span></span> <span data-ttu-id="1e2ce-112">Başka bir sürüm şirket çalışanlarının kullanılan `PrivateSchedule`.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-112">The other version is used for company employees, `PrivateSchedule`.</span></span> <span data-ttu-id="1e2ce-113">Her bir sürümü ayrı bir dosya olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-113">Each version is uploaded as a separate file.</span></span> <span data-ttu-id="1e2ce-114">Öğretici iki dosya yüklemeleriyle tek bir POST ile bir sayfadan sunucuya nasıl gerçekleştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-114">The tutorial demonstrates how to perform two file uploads from a page with a single POST to the server.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="1e2ce-115">Güvenlik konuları</span><span class="sxs-lookup"><span data-stu-id="1e2ce-115">Security considerations</span></span>

<span data-ttu-id="1e2ce-116">Kullanıcılar bir sunucuya dosyaları karşıya yükleme olanağı sağlarken dikkat alınması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-116">Caution must be taken when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="1e2ce-117">Saldırganlar yürütme [hizmet reddi](/windows-hardware/drivers/ifs/denial-of-service) ve bir sistem diğer saldırılar.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-117">Attackers may execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) and other attacks on a system.</span></span> <span data-ttu-id="1e2ce-118">Başarılı bir saldırı olasılığını azaltmak bazı güvenlik adımlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-118">Some security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="1e2ce-119">Dosyaları karşıya yükleme adanmış dosya karşıya yükleme alanına sisteminde, karşıya yüklenen içerik üzerinde güvenlik önlemleri zorunlu tuttukları kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-119">Upload files to a dedicated file upload area on the system, which makes it easier to impose security measures on uploaded content.</span></span> <span data-ttu-id="1e2ce-120">Dosya yüklemeleri sorgulamasına olduğunda, yürütme izinleri emin olun karşıya yükleme konumuna devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-120">When permitting file uploads, make sure that execute permissions are disabled on the upload location.</span></span>
* <span data-ttu-id="1e2ce-121">Uygulamadan değil kullanıcı girişi tarafından belirlenen bir güvenli dosya adı veya karşıya yüklenen dosyanın dosya adı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-121">Use a safe file name determined by the app, not from user input or the file name of the uploaded file.</span></span>
* <span data-ttu-id="1e2ce-122">Yalnızca onaylanan dosya uzantılarını belirli bir dizi izin verir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-122">Only allow a specific set of approved file extensions.</span></span>
* <span data-ttu-id="1e2ce-123">İstemci-tarafı denetimleri sunucu üzerinde gerçekleştirilen doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-123">Verify client-side checks are performed on the server.</span></span> <span data-ttu-id="1e2ce-124">İstemci-tarafı denetimleri aşmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-124">Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="1e2ce-125">Karşıya yükleme boyutunu denetlemek ve beklenenden daha büyük yüklemeler engelleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-125">Check the size of the upload and prevent larger uploads than expected.</span></span>
* <span data-ttu-id="1e2ce-126">Virüs/kötü amaçlı yazılım tarayıcı karşıya yüklenen içerikte çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-126">Run a virus/malware scanner on uploaded content.</span></span>

> [!WARNING]
> <span data-ttu-id="1e2ce-127">Kötü amaçlı kod bir sisteme karşıya sık yapabilirsiniz kod yürütmek için ilk adımdır:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-127">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
> * <span data-ttu-id="1e2ce-128">Tamamen devralma sistemin.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-128">Completely takeover a system.</span></span>
> * <span data-ttu-id="1e2ce-129">Sistem tamamen başarısız sonucu sistemiyle aşırı yükleme.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-129">Overload a system with the result that the system completely fails.</span></span>
> * <span data-ttu-id="1e2ce-130">Kullanıcı veya sistem veri tehlikeye.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-130">Compromise user or system data.</span></span>
> * <span data-ttu-id="1e2ce-131">Graffiti ortak bir arabirim için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-131">Apply graffiti to a public interface.</span></span>

## <a name="add-a-fileupload-class"></a><span data-ttu-id="1e2ce-132">Dosya yükleme sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="1e2ce-132">Add a FileUpload class</span></span>

<span data-ttu-id="1e2ce-133">Dosya yüklemeleri çifti işlemek için bir Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-133">Create a Razor Page to handle a pair of file uploads.</span></span> <span data-ttu-id="1e2ce-134">Ekleme bir `FileUpload` zamanlama verileri elde etmek için sayfaya bağlı sınıfı.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-134">Add a `FileUpload` class, which is bound to the page to obtain the schedule data.</span></span> <span data-ttu-id="1e2ce-135">Sağ tıklayın *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-135">Right click the *Models* folder.</span></span> <span data-ttu-id="1e2ce-136">Seçin **ekleme** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-136">Select **Add** > **Class**.</span></span> <span data-ttu-id="1e2ce-137">Sınıf adını **dosya yükleme** ve aşağıdaki özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-137">Name the class **FileUpload** and add the following properties:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/FileUpload.cs)]

<span data-ttu-id="1e2ce-138">Sınıfı, zamanlamanın başlık özelliğini ve her iki sürümü zamanlama için bir özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-138">The class has a property for the schedule's title and a property for each of the two versions of the schedule.</span></span> <span data-ttu-id="1e2ce-139">Tüm üç özellikleri gereklidir ve başlığı 3-60 karakter uzunluğunda olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-139">All three properties are required, and the title must be 3-60 characters long.</span></span>

## <a name="add-a-helper-method-to-upload-files"></a><span data-ttu-id="1e2ce-140">Dosyaları karşıya yüklemek için bir yardımcı yöntemi ekleyin</span><span class="sxs-lookup"><span data-stu-id="1e2ce-140">Add a helper method to upload files</span></span>

<span data-ttu-id="1e2ce-141">Karşıya yüklenen zamanlama dosyalarını işlemek için kod yinelemesinden kaçınmak için önce bir statik yardımcı yöntemi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-141">To avoid code duplication for processing uploaded schedule files, add a static helper method first.</span></span> <span data-ttu-id="1e2ce-142">Oluşturma bir *yardımcı programları* uygulama klasöründe ve ekleme bir *FileHelpers.cs* dosya aşağıdaki içeriğe sahip.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-142">Create a *Utilities* folder in the app and add a *FileHelpers.cs* file with the following content.</span></span> <span data-ttu-id="1e2ce-143">Yardımcı yöntemi `ProcessFormFile`, alan bir [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) ve [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) ve dosyanın boyutu ve içeriğini içeren bir dize döndürür.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-143">The helper method, `ProcessFormFile`, takes an [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) and returns a string containing the file's size and content.</span></span> <span data-ttu-id="1e2ce-144">İçerik türü ve uzunluğu denetlenir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-144">The content type and length are checked.</span></span> <span data-ttu-id="1e2ce-145">Dosya bir doğrulama denetimi geçmiyor, bir hata eklenen `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-145">If the file doesn't pass a validation check, an error is added to the `ModelState`.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Utilities/FileHelpers.cs)]

### <a name="save-the-file-to-disk"></a><span data-ttu-id="1e2ce-146">Dosyayı diske kaydedin</span><span class="sxs-lookup"><span data-stu-id="1e2ce-146">Save the file to disk</span></span>

<span data-ttu-id="1e2ce-147">Örnek uygulaması dosyanın içeriğini veritabanı alanına kaydeder.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-147">The sample app saves the file's content into a database field.</span></span> <span data-ttu-id="1e2ce-148">Dosyanın içeriğini diske kaydetmek için kullanın bir [FILESTREAM](/dotnet/api/system.io.filestream):</span><span class="sxs-lookup"><span data-stu-id="1e2ce-148">To save the file's content to disk, use a [FileStream](/dotnet/api/system.io.filestream):</span></span>

```csharp
using (var fileStream = new FileStream(filePath, FileMode.Create))
{
    await formFile.CopyToAsync(fileStream);
}
```

<span data-ttu-id="1e2ce-149">Çalışan işlemi tarafından belirtilen konuma yazma izinlerine sahip olmalıdır `filePath`.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-149">The worker process must have write permissions to the location specified by `filePath`.</span></span>

### <a name="save-the-file-to-azure-blob-storage"></a><span data-ttu-id="1e2ce-150">Dosyayı Azure Blob depolama alanına kaydedin</span><span class="sxs-lookup"><span data-stu-id="1e2ce-150">Save the file to Azure Blob Storage</span></span>

<span data-ttu-id="1e2ce-151">Azure Blob Depolama birimine dosya içerik yüklemek için bkz: [.NET kullanarak Azure Blob Storage ile çalışmaya başlama](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span><span class="sxs-lookup"><span data-stu-id="1e2ce-151">To upload file content to Azure Blob Storage, see [Get started with Azure Blob Storage using .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span></span> <span data-ttu-id="1e2ce-152">Nasıl kullanılacağı konusunda ortaya [UploadFromStream](/dotnet/api/microsoft.windowsazure.storage.file.cloudfile.uploadfromstreamasync) kaydetmek için bir [FILESTREAM](/dotnet/api/system.io.filestream) blob depolama.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-152">The topic demonstrates how to use [UploadFromStream](/dotnet/api/microsoft.windowsazure.storage.file.cloudfile.uploadfromstreamasync) to save a [FileStream](/dotnet/api/system.io.filestream) to blob storage.</span></span>

## <a name="add-the-schedule-class"></a><span data-ttu-id="1e2ce-153">Zamanlama sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="1e2ce-153">Add the Schedule class</span></span>

<span data-ttu-id="1e2ce-154">Sağ tıklayın *modelleri* klasör.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-154">Right click the *Models* folder.</span></span> <span data-ttu-id="1e2ce-155">Seçin **ekleme** > **sınıfı**.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-155">Select **Add** > **Class**.</span></span> <span data-ttu-id="1e2ce-156">Sınıf adını **zamanlama** ve aşağıdaki özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-156">Name the class **Schedule** and add the following properties:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/Schedule.cs)]

<span data-ttu-id="1e2ce-157">Sınıf kullandığı `Display` ve `DisplayFormat` kolay başlıkları ve zamanlama veri işlendiğinde biçimlendirme oluşturur özniteliklerini.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-157">The class uses `Display` and `DisplayFormat` attributes, which produce friendly titles and formatting when the schedule data is rendered.</span></span>

## <a name="update-the-moviecontext"></a><span data-ttu-id="1e2ce-158">Güncelleştirme MovieContext</span><span class="sxs-lookup"><span data-stu-id="1e2ce-158">Update the MovieContext</span></span>

<span data-ttu-id="1e2ce-159">Belirtin bir `DbSet` içinde `MovieContext` (*Models/MovieContext.cs*) tabloları için:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-159">Specify a `DbSet` in the `MovieContext` (*Models/MovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

## <a name="add-the-schedule-table-to-the-database"></a><span data-ttu-id="1e2ce-160">Zamanlama tablo veritabanına ekleyin</span><span class="sxs-lookup"><span data-stu-id="1e2ce-160">Add the Schedule table to the database</span></span>

<span data-ttu-id="1e2ce-161">(PMC) Paket Yöneticisi konsolunu açın: **Araçları** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu**.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-161">Open the Package Manger Console (PMC): **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1e2ce-163">PMC aşağıdaki komutları yürütün.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-163">In the PMC, execute the following commands.</span></span> <span data-ttu-id="1e2ce-164">Bu komutlar ekleme bir `Schedule` veritabanı tablosuna:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-164">These commands add a `Schedule` table to the database:</span></span>

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a><span data-ttu-id="1e2ce-165">Bir dosyayı karşıya yükleme Razor sayfası ekleme</span><span class="sxs-lookup"><span data-stu-id="1e2ce-165">Add a file upload Razor Page</span></span>

<span data-ttu-id="1e2ce-166">İçinde *sayfaları* klasörü oluşturmak bir *zamanlamaları* klasör.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-166">In the *Pages* folder, create a *Schedules* folder.</span></span> <span data-ttu-id="1e2ce-167">İçinde *zamanlamaları* klasörünü adlı bir sayfa oluşturma *Index.cshtml* aşağıdaki içeriğe sahip bir zamanlama karşıya yükleme için:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-167">In the *Schedules* folder, create a page named *Index.cshtml* for uploading a schedule with the following content:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

<span data-ttu-id="1e2ce-168">Her form grubu içeren bir  **\<etiketi >** her sınıf özelliğinin adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-168">Each form group includes a **\<label>** that displays the name of each class property.</span></span> <span data-ttu-id="1e2ce-169">`Display` Öznitelikleri `FileUpload` modeli etiketlerini görüntüleme değerleri belirtin.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-169">The `Display` attributes in the `FileUpload` model provide the display values for the labels.</span></span> <span data-ttu-id="1e2ce-170">Örneğin, `UploadPublicSchedule` özelliğin görünen adı ayarlandığında `[Display(Name="Public Schedule")]` ve formu işleyen böylece "Ortak zamanlama" etiketi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-170">For example, the `UploadPublicSchedule` property's display name is set with `[Display(Name="Public Schedule")]` and thus displays "Public Schedule" in the label when the form renders.</span></span>

<span data-ttu-id="1e2ce-171">Her form grubu bir doğrulama içeren  **\<span >**.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-171">Each form group includes a validation **\<span>**.</span></span> <span data-ttu-id="1e2ce-172">Kullanıcı karşılamak üzere başarısız giriş olması durumunda özellik öznitelikleri kümesinde `FileUpload` sınıfı veya varsa `ProcessFormFile` yöntemi dosya doğrulama başarısız denetler, model doğrulamak başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-172">If the user's input fails to meet the property attributes set in the `FileUpload` class or if any of the `ProcessFormFile` method file validation checks fail, the model fails to validate.</span></span> <span data-ttu-id="1e2ce-173">Model doğrulama başarısız olduğunda, bir yardımcı doğrulama ileti kullanıcıya işlenir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-173">When model validation fails, a helpful validation message is rendered to the user.</span></span> <span data-ttu-id="1e2ce-174">Örneğin, `Title` özellik açıklama ile `[Required]` ve `[StringLength(60, MinimumLength = 3)]`.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-174">For example, the `Title` property is annotated with `[Required]` and `[StringLength(60, MinimumLength = 3)]`.</span></span> <span data-ttu-id="1e2ce-175">Kullanıcı bir başlık sağlamanız başarısız olursa, bir değer gerekli olduğunu belirten bir ileti alırlar.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-175">If the user fails to supply a title, they receive a message indicating that a value is required.</span></span> <span data-ttu-id="1e2ce-176">Kullanıcı değerinden üç veya daha fazla altmış karakter değeri girerse, bunlar değeri yanlış bir uzunluğa sahip olduğunu belirten bir ileti alırsınız.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-176">If the user enters a value less than three characters or more than sixty characters, they receive a message indicating that the value has an incorrect length.</span></span> <span data-ttu-id="1e2ce-177">İçeriği yok sağlanan bir dosya varsa, dosyayı boş olduğunu belirten bir ileti görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-177">If a file is provided that has no content, a message appears indicating that the file is empty.</span></span>

## <a name="add-the-page-model"></a><span data-ttu-id="1e2ce-178">Sayfa modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="1e2ce-178">Add the page model</span></span>

<span data-ttu-id="1e2ce-179">Sayfa modeli ekleme (*Index.cshtml.cs*) için *zamanlamaları* klasörü:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-179">Add the page model (*Index.cshtml.cs*) to the *Schedules* folder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

<span data-ttu-id="1e2ce-180">Sayfa modeli (`IndexModel` içinde *Index.cshtml.cs*) bağlar `FileUpload` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-180">The page model (`IndexModel` in *Index.cshtml.cs*) binds the `FileUpload` class:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1e2ce-181">Model zamanlamalar listesini de kullanır (`IList<Schedule>`) sayfasında veritabanında depolanan zamanlamaları görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-181">The model also uses a list of the schedules (`IList<Schedule>`) to display the schedules stored in the database on the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1e2ce-182">Sayfa yüklediğinde ile `OnGetAsync`, `Schedules` veritabanından doldurulur ve yüklenen zamanlamalar HTML tablosu oluşturmak için kullanılan:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-182">When the page loads with `OnGetAsync`, `Schedules` is populated from the database and used to generate an HTML table of loaded schedules:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

<span data-ttu-id="1e2ce-183">Sunucuya form gönderildiğinde `ModelState` denetlenir.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-183">When the form is posted to the server, the `ModelState` is checked.</span></span> <span data-ttu-id="1e2ce-184">Geçersiz, `Schedule` yeniden oluşturulur ve sayfa doğrulamayı neden geçemediğini belirten bir veya daha fazla doğrulama iletileri ile sayfasını işler.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-184">If invalid, `Schedule` is rebuilt, and the page renders with one or more validation messages stating why page validation failed.</span></span> <span data-ttu-id="1e2ce-185">Geçerliyse, `FileUpload` özellikleri kullanıldığı *OnPostAsync* zamanlama iki sürümleri için dosya karşıya yükleme işlemini tamamlamak için ve yeni bir oluşturmak için `Schedule` verileri depolamak için nesne.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-185">If valid, the `FileUpload` properties are used in *OnPostAsync* to complete the file upload for the two versions of the schedule and to create a new `Schedule` object to store the data.</span></span> <span data-ttu-id="1e2ce-186">Zamanlama sonra veritabanına kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-186">The schedule is then saved to the database:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

## <a name="link-the-file-upload-razor-page"></a><span data-ttu-id="1e2ce-187">Dosya karşıya yükleme Razor sayfasını bağlantı</span><span class="sxs-lookup"><span data-stu-id="1e2ce-187">Link the file upload Razor Page</span></span>

<span data-ttu-id="1e2ce-188">Açık *_Layout.cshtml* ve bir bağlantı dosya karşıya yükleme sayfasına ulaşmak için gezinti çubuğu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-188">Open *_Layout.cshtml* and add a link to the navigation bar to reach the file upload page:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=31-38&highlight=4)]

## <a name="add-a-page-to-confirm-schedule-deletion"></a><span data-ttu-id="1e2ce-189">Zamanlama silmeyi onaylamak için bir sayfa ekleyin</span><span class="sxs-lookup"><span data-stu-id="1e2ce-189">Add a page to confirm schedule deletion</span></span>

<span data-ttu-id="1e2ce-190">Kullanıcı bir zamanlama silinecek tıklattığında işlemi iptal etmek için bir fırsat sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-190">When the user clicks to delete a schedule, a chance to cancel the operation is provided.</span></span> <span data-ttu-id="1e2ce-191">Silme onayı sayfası ekleme (*Delete.cshtml*) için *zamanlamaları* klasörü:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-191">Add a delete confirmation page (*Delete.cshtml*) to the *Schedules* folder:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

<span data-ttu-id="1e2ce-192">Sayfa modeli (*Delete.cshtml.cs*) tarafından tanımlanan tek bir zamanlama yükler `id` isteğin rota verilerindeki.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-192">The page model (*Delete.cshtml.cs*) loads a single schedule identified by `id` in the request's route data.</span></span> <span data-ttu-id="1e2ce-193">Ekleme *Delete.cshtml.cs* dosya *zamanlamaları* klasörü:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-193">Add the *Delete.cshtml.cs* file to the *Schedules* folder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

<span data-ttu-id="1e2ce-194">`OnPostAsync` Yöntemi işler tarafından zamanlama silinirken kendi `id`:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-194">The `OnPostAsync` method handles deleting the schedule by its `id`:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

<span data-ttu-id="1e2ce-195">Zamanlama başarıyla sildikten sonra `RedirectToPage` zamanlamaları için kullanıcı gönderir *Index.cshtml* sayfası.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-195">After successfully deleting the schedule, the `RedirectToPage` sends the user back to the schedules *Index.cshtml* page.</span></span>

## <a name="the-working-schedules-razor-page"></a><span data-ttu-id="1e2ce-196">Zamanlamalar Razor sayfasını çalışma</span><span class="sxs-lookup"><span data-stu-id="1e2ce-196">The working Schedules Razor Page</span></span>

<span data-ttu-id="1e2ce-197">Etiketleri ve girişleri zamanlama başlık, sayfa yüklendiğinde ortak zamanlama ve özel zamanlama bir gönderme düğmesi ile işlenir:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-197">When the page loads, labels and inputs for schedule title, public schedule, and private schedule are rendered with a submit button:</span></span>

![Hiçbir doğrulama hataları ve boş alanları ile ilk yükü görülen Razor sayfasını zamanlar](uploading-files/_static/browser1.png)

<span data-ttu-id="1e2ce-199">Seçme **karşıya** tüm alanları doldurmak ihlal olmadan düğmesini `[Required]` model üzerinde öznitelikleri.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-199">Selecting the **Upload** button without populating any of the fields violates the `[Required]` attributes on the model.</span></span> <span data-ttu-id="1e2ce-200">`ModelState` Geçersiz.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-200">The `ModelState` is invalid.</span></span> <span data-ttu-id="1e2ce-201">Kullanıcı için bir doğrulama hata iletisi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-201">The validation error messages are displayed to the user:</span></span>

![Her giriş denetiminin yanındaki bir doğrulama hata iletisi görüntülenir](uploading-files/_static/browser2.png)

<span data-ttu-id="1e2ce-203">İki harf içine yazın **başlık** alan.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-203">Type two letters into the **Title** field.</span></span> <span data-ttu-id="1e2ce-204">Başlık 3-60 karakter arasında olması gerektiğini belirtmek için doğrulama ileti değişiklikler:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-204">The validation message changes to indicate that the title must be between 3-60 characters:</span></span>

![Başlık doğrulama ileti değiştirildi](uploading-files/_static/browser3.png)

<span data-ttu-id="1e2ce-206">Bir veya daha fazla Zamanlama yüklenirken **yüklenen zamanlamaları** bölüm yüklenen zamanlamaları işler:</span><span class="sxs-lookup"><span data-stu-id="1e2ce-206">When one or more schedules are uploaded, the **Loaded Schedules** section renders the loaded schedules:</span></span>

![Yüklenen zamanlamaları, her zamanlamanın başlık gösteren tablosunun tarihi UTC, genel bir sürümü dosya boyutu ve özel sürüm dosya boyutu karşıya](uploading-files/_static/browser4.png)

<span data-ttu-id="1e2ce-208">Kullanıcı tıklatabilirsiniz **silmek** buradan onaylayın ya da silme işlemini iptal etmek için bir fırsat sahip oldukları silme onayı görünüm ulaşmak için bağlantı.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-208">The user can click the **Delete** link from there to reach the delete confirmation view, where they have an opportunity to confirm or cancel the delete operation.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1e2ce-209">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="1e2ce-209">Troubleshooting</span></span>

<span data-ttu-id="1e2ce-210">Sorun giderme bilgileri ile `IFormFile` yüklemek bkz [dosya yüklemeleri ASP.NET Core: sorun giderme](xref:mvc/models/file-uploads#troubleshooting).</span><span class="sxs-lookup"><span data-stu-id="1e2ce-210">For troubleshooting information with `IFormFile` uploading, see the [File uploads in ASP.NET Core: Troubleshooting](xref:mvc/models/file-uploads#troubleshooting).</span></span>

<span data-ttu-id="1e2ce-211">Bu giriş Razor sayfalarının tamamlamak için teşekkür ederiz.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-211">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="1e2ce-212">Geri bildirim veriyoruz.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-212">We appreciate feedback.</span></span> <span data-ttu-id="1e2ce-213">[MVC ve EF çekirdek kullanmaya başlama](xref:data/ef-mvc/intro) mükemmel bir izleme Bu öğretici kadar olan.</span><span class="sxs-lookup"><span data-stu-id="1e2ce-213">[Get started with MVC and EF Core](xref:data/ef-mvc/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e2ce-214">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1e2ce-214">Additional resources</span></span>

* [<span data-ttu-id="1e2ce-215">ASP.NET Core dosya yüklemeleri</span><span class="sxs-lookup"><span data-stu-id="1e2ce-215">File uploads in ASP.NET Core</span></span>](xref:mvc/models/file-uploads)
* [<span data-ttu-id="1e2ce-216">IFormFile</span><span class="sxs-lookup"><span data-stu-id="1e2ce-216">IFormFile</span></span>](/dotnet/api/microsoft.aspnetcore.http.iformfile)

> [!div class="step-by-step"]
> [<span data-ttu-id="1e2ce-217">Önceki: doğrulama</span><span class="sxs-lookup"><span data-stu-id="1e2ce-217">Previous: Validation</span></span>](xref:tutorials/razor-pages/validation)
