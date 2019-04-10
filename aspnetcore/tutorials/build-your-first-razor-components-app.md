---
title: Razor bileşenleri ilk uygulamanızı oluşturun
author: guardrex
description: Adım adım bir Razor bileşenleri uygulaması derleme ve Razor bileşenleri temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 697c4659bcc9952ffe9868fe9b3c0d28019bc369
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468782"
---
# <a name="build-your-first-razor-components-app"></a>Razor bileşenleri ilk uygulamanızı oluşturun

Tarafından [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Bu öğretici, Razor bileşenleri ile uygulama oluşturma işlemini göstermektedir ve Razor bileşenleri temel kavramlarını gösterir. Bu öğretici (.NET Core 3.0 veya sonraki sürümlerde desteklenir) ya da bir Razor bileşenleri tabanlı proje veya (.NET Core gelecekteki bir sürümde desteklenir) Blazor tabanlı bir proje kullanarak keyfini çıkarabilirsiniz.

ASP.NET Core Razor bileşenlerini kullanarak bir deneyim için (*önerilen*):

* Sunulan yönergeleri <xref:razor-components/get-started> Razor bileşenleri tabanlı bir proje oluşturmaktır.
* Projeyi adlandırın `RazorComponents`.

Blazor kullanarak bir deneyim için:

* Sunulan yönergeleri <xref:spa/blazor/get-started> Blazor tabanlı bir proje oluşturmaktır.
* Projeyi adlandırın `Blazor`.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([nasıl indirileceğini](xref:index#how-to-download-a-sample)). Önkoşullar için aşağıdaki konulara bakın:

## <a name="build-components"></a>Yapı bileşenleri

1. Her uygulamanın üç sayfalarında Gözat *bileşenleri/sayfaları* klasörü (*sayfaları* Blazor içinde): Giriş, sayaç ve veri getirilemiyor. Bu sayfalar, Razor bileşen dosyaları tarafından uygulanır: *Index.Razor*, *Counter.razor*, ve *FetchData.razor*. (Blazor devam kullanılacak *.cshtml* dosya uzantısı: *Index.cshtml*, *Counter.cshtml*, ve *FetchData.cshtml*.)

1. Sayaç sayfasında **me tıklayın** sayfa yenileme olmadan sayaç artmaya düğmesi. Normal olarak artan bir Web sayfasındaki bir sayaç JavaScript Yazma gerektiriyor, ancak Razor bileşenleri sağlayan daha iyi bir yaklaşım kullanarak C#.

1. Uygulama sayacı bileşenin inceleyin *Counter.razor* dosya.

   *Components/Pages/Counter.razor* (*Pages/Counter.cshtml* Blazor içinde):

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.razor)]

   Kullanıcı arabirimini sayacı bileşenin HTML kullanılarak tanımlanır. (Örneğin, döngü, koşullular, ifadeleri) dinamik işleme mantığı, katıştırılmış kullanarak eklenir C# adlı söz dizimi [Razor](xref:mvc/views/razor). HTML biçimlendirmesi ve C# işleme mantığı, oluşturma zamanında bir bileşen sınıfı dönüştürülür. Oluşturulan .NET sınıf adı dosya adıyla eşleşen.

   Bileşen sınıfı üyeleri tanımlanmış bir `@functions` blok. İçinde `@functions` blok, bileşen durumu (Özellikler, alanlar) ve olay işleme için veya başka bir bileşen mantığı tanımlamak için yöntemleri belirtilir. Bu üyeleri, ardından bileşenin işleme mantığı ve olayları işlemek için kullanılır.

   Zaman **me tıklayın** düğmesi seçili:

   * Sayaç bileşen kayıtlı `onclick` işleyici çağrılır ( `IncrementCount` yöntemi).
   * Sayaç bileşen kendi işleme ağacında yeniden oluşturur.
   * Yeni bir işleme ağacı Öncekine karşılaştırılır.
   * Yalnızca belge nesne modeli (DOM) değişiklikler uygulanır. Görüntülenen sayısı güncelleştirilir.

1. Değiştirme C# bir yerine ikiye sayısı artması yapmak için sayaç bileşeninin mantığı.

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Yeniden oluşturun ve değişiklikleri görmek için uygulamayı çalıştırın. Seçin **me tıklayın** düğmeyi ve iki sayacını artırır.

## <a name="use-components"></a>Bileşenleri kullanma

Bir HTML benzeri sözdizimi kullanarak başka bir bileşene dönüştürerek bileşen içerir.

1. Sayaç bileşen uygulamanın dizini (giriş) bileşenine ekleyerek bir `<Counter />` dizin bileşeni öğesi.

   Bu deneyim için bir anket istemi bileşeni Blazor kullanıyorsanız (`<SurveyPrompt>` öğesi) dizin bileşenidir. Değiştirin `<SurveyPrompt>` öğeyle `<Counter>` öğesi.

   *Components/Pages/Index.razor* (*Pages/Index.cshtml* Blazor içinde):

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. Yeniden oluşturun ve uygulamayı çalıştırın. Giriş sayfası, kendi sayaç vardır.

## <a name="component-parameters"></a>Bileşen parametreleri

Bileşenleri parametrelerini de sağlayabilirsiniz. Bileşen parametreleri, genel olmayan özellikler ile donatılmış bileşen sınıfı kullanarak tanımlanan `[Parameter]`. Öznitelikleri bir bileşen için bağımsız değişken biçimlendirme içinde belirtmek için kullanın.

1. Bileşenin güncelleştirme `@functions` C# kod:

   * Ekleme bir `IncrementAmount` özelliği düzenlenmiş ile `[Parameter]` özniteliği.
   * Değişiklik `IncrementCount` yönteminin kullanılacağını `IncrementAmount` değerini artırmayı olduğunda `currentCount`.

   *Components/Pages/Counter.razor* (*Pages/Counter.cshtml* Blazor içinde):

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. Belirtin bir `IncrementAmount` ana bileşenin parametresinde `<Counter>` öğesini kullanarak bir öznitelik. Sayaç tarafından on Artır bir değere ayarlayın.

   *Components/Pages/Index.razor* (*Pages/Index.cshtml* Blazor içinde):

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. Ana sayfayı yeniden yükleyin. Sayaç artırılır on tarafından her zaman **me tıklayın** düğmesi seçili. Bir sayacı sayfa artışlarla üzerinde sayacı.

## <a name="route-to-components"></a>Bileşenleri için yol

`@page` En üstündeki yönerge *Counter.razor* dosya bu bileşen yönlendirme bir uç nokta olduğunu belirtir. Sayaç bileşeni için gönderilen istekleri işler `/Counter`. Olmadan `@page` yönergesi, bileşen yönlendirilen istekler da işlemiyor ancak bileşeni hala başka bileşenler tarafından kullanılabilir.

## <a name="dependency-injection"></a>Bağımlılık ekleme

Bileşenleri uygulamanın service kapsayıcısında kayıtlı hizmetlerinin kullanılabilir [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection). Halinde bileşenini kullanarak Hizmetleri ekleme `@inject` yönergesi.

Örnek uygulamada parçalar bileşeninin yönergeleri inceleyin.

Razor bileşenleri örnek uygulamada `WeatherForecastService` hizmet olarak kayıtlı bir [tekil](xref:fundamentals/dependency-injection#service-lifetimes), Hizmeti'nin bir örneğini uygulama boyunca kullanılabilir olması. `@inject` Yönergesi örneğini ekleme için kullanılan `WeatherForecastService` halinde bileşenini hizmet.

*Components/Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Parçalar bileşeni olarak eklenen hizmet kullanan `ForecastService`, bir dizi alınacak `WeatherForecast` nesneler:

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

Örnek uygulama Blazor sürümünde `HttpClient` hava durumu tahminini verilerden elde etmek için eklenmiş *weather.json* dosyası *wwwroot/örnek-veri* klasörü:

*Pages/FetchData.cshtml*:

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

Her iki örnek uygulamalarda bir [ @foreach ](/dotnet/csharp/language-reference/keywords/foreach-in) döngü, her bir tahmin örneği tablosunda bir satıra hava durumu verilerinin işlemek için kullanılır:

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Bir Yapılacaklar listesi oluşturun

Yeni bir bileşen, bir basit bir Yapılacaklar listesi uygulayan uygulamaya ekleyin.

1. Örnek uygulamaya boş bir dosya ekleyin:

   * Razor bileşenleri deneyimi için ekleme bir *Todo.razor* dosyasını *bileşenleri/sayfaları* klasör.
   * Blazor deneyimi için ekleme bir *Todo.cshtml* dosyasını *sayfaları* klasör.

1. Bileşen için ilk biçimlendirme sağlar:

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. Todo bileşeni Gezinti çubuğuna ekleyin.

   NavMenu bileşeni (*Components/Shared/NavMenu.razor* veya *Shared/NavMenu.cshtml* Blazor içinde) uygulamanın düzende kullanılır. Düzenleri, uygulama içeriği yinelenmesini önlemek izin bileşenlerdir. Daha fazla bilgi için bkz. <xref:razor-components/layouts>.

   Ekleme bir `<NavLink>` aşağıda bulunan mevcut liste öğelerini aşağıdaki liste öğesi işaretleme ekleyerek Todo bileşeni için *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* Blazor içinde) dosyası:

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Yeniden oluşturun ve uygulamayı çalıştırın. Todo bileşen bağlantısını çalıştığını onaylamak için yeni Todo sayfasını ziyaret edin.

1. Ekleme bir *TodoItem.cs* bir todo öğesini temsil eden bir sınıf tutmak için projenin kök dosya. Aşağıdaki C# için kod `TodoItem` sınıfı:

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. Todo bileşenine döndürür (*Components/Pages/Todo.razor* veya *Pages/Todo.cshtml* Blazor içinde):

   * Açıklamada içinde için bir alan eklemek bir `@functions` blok. Todo bileşeni, yapılacaklar listesi durumunu korumak için bu alanı kullanır.
   * Sırasız liste biçimlendirme eklemek ve `foreach` her bir todo öğesi bir liste öğesi olarak işlenecek döngü.

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Uygulama, açıklamada listeye eklemek için kullanıcı Arabirimi öğeleri gerektirir. Bir metin girişi ve listenin altındaki bir düğme ekleyin:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Yeniden oluşturun ve uygulamayı çalıştırın. Zaman **todo ekleyin** düğmesi seçilirse, hiçbir şey olmaz kadar düğmesi olay işleyicisi kablolu değildir çünkü.

1. Ekleme bir `AddTodo` Todo bileşen ve düğme için tıkladığında kullanarak kayıt yöntemi `onclick` özniteliği:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   `AddTodo` C# Yöntemi düğme seçildiğinde çağrılır.

1. Yeni todo öğesinin başlığını almak için ekleyin bir `newTodo` dize alanı ve değeri kullanarak metin olarak bağlama `bind` özniteliği:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. Güncelleştirme `AddTodo` ekleme yöntemi `TodoItem` listesine belirtilen başlığa sahip. Metin girişi değerini ayarlayarak Temizle `newTodo` boş bir dize:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Yeniden oluşturun ve uygulamayı çalıştırın. Bazı açıklamada yeni kodu test etmek için yapılacaklar listesine ekleyin.

1. Her bir todo öğesi için başlık metnini düzenlenebilir hale getirilebilir ve onay kutusu tamamlanan öğeleri takip kullanıcı yardımcı olabilir. Her bir todo öğesi için bir onay kutusu giriş ekleyin ve değerini bağlama `IsDone` özelliği. Değişiklik `@todo.Title` için bir `<input>` bağlı öğe `@todo.Title`:

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Bu değerleri bağlı olduğunu doğrulamak için güncelleştirme `<h1>` tamamlanmamış todo öğelerini sayısını göstermek için başlık (`IsDone` olan `false`).

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. Tamamlanmış Todo bileşeni (*Components/Pages/Todo.razor* veya *Pages/Todo.cshtml* Blazor içinde):

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. Yeniden oluşturun ve uygulamayı çalıştırın. Yeni kod test etmek için todo öğeleri ekleyin.

## <a name="publish-and-deploy-the-app"></a>Yayımlama ve uygulama dağıtma

Uygulamayı yayımlamak için bkz: <xref:host-and-deploy/razor-components-blazor/index>.
