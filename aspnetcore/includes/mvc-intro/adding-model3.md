
## <a name="test-the-app"></a><span data-ttu-id="25e91-101">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="25e91-101">Test the app</span></span>

* <span data-ttu-id="25e91-102">Uygulamayı çalıştırın ve dokunun **Mvc film** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="25e91-102">Run the app and tap the **Mvc Movie** link.</span></span>
* <span data-ttu-id="25e91-103">Dokunun **Yeni Oluştur** bağlayın ve bir filmi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="25e91-103">Tap the **Create New** link and create a movie.</span></span>

  ![Türe, fiyat, yayın tarihi ve başlık alanlarla görünümü oluşturma](~/tutorials/first-mvc-app/adding-model/_static/movies.png)

* <span data-ttu-id="25e91-105">Ondalık nokta veya virgül, girmeniz mümkün olmayabilir `Price` alan.</span><span class="sxs-lookup"><span data-stu-id="25e91-105">You may not be able to enter decimal points or commas in the `Price` field.</span></span> <span data-ttu-id="25e91-106">Desteklemek için [jQuery doğrulama](https://jqueryvalidation.org/) virgül İngilizce olmayan yerel ayarlar için (",") ondalık ve ABD İngilizce olmayan tarih biçimleri için uygulamanızı globalleştirmek için adımları izlemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="25e91-106">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="25e91-107">Bkz: [ https://github.com/aspnet/AspNetCore.Docs/issues/4076 ](https://github.com/aspnet/AspNetCore.Docs/issues/4076) ve [ek kaynaklar](#additional-resources) daha fazla bilgi için.</span><span class="sxs-lookup"><span data-stu-id="25e91-107">See [https://github.com/aspnet/AspNetCore.Docs/issues/4076](https://github.com/aspnet/AspNetCore.Docs/issues/4076) and [Additional resources](#additional-resources) for more information.</span></span> <span data-ttu-id="25e91-108">Şimdilik yalnızca 10 gibi tam sayı girin.</span><span class="sxs-lookup"><span data-stu-id="25e91-108">For now, just enter whole numbers like 10.</span></span>

<a name="displayformatdatelocal"></a>

* <span data-ttu-id="25e91-109">Bazı yerel ayarlarda tarih biçimini belirtmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="25e91-109">In some locales you need to specify the date format.</span></span> <span data-ttu-id="25e91-110">Aşağıdaki vurgulanmış kodu bakın.</span><span class="sxs-lookup"><span data-stu-id="25e91-110">See the highlighted code below.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]

<span data-ttu-id="25e91-111">Hakkında konuşacağız `DataAnnotations` öğreticide daha ilerideki.</span><span class="sxs-lookup"><span data-stu-id="25e91-111">We'll talk about `DataAnnotations` later in the tutorial.</span></span>

<span data-ttu-id="25e91-112">Dokunarak **Oluştur** film bilgiler bir veritabanında kaydedildiği sunucuya yayımlanacak formun neden olur.</span><span class="sxs-lookup"><span data-stu-id="25e91-112">Tapping **Create** causes the form to be posted to the server, where the movie information is saved in a database.</span></span> <span data-ttu-id="25e91-113">Uygulamayı yeniden */Movies* yeni oluşturulan film bilgileri görüntüleyen bir URL.</span><span class="sxs-lookup"><span data-stu-id="25e91-113">The app redirects to the */Movies* URL, where the newly created movie information is displayed.</span></span>

![Filmler gösteren yeni oluşturulan film listeyi görüntüle](~/tutorials/first-mvc-app/adding-model/_static/h.png)

<span data-ttu-id="25e91-115">Birkaç daha fazla film girişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="25e91-115">Create a couple more movie entries.</span></span> <span data-ttu-id="25e91-116">Deneyin **Düzenle**, **ayrıntıları**, ve **Sil** tüm işlevsel bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="25e91-116">Try the **Edit**, **Details**, and **Delete** links, which are all functional.</span></span>
