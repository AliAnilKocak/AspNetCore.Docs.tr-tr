---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
title: "ASP.NET Web sayfaları sunarak - veritabanı verilerini silme | Microsoft Docs"
author: tfitzmac
description: "Bu öğretici bir tek tek veritabanı girişi silmek gösterilmiştir. Bu, veritabanı verilerde güncelleştirme ASP.NET Web Pa aracılığıyla serisini tamamladınız varsayar..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: 75b5c1cf-84bd-434f-8a86-85c568eb5b09
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
msc.type: authoredcontent
ms.openlocfilehash: aef31b6170cc3bba2421eb8c2c41e83aadc129c5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="introducing-aspnet-web-pages---deleting-database-data"></a><span data-ttu-id="b1686-104">ASP.NET Web sayfaları sunarak - veritabanı verileri silme</span><span class="sxs-lookup"><span data-stu-id="b1686-104">Introducing ASP.NET Web Pages - Deleting Database Data</span></span>
====================
<span data-ttu-id="b1686-105">tarafından [zel FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="b1686-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="b1686-106">Bu öğretici bir tek tek veritabanı girişi silmek gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b1686-106">This tutorial shows you how to delete an individual database entry.</span></span> <span data-ttu-id="b1686-107">Seri aracılığıyla tamamladığınızdan varsayar [güncelleştirme veritabanı veri ASP.NET Web Pages'de](https://go.microsoft.com/fwlink/?LinkId=251583).</span><span class="sxs-lookup"><span data-stu-id="b1686-107">It assumes you have completed the series through [Updating Database Data in ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251583).</span></span>
> 
> <span data-ttu-id="b1686-108">Öğrenecekleriniz:</span><span class="sxs-lookup"><span data-stu-id="b1686-108">What you'll learn:</span></span>
> 
> - <span data-ttu-id="b1686-109">Tek bir kaydın listesini kayıtları seçmek nasıl.</span><span class="sxs-lookup"><span data-stu-id="b1686-109">How to select an individual record from a listing of records.</span></span>
> - <span data-ttu-id="b1686-110">Tek kayıtlı bir veritabanından silmek nasıl.</span><span class="sxs-lookup"><span data-stu-id="b1686-110">How to delete a single record from a database.</span></span>
> - <span data-ttu-id="b1686-111">Belirli bir düğme bir formda tıklandığını kontrol etme.</span><span class="sxs-lookup"><span data-stu-id="b1686-111">How to check that a specific button was clicked in a form.</span></span>
>   
> 
> <span data-ttu-id="b1686-112">Özellikler/teknolojilerini ele alınan:</span><span class="sxs-lookup"><span data-stu-id="b1686-112">Features/technologies discussed:</span></span>
> 
> - <span data-ttu-id="b1686-113">`WebGrid` Yardımcısı.</span><span class="sxs-lookup"><span data-stu-id="b1686-113">The `WebGrid` helper.</span></span>
> - <span data-ttu-id="b1686-114">SQL `Delete` komutu.</span><span class="sxs-lookup"><span data-stu-id="b1686-114">The SQL `Delete` command.</span></span>
> - <span data-ttu-id="b1686-115">`Database.Execute` Bir SQL çalıştırılacak yöntemi `Delete` komutu.</span><span class="sxs-lookup"><span data-stu-id="b1686-115">The `Database.Execute` method to run a SQL `Delete` command.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="b1686-116">Ne oluşturacağınız</span><span class="sxs-lookup"><span data-stu-id="b1686-116">What You'll Build</span></span>

<span data-ttu-id="b1686-117">Önceki öğreticide, var olan bir veritabanı kaydını güncelleştirmek öğrendiniz.</span><span class="sxs-lookup"><span data-stu-id="b1686-117">In the previous tutorial, you learned how to update an existing database record.</span></span> <span data-ttu-id="b1686-118">Kayıt güncelleme yerine onu silersiniz Bu öğretici, benzerdir.</span><span class="sxs-lookup"><span data-stu-id="b1686-118">This tutorial is similar, except that instead of updating the record, you'll delete it.</span></span> <span data-ttu-id="b1686-119">Bu öğretici kısa olacak şekilde silme daha basit, olması dışında işlemleri çok aynıdır.</span><span class="sxs-lookup"><span data-stu-id="b1686-119">The processes are much the same, except that deleting is simpler, so this tutorial will be short.</span></span>

<span data-ttu-id="b1686-120">İçinde *filmler* update sayfasında, `WebGrid` onun görüntüler için yardımcı bir **silmek** eşlik etmek üzere her film yanındaki bağlantı **Düzenle** daha önce eklediğiniz bağlantı.</span><span class="sxs-lookup"><span data-stu-id="b1686-120">In the *Movies* page, you'll update the `WebGrid` helper so that it displays a **Delete** link next to each movie to accompany the **Edit** link you added earlier.</span></span>

![Her film için Sil bağlantısını gösteren filmler sayfası](deleting-data/_static/image1.png)

<span data-ttu-id="b1686-122">Düzenleme gibi ile tıkladığınızda **silmek** bağlantı sürdüğünü, farklı bir sayfaya film bilgileri zaten bir formda olduğu:</span><span class="sxs-lookup"><span data-stu-id="b1686-122">As with editing, when you click the **Delete** link, it takes you to a different page, where the movie information is already in a form:</span></span>

![Görüntülenen bir filmi film sayfayı silin](deleting-data/_static/image2.png)

<span data-ttu-id="b1686-124">Ardından, kayıt kalıcı olarak silmek için düğmeyi tıklatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b1686-124">You can then click the button to delete the record permanently.</span></span>

## <a name="adding-a-delete-link-to-the-movie-listing"></a><span data-ttu-id="b1686-125">Film listenin Delete bağlantı ekleme</span><span class="sxs-lookup"><span data-stu-id="b1686-125">Adding a Delete Link to the Movie Listing</span></span>

<span data-ttu-id="b1686-126">Ekleyerek başlayacaksınız bir **silmek** bağlantı `WebGrid` Yardımcısı.</span><span class="sxs-lookup"><span data-stu-id="b1686-126">You'll start by adding a **Delete** link to the `WebGrid` helper.</span></span> <span data-ttu-id="b1686-127">Bu bağlantıyı benzer **Düzenle** önceki öğreticide eklediğiniz bağlantı.</span><span class="sxs-lookup"><span data-stu-id="b1686-127">This link is similar to the **Edit** link you added in a previous tutorial.</span></span>

<span data-ttu-id="b1686-128">Açık *Movies.cshtml* dosya.</span><span class="sxs-lookup"><span data-stu-id="b1686-128">Open the *Movies.cshtml* file.</span></span>

<span data-ttu-id="b1686-129">Değişiklik `WebGrid` bir sütunu ekleyerek sayfasının gövdesindeki biçimlendirme.</span><span class="sxs-lookup"><span data-stu-id="b1686-129">Change the `WebGrid` markup in the body of the page by adding a column.</span></span> <span data-ttu-id="b1686-130">Değiştirilen biçimlendirme aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="b1686-130">Here's the modified markup:</span></span>

[!code-html[Main](deleting-data/samples/sample1.html?highlight=9-10)]

<span data-ttu-id="b1686-131">Yeni bir sütun bu bilgisayardır:</span><span class="sxs-lookup"><span data-stu-id="b1686-131">The new column is this one:</span></span>

[!code-html[Main](deleting-data/samples/sample2.html)]

<span data-ttu-id="b1686-132">Kılavuz yapılandırılır, yol **Düzenle** sütundur kılavuzda soldaki ve **silmek** en sağdaki sütun.</span><span class="sxs-lookup"><span data-stu-id="b1686-132">The way the grid is configured, the **Edit** column is leftmost in the grid and the **Delete** column is rightmost.</span></span> <span data-ttu-id="b1686-133">(Sonra bir virgül `Year` şimdi sütun durumunda, fark etmemiştir.) Bu bağlantı sütunları nereye özel bir şey yoktur ve kolayca bunları birbirinin yanına yerleştirdiğiniz.</span><span class="sxs-lookup"><span data-stu-id="b1686-133">(There's a comma after the `Year` column now, in case you didn't notice that.) There's nothing special about where these link columns go, and you could as easily put them next to each other.</span></span> <span data-ttu-id="b1686-134">Bu durumda, kullanıcılar bunları karma daha zor hale getirmek için ayrı.</span><span class="sxs-lookup"><span data-stu-id="b1686-134">In this case, they're separate to make them harder to get mixed up.</span></span>

![Birbirinin yanına olmadıklarını olduğunu göstermek için filmler sayfa düzenleme ve ayrıntıları bağlantıları ile işaretlenmiş](deleting-data/_static/image3.png)

<span data-ttu-id="b1686-136">Yeni bir sütun bağlantıyı gösterir (`<a>` öğesi) "Delete" metnini söyler.</span><span class="sxs-lookup"><span data-stu-id="b1686-136">The new column shows a link (`<a>` element) whose text says "Delete".</span></span> <span data-ttu-id="b1686-137">Bağlantı hedefi (kendi `href` özniteliği) sonuçta bu URL şöyle ile çözümler kodu `id` her film için farklı bir değer:</span><span class="sxs-lookup"><span data-stu-id="b1686-137">The target of the link (its `href` attribute) is code that ultimately resolves to something like this URL, with the `id` value different for each movie:</span></span>

[!code-css[Main](deleting-data/samples/sample3.css)]

<span data-ttu-id="b1686-138">Bu bağlantıyı adlı bir sayfaya çağıracağı *DeleteMovie* ve seçtiğiniz film Kimliğini geçirin.</span><span class="sxs-lookup"><span data-stu-id="b1686-138">This link will invoke a page named *DeleteMovie* and pass it the ID of the movie you've selected.</span></span>

<span data-ttu-id="b1686-139">Neredeyse aynı olduğu için bu öğreticiyi bu bağlantıyı nasıl oluşturulur, ayrıntılı gitmesi olmaz **Düzenle** önceki öğretici bağlantısından ([güncelleştirme veritabanı veri ASP.NET Web Pages'de](https://go.microsoft.com/fwlink/?LinkId=251583)).</span><span class="sxs-lookup"><span data-stu-id="b1686-139">This tutorial won't go into detail about how this link is constructed, because it's almost identical to the **Edit** link from the previous tutorial ([Updating Database Data in ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251583)).</span></span>

## <a name="creating-the-delete-page"></a><span data-ttu-id="b1686-140">Delete sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="b1686-140">Creating the Delete Page</span></span>

<span data-ttu-id="b1686-141">Hedef olacaktır sayfası oluşturabilirsiniz artık **silmek** kılavuzunda bağlantı.</span><span class="sxs-lookup"><span data-stu-id="b1686-141">Now you can create the page that will be the target for the **Delete** link in the grid.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="b1686-142">**Önemli** ilk silmek için bir kayıt seçerek ve sonra işlemi onaylamak için ayrı bir sayfa ve düğmesini kullanarak tekniği güvenlik için son derece önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b1686-142">**Important** The technique of first selecting a record to delete and then using a separate page and button to confirm the process is extremely important for security.</span></span> <span data-ttu-id="b1686-143">Önceki eğitimlerine okuduğunuz gibi yapma *herhangi* tür değişiklik sitenizin gereken *her zaman* yapılması formu kullanarak &mdash; diğer bir deyişle, bir HTTP POST işlemini kullanarak.</span><span class="sxs-lookup"><span data-stu-id="b1686-143">As you've read in previous tutorials, making *any* sort of change to your website should *always* be done using a form &mdash; that is, using an HTTP POST operation.</span></span> <span data-ttu-id="b1686-144">Site (GET işlemi kullanan) bir bağlantıya tıklayarak değiştirmek mümkün hale, kişiler, sitenize basit isteklerde ve verilerinizi silmek.</span><span class="sxs-lookup"><span data-stu-id="b1686-144">If you made it possible to change the site just by clicking a link (that is, using a GET operation), people could make simple requests to your site and delete your data.</span></span> <span data-ttu-id="b1686-145">Bile, sitenizin dizinini bir arama motoru Gezgin yalnızca aşağıdaki bağlantılar tarafından yanlışlıkla veri silinemedi.</span><span class="sxs-lookup"><span data-stu-id="b1686-145">Even a search-engine crawler that's indexing your site could inadvertently delete data just by following links.</span></span>
> 
> <span data-ttu-id="b1686-146">Uygulamanızı bir kaydı değiştirme kişiler sağlar, yine de düzenleme için kullanıcıya kaydı sunmak üzere vardır.</span><span class="sxs-lookup"><span data-stu-id="b1686-146">When your app lets people change a record, you have to present the record to the user for editing anyway.</span></span> <span data-ttu-id="b1686-147">Ancak, bir kaydın silinmesi için bu adımı atlamak için gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="b1686-147">But you might be tempted to skip this step for deleting a record.</span></span> <span data-ttu-id="b1686-148">Bu adım, ancak atlamayın.</span><span class="sxs-lookup"><span data-stu-id="b1686-148">Don't skip that step, though.</span></span> <span data-ttu-id="b1686-149">(Bu da kaydını görmek ve bunlar kayıt silmekte olduğunuz onaylamak kullanıcılar için yararlı olur.)</span><span class="sxs-lookup"><span data-stu-id="b1686-149">(It's also helpful for users to see the record and confirm that they're deleting the record that they intended.)</span></span>
> 
> <span data-ttu-id="b1686-150">Bir sonraki öğretici kümesindeki bir kullanıcının bir kaydı silmeden önce oturum açması için oturum açma işlevselliği ekleme görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="b1686-150">In a subsequent tutorial set, you'll see how to add login functionality so a user would have to log in before deleting a record.</span></span>


<span data-ttu-id="b1686-151">Adlı bir sayfa oluşturma *DeleteMovie.cshtml* ve dosyasında, aşağıdaki biçimlendirme nedir değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b1686-151">Create a page named *DeleteMovie.cshtml* and replace what's in the file with the following markup:</span></span>

[!code-cshtml[Main](deleting-data/samples/sample4.cshtml)]

<span data-ttu-id="b1686-152">Bu biçimlendirme benzer *EditMovie* , metin kutuları yerine dışındaki sayfalara (`<input type="text">`), biçimlendirme içeren `<span>` öğeleri.</span><span class="sxs-lookup"><span data-stu-id="b1686-152">This markup is like the *EditMovie* pages, except that instead of using text boxes (`<input type="text">`), the markup includes `<span>` elements.</span></span> <span data-ttu-id="b1686-153">Düzenlemek için burada şey yoktur.</span><span class="sxs-lookup"><span data-stu-id="b1686-153">There's nothing here to edit.</span></span> <span data-ttu-id="b1686-154">Yapmanız gereken tek şey böylece kullanıcılar sağ film silmekte olduğunuz emin olabilirsiniz film ayrıntıları görüntüle.</span><span class="sxs-lookup"><span data-stu-id="b1686-154">All you have to do is display the movie details so that users can make sure that they're deleting the right movie.</span></span>

<span data-ttu-id="b1686-155">İşaretleme film listeleme sayfaya dönmek kullanıcı olanak sağlayan bir bağlantı zaten var.</span><span class="sxs-lookup"><span data-stu-id="b1686-155">The markup already contains a link that lets the user return to the movie listing page.</span></span>

<span data-ttu-id="b1686-156">Olarak *EditMovie* sayfası, seçilen film Kimliğini, gizli bir alan depolanır.</span><span class="sxs-lookup"><span data-stu-id="b1686-156">As in the *EditMovie* page, the ID of the selected movie is stored in a hidden field.</span></span> <span data-ttu-id="b1686-157">(Bu sayfaya ilk başta bir sorgu dizesi değeri olarak geçirilir.) Var olan bir `Html.ValidationSummary` doğrulama hataları görüntüler çağrısı.</span><span class="sxs-lookup"><span data-stu-id="b1686-157">(It's passed into the page in the first place as a query string value.) There's an `Html.ValidationSummary` call that will display validation errors.</span></span> <span data-ttu-id="b1686-158">Bu durumda, hata film kimliği yok sayfasına geçildi veya film kimliği geçersiz olabilir.</span><span class="sxs-lookup"><span data-stu-id="b1686-158">In this case, the error might be that no movie ID was passed to the page or that the movie ID is invalid.</span></span> <span data-ttu-id="b1686-159">Birisi bu sayfayı bir filmi seçmeden çalıştıracaksanız, bu durum oluşabilir *filmler* sayfası.</span><span class="sxs-lookup"><span data-stu-id="b1686-159">This situation could occur if someone ran this page without first selecting a movie in the *Movies* page.</span></span>

<span data-ttu-id="b1686-160">Düğme resim yazısı **silmek film**, ve kendi ad özniteliği kümesine `buttonDelete`.</span><span class="sxs-lookup"><span data-stu-id="b1686-160">The button caption is **Delete Movie**, and its name attribute is set to `buttonDelete`.</span></span> <span data-ttu-id="b1686-161">`name` Özniteliği formun gönderilen düğmeyi saptamak için kodda kullanılacak.</span><span class="sxs-lookup"><span data-stu-id="b1686-161">The `name` attribute will be used in the code to identify the button that submitted the form.</span></span>

<span data-ttu-id="b1686-162">1) film Ayrıntıları sayfası görüntülendiğinde okumak için kod yazmak ve kullanıcı düğmesini tıklattığında film 2) gerçekten silmek gerekir.</span><span class="sxs-lookup"><span data-stu-id="b1686-162">You'll have to write code to 1) read the movie details when the page is first displayed and 2) actually delete the movie when the user clicks the button.</span></span>

## <a name="adding-code-to-read-a-single-movie"></a><span data-ttu-id="b1686-163">Tek bir filmi okumak için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="b1686-163">Adding Code to Read a Single Movie</span></span>

<span data-ttu-id="b1686-164">Üstündeki *DeleteMovie.cshtml* sayfasında, aşağıdaki kod bloğunu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1686-164">At the top of the *DeleteMovie.cshtml* page, add the following code block:</span></span>

[!code-cshtml[Main](deleting-data/samples/sample5.cshtml)]

<span data-ttu-id="b1686-165">Bu biçimlendirme karşılık gelen kodu aynıdır *EditMovie* sayfası.</span><span class="sxs-lookup"><span data-stu-id="b1686-165">This markup is the same as the corresponding code in the *EditMovie* page.</span></span> <span data-ttu-id="b1686-166">Sorgu dizesi dışında film Kimliğini alır ve bir kayıt veritabanından okumak için Kimliğini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b1686-166">It gets the movie ID out of the query string and uses the ID to read a record from the database.</span></span> <span data-ttu-id="b1686-167">Doğrulama testi kodu içerir (`IsInt()` ve `row != null`) sayfasına geçirilen film Kimliğinin geçerli olduğundan emin olmak için.</span><span class="sxs-lookup"><span data-stu-id="b1686-167">The code includes the validation test (`IsInt()` and `row != null`) to make sure that the movie ID being passed to the page is valid.</span></span>

<span data-ttu-id="b1686-168">Bu kod, sayfa ilk çalıştığında yalnızca çalışması gerektiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="b1686-168">Remember that this code should only run the first time the page runs.</span></span> <span data-ttu-id="b1686-169">Kullanıcı tıklattığında film kaydı veritabanından yeniden okumak istemediğiniz **silmek film** düğmesi.</span><span class="sxs-lookup"><span data-stu-id="b1686-169">You don't want to re-read the movie record from the database when the user clicks the **Delete Movie** button.</span></span> <span data-ttu-id="b1686-170">Bu nedenle, okuma film içinde belirten bir test kodu `if(!IsPost)` &mdash; diğer bir deyişle, *isteği post işlemine (form gönderme) değilse,*.</span><span class="sxs-lookup"><span data-stu-id="b1686-170">Therefore, code to read the movie is inside a test that says `if(!IsPost)` &mdash; that is, *if the request is not a post operation (form submission)*.</span></span>

## <a name="adding-code-to-delete-the-selected-movie"></a><span data-ttu-id="b1686-171">Seçili film silmek için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="b1686-171">Adding Code to Delete the Selected Movie</span></span>

<span data-ttu-id="b1686-172">Kullanıcı düğmesini tıklattığında film silmek için aşağıdaki kodu kapanış ayracı yalnızca eklemek `@` engelle:</span><span class="sxs-lookup"><span data-stu-id="b1686-172">To delete the movie when the user clicks the button, add the following code just inside the closing brace of the `@` block:</span></span>

[!code-csharp[Main](deleting-data/samples/sample6.cs)]

<span data-ttu-id="b1686-173">Bu, varolan bir kaydı güncelleştirmek için kod benzer, ancak daha basit kodudur.</span><span class="sxs-lookup"><span data-stu-id="b1686-173">This code is similar to the code for updating an existing record, but simpler.</span></span> <span data-ttu-id="b1686-174">Kod temelde bir SQL çalışır `Delete` deyimi.</span><span class="sxs-lookup"><span data-stu-id="b1686-174">The code basically runs a SQL `Delete` statement.</span></span>

 <span data-ttu-id="b1686-175">Olarak *EditMovie* kod sayfası, konusu bir `if(IsPost)` bloğu.</span><span class="sxs-lookup"><span data-stu-id="b1686-175">As in the *EditMovie* page, the code is in an `if(IsPost)` block.</span></span> <span data-ttu-id="b1686-176">Bu süre, `if()` durumdur biraz daha karmaşık:</span><span class="sxs-lookup"><span data-stu-id="b1686-176">This time, the `if()` condition is a little more complicated:</span></span> 

[!code-csharp[Main](deleting-data/samples/sample7.cs)]

<span data-ttu-id="b1686-177">Burada iki koşul vardır.</span><span class="sxs-lookup"><span data-stu-id="b1686-177">There are two conditions here.</span></span> <span data-ttu-id="b1686-178">Sayfa gönderiliyor olduğunu, ilk önce gördüğünüz gibi olan &mdash; `if(IsPost)`.</span><span class="sxs-lookup"><span data-stu-id="b1686-178">The first is that the page is being submitted, as you've seen before &mdash; `if(IsPost)`.</span></span>

<span data-ttu-id="b1686-179">İkinci koşulu `!Request["buttonDelete"].IsEmpty()`, istek adlı bir nesne olduğu anlamına gelen `buttonDelete`.</span><span class="sxs-lookup"><span data-stu-id="b1686-179">The second condition is `!Request["buttonDelete"].IsEmpty()`, meaning that the request has an object named `buttonDelete`.</span></span> <span data-ttu-id="b1686-180">Kuşkusuz bu formun hangi düğmesi gönderilen sınama bir dolaylı yoludur.</span><span class="sxs-lookup"><span data-stu-id="b1686-180">Admittedly, it's an indirect way of testing which button submitted the form.</span></span> <span data-ttu-id="b1686-181">Bir form birden çok düğmeleri içeriyorsa, istekte yalnızca tıklandığını düğmenin adı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b1686-181">If a form contains multiple submit buttons, only the name of the button that was clicked appears in the request.</span></span> <span data-ttu-id="b1686-182">Bu nedenle, mantıksal olarak, belirli bir düğmeyi adını istekte görünüp görünmeyeceğini &mdash; veya bu düğme boş değilse kodda belirtildiği gibi &mdash; formun gönderilen düğmesi olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="b1686-182">Therefore, logically, if the name of a particular button appears in the request &mdash; or as stated in the code, if that button isn't empty &mdash; that's the button that submitted the form.</span></span>

<span data-ttu-id="b1686-183">`&&` İşleci anlamına gelir "ve" (mantıksal AND).</span><span class="sxs-lookup"><span data-stu-id="b1686-183">The `&&` operator means "and" (logical AND).</span></span> <span data-ttu-id="b1686-184">Bu nedenle tüm `if` koşul...</span><span class="sxs-lookup"><span data-stu-id="b1686-184">Therefore the entire `if` condition is ...</span></span>

<span data-ttu-id="b1686-185">*Bu istek bir (ilk kez isteği değil) postasıdır*</span><span class="sxs-lookup"><span data-stu-id="b1686-185">*This request is a post (not a first-time request)*</span></span>  
  
 <span data-ttu-id="b1686-186">AND</span><span class="sxs-lookup"><span data-stu-id="b1686-186">AND</span></span>  
  
<span data-ttu-id="b1686-187">** `buttonDelete` *Düğmesi formun gönderilen düğmesi oluştu.*</span><span class="sxs-lookup"><span data-stu-id="b1686-187">*The* `buttonDelete`*button was the button that submitted the form.*</span></span>

<span data-ttu-id="b1686-188">Bu form (aslında, bu sayfayı) yalnızca bir düğme, bu nedenle içerir için ek sınama `buttonDelete` teknik gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b1686-188">This form (in fact, this page) contains only one button, so the additional test for `buttonDelete` is technically not required.</span></span> <span data-ttu-id="b1686-189">Yine de, verileri kalıcı olarak kaldırır bir işlem gerçekleştirmek üzere olduğunuz.</span><span class="sxs-lookup"><span data-stu-id="b1686-189">Still, you're about to perform an operation that will permanently remove data.</span></span> <span data-ttu-id="b1686-190">Böylece olabildiğince yalnızca kullanıcının açıkça, istediği zaman işlemi yaptığınızdan emin olmak istiyor.</span><span class="sxs-lookup"><span data-stu-id="b1686-190">So you want to be as sure as possible that you're performing the operation only when the user has explicitly requested it.</span></span> <span data-ttu-id="b1686-191">Örneğin, daha sonra bu sayfayı genişletilmiş ve diğer düğmeleri kendisine eklenmiş olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="b1686-191">For example, suppose that you expanded this page later and added other buttons to it.</span></span> <span data-ttu-id="b1686-192">Yalnızca film siler kodu dahi sonra çalıştıracak `buttonDelete` düğmesine tıklanana.</span><span class="sxs-lookup"><span data-stu-id="b1686-192">Even then, the code that deletes the movie will run only if the `buttonDelete` button was clicked.</span></span>

<span data-ttu-id="b1686-193">Olarak *EditMovie* sayfasında kimliği gizli alanından almanıza ve ardından SQL komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b1686-193">As in the *EditMovie* page, you get the ID from the hidden field and then run the SQL command.</span></span> <span data-ttu-id="b1686-194">Sözdizimi `Delete` ifadesi:</span><span class="sxs-lookup"><span data-stu-id="b1686-194">The syntax for the `Delete` statement is:</span></span>

`DELETE FROM table WHERE ID = value`

<span data-ttu-id="b1686-195">Eklenecek önemlidir `WHERE` yan tümcesi ve kimliği.</span><span class="sxs-lookup"><span data-stu-id="b1686-195">It's vital to include the `WHERE` clause and the ID.</span></span> <span data-ttu-id="b1686-196">WHERE yan tümcesi bırakırsanız *tablosundaki tüm kayıtları silinecek*.</span><span class="sxs-lookup"><span data-stu-id="b1686-196">If you leave out the WHERE clause, *all the records in the table will be deleted*.</span></span> <span data-ttu-id="b1686-197">Görülen, kimlik değeri SQL komutu için bir yer tutucu kullanarak geçirin.</span><span class="sxs-lookup"><span data-stu-id="b1686-197">As you have seen, you pass the ID value to the SQL command by using a placeholder.</span></span>

## <a name="testing-the-movie-delete-process"></a><span data-ttu-id="b1686-198">Film silme işlemi test etme</span><span class="sxs-lookup"><span data-stu-id="b1686-198">Testing the Movie Delete Process</span></span>

<span data-ttu-id="b1686-199">Artık test edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b1686-199">Now you can test.</span></span> <span data-ttu-id="b1686-200">Çalıştırma *filmler* sayfasında ve tıklayın **silmek** film yanındaki.</span><span class="sxs-lookup"><span data-stu-id="b1686-200">Run the *Movies* page, and click **Delete** next to a movie.</span></span> <span data-ttu-id="b1686-201">Zaman *DeleteMovie* sayfası görüntülendikten sonra **silmek film**.</span><span class="sxs-lookup"><span data-stu-id="b1686-201">When the *DeleteMovie* page appears, click **Delete Movie**.</span></span>

![Silme film düğmesi vurgulanmış filmi sayfayı silin](deleting-data/_static/image4.png)

<span data-ttu-id="b1686-203">Düğmeye tıkladığınızda, kod filmler siler ve film listesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1686-203">When you click the button, the code deletes the movies and returns to the movie listing.</span></span> <span data-ttu-id="b1686-204">Silinen film için arama vardır ve silinmiş onaylayın.</span><span class="sxs-lookup"><span data-stu-id="b1686-204">There you can search for the deleted movie and confirm that it's been deleted.</span></span>

## <a name="coming-up-next"></a><span data-ttu-id="b1686-205">Sıradaki gelen</span><span class="sxs-lookup"><span data-stu-id="b1686-205">Coming Up Next</span></span>

<span data-ttu-id="b1686-206">Sonraki öğretici ortak bir görünüm ve düzeni sitenizdeki tüm sayfaları koyduğunuzdan gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b1686-206">The next tutorial shows you how to give all the pages on your site a common look and layout.</span></span>

## <a name="complete-listing-for-movie-page-updated-with-delete-links"></a><span data-ttu-id="b1686-207">Tam listesi için (Delete bağlantılarıyla güncelleştirilmiş) film sayfası</span><span class="sxs-lookup"><span data-stu-id="b1686-207">Complete Listing for Movie Page (Updated with Delete Links)</span></span>

[!code-cshtml[Main](deleting-data/samples/sample8.cshtml)]

## <a name="complete-listing-for-deletemovie-page"></a><span data-ttu-id="b1686-208">Tam listesi için DeleteMovie sayfası</span><span class="sxs-lookup"><span data-stu-id="b1686-208">Complete Listing for DeleteMovie Page</span></span>

[!code-cshtml[Main](deleting-data/samples/sample9.cshtml)]

## <a name="additional-resources"></a><span data-ttu-id="b1686-209">Ek Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b1686-209">Additional Resources</span></span>

- [<span data-ttu-id="b1686-210">Razor sözdizimini kullanarak ASP.NET Web programlamaya giriş</span><span class="sxs-lookup"><span data-stu-id="b1686-210">Introduction to ASP.NET Web Programming by Using the Razor Syntax</span></span>](https://go.microsoft.com/fwlink/?LinkID=202890)
- <span data-ttu-id="b1686-211">[SQL DELETE deyimi](http://www.w3schools.com/sql/sql_delete.asp) W3Schools sitesinde</span><span class="sxs-lookup"><span data-stu-id="b1686-211">[SQL DELETE Statement](http://www.w3schools.com/sql/sql_delete.asp) on the W3Schools site</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="b1686-212">[Önceki](updating-data.md)
[sonraki](layouts.md)</span><span class="sxs-lookup"><span data-stu-id="b1686-212">[Previous](updating-data.md)
[Next](layouts.md)</span></span>
