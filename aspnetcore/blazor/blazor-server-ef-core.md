---
title: Blazor ServerEntity Framework Core ile ASP.NET Core (EFCore)
author: JeremyLikness
description: Uygulamalarda EF Core kullanma kılavuzu Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: e548465b3d79279802fbfacd66c69724d864d14d
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080335"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>Blazor ServerEntity Framework Core ile ASP.NET Core (EFCore)

Şu kadar: [Jeremy Liksizlik](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server durum bilgisi olan bir uygulama çerçevesidir. Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur. Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri. Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir.

> [!NOTE]
> Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server . Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır. İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.

<h2 id="sample-app-5x">Örnek uygulama</h2>

Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server . Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir. Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır. Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır. Bu, içinde yapılandırılır `appsettings.Development.json` :

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır. Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.

> [!NOTE]
> Bu konudaki bazı kod örnekleri, gösterilmeyen ad alanlarını ve Hizmetleri gerektirir. Gerekli ve örnek yönergeleri dahil olmak üzere tam çalışma kodunu incelemek için [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).

<h2 id="database-access-5x">Veritabanı erişimi</h2>

EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [*iş birimi*](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır. EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak *kapsamlı* bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar. Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından kapsamlı hizmet kayıtları sorunlu olabilir. <xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır. Mevcut yaşam süreleri şu nedenlerle uygun değildir:

* **Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.
* **Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.
* İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.

Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server .

* Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün. Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  `Loading = true;`Blok içindeki satırdan sonra işlemleri yerleştir `try` .

* EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime-5x).

<h3 id="new-dbcontext-instances-5x">Yeni DbContext örnekleri</h3>

Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır. Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır. Örneğin, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz.

Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır. EF Core 5,0 veya üzeri, yeni bağlamlar oluşturmaya yönelik yerleşik bir fabrika sağlar.

Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir. Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır. Örneğin, içinde `Pages/Index.razor` :

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` bileşene bir [bileşen başvurusu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` . `Index` `Pages/Index.razor` [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)bileşene () bakın.

<h3 id="scope-to-the-component-lifetime-5x">Bileşen ömrü için kapsam</h3>

<xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz. Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.
Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz. İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Örnek uygulama, bileşen bırakıldığında bağlamın elden çıkarılmasını sağlar:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Son olarak, [`OnInitializedAsync`](xref:blazor/components/lifecycle) Yeni bağlam oluşturmak için geçersiz kılınır. Örnek uygulamada, [`OnInitializedAsync`](xref:blazor/components/lifecycle) kişiyi aynı yönteme yükler:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server durum bilgisi olan bir uygulama çerçevesidir. Uygulama, sunucuyla devam eden bir bağlantı sağlar ve kullanıcının durumu bir *devrendeki*sunucunun belleğinde tutulur. Kullanıcı durumunun bir örneği, devre kapsamına alınan [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmet örneklerinde tutulan veri. Tarafından sağlanan benzersiz uygulama modeli Blazor Server Entity Framework Core kullanmak için özel bir yaklaşım gerektirir.

> [!NOTE]
> Bu makalede, uygulamalarda EF Core ele alınmaktadır Blazor Server . Blazor WebAssembly uygulamalar, çoğu doğrudan veritabanı bağlantısının önlediği bir WebAssembly korumalı alanında çalışır. İçinde EF Core çalıştırmak, Blazor WebAssembly Bu makalenin kapsamı dışındadır.

<h2 id="sample-app-3x">Örnek uygulama</h2>

Örnek uygulama, EF Core kullanan uygulamalar için bir başvuru olarak oluşturulmuştur Blazor Server . Örnek uygulama sıralama ve filtreleme, silme, ekleme ve güncelleştirme işlemlerini içeren bir kılavuz içerir. Örnek, iyimser eşzamanlılık işlemek için EF Core kullanımını gösterir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek, herhangi bir platformda kullanılabilmesi için yerel bir [SQLite](https://www.sqlite.org/index.html) veritabanı kullanır. Örnek ayrıca, oluşturulan SQL sorgularını göstermek için veritabanı günlüğünü de yapılandırır. Bu, içinde yapılandırılır `appsettings.Development.json` :

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Izgara, ekleme ve görüntüleme bileşenleri, her işlem için bir bağlamın oluşturulduğu "işlem başına bağlam" modelini kullanır. Düzenleme bileşeni, her bileşen için bir bağlamın oluşturulduğu "bileşen başına bağlam" modelini kullanır.

> [!NOTE]
> Bu konudaki bazı kod örnekleri, gösterilmeyen ad alanlarını ve Hizmetleri gerektirir. Gerekli ve örnek yönergeleri dahil olmak üzere tam çalışma kodunu incelemek için [`@using`](xref:mvc/views/razor#using) [`@inject`](xref:mvc/views/razor#inject) Razor bkz. [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).

<h2 id="database-access-3x">Veritabanı erişimi</h2>

EF Core <xref:Microsoft.EntityFrameworkCore.DbContext> , [veritabanı erişimini yapılandırma](/ef/core/miscellaneous/configuring-dbcontext) ve bir [*iş birimi*](https://martinfowler.com/eaaCatalog/unitOfWork.html)görevi gören bir yöntem olarak bir kullanır. EF Core <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> , bağlamı varsayılan olarak *kapsamlı* bir hizmet olarak kaydeden ASP.NET Core uygulamalar için uzantı sağlar. Blazor ServerUygulamalarda, örnek Kullanıcı devresi içindeki bileşenler arasında paylaşıldığından bu sorun sorunlu olabilir. <xref:Microsoft.EntityFrameworkCore.DbContext> iş parçacığı güvenli değildir ve eşzamanlı kullanım için tasarlanmamıştır. Mevcut yaşam süreleri şu nedenlerle uygun değildir:

* **Tek tek** , uygulamanın tüm kullanıcıları genelinde durumu paylaşır ve uygunsuz eşzamanlı kullanım sağlar.
* **Kapsamlı** (varsayılan), aynı kullanıcının bileşenleri arasında benzer bir sorun oluşturur.
* İstek başına yeni bir örneğe **geçici** sonuçlar; Ancak, bileşenler uzun süreli olabileceğinden, bu, amacına kıyasla daha uzun ömürlü bir bağlam elde edebilir.

Aşağıdaki öneriler, uygulamalarda EF Core kullanmak için tutarlı bir yaklaşım sağlamak üzere tasarlanmıştır Blazor Server .

* Varsayılan olarak, işlem başına bir bağlam kullanmayı düşünün. Bağlam hızlı, düşük yük örneği oluşturma için tasarlanmıştır:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Birden çok eş zamanlı işlemi engellemek için bayrak kullanın:

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  `Loading = true;`Blok içindeki satırdan sonra işlemleri yerleştir `try` .

* EF Core [değişiklik izleme](/ef/core/querying/tracking) veya [eşzamanlılık denetiminden](/ef/core/saving/concurrency)faydalanan uzun süreli işlemler için, [bağlamı bileşenin kullanım ömrüne göre kapsam](#scope-to-the-component-lifetime-3x).

<h3 id="new-dbcontext-instances-3x">Yeni DbContext örnekleri</h3>

Yeni bir örnek oluşturmanın en hızlı yolu <xref:Microsoft.EntityFrameworkCore.DbContext> , `new` Yeni bir örnek oluşturmak için kullanmaktır. Ancak, ek bağımlılıkların çözümlenme gerektirebilecek birkaç senaryo vardır. Örneğin, [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) bağlamını yapılandırmak için kullanmak isteyebilirsiniz.

Bağımlılıklar ile yeni bir oluşturma için önerilen çözüm, <xref:Microsoft.EntityFrameworkCore.DbContext> bir fabrika kullanmaktır. Örnek uygulama, içinde kendi fabrikasını uygular `Data/DbContextFactory.cs` .

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

Önceki fabrikada, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> hizmet sağlayıcısı aracılığıyla tüm bağımlılıkları karşılar.

Aşağıdaki örnekte, [SQLite](https://www.sqlite.org/index.html) yapılandırılır ve veri günlüğü etkinleştirilir. Kod, dı için veritabanı fabrikası yapılandırmak ve varsayılan seçenekleri sağlamak için bir genişletme yöntemi kullanır:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Fabrika bileşenlere eklenir ve yeni örnekler oluşturmak için kullanılır. Örneğin, içinde `Pages/Index.razor` :

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` bileşene bir [bileşen başvurusu](xref:blazor/components/index#capture-references-to-components) `GridWrapper` . `Index` `Pages/Index.razor` [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor)bileşene () bakın.

<h3 id="scope-to-the-component-lifetime-3x">Bileşen ömrü için kapsam</h3>

<xref:Microsoft.EntityFrameworkCore.DbContext>Bir bileşenin ömrü için var olan bir oluşturmak isteyebilirsiniz. Bu, bunu [çalışma birimi](https://martinfowler.com/eaaCatalog/unitOfWork.html) olarak kullanmanıza ve değişiklik izleme ve eşzamanlılık çözümleme gibi yerleşik özelliklerden yararlanmanıza olanak sağlar.
Fabrika 'yi kullanarak bir bağlam oluşturabilir ve bileşenin kullanım ömrü boyunca izleyebilirsiniz. İlk olarak, <xref:System.IDisposable> fabrika 'yi aşağıda gösterildiği gibi uygulayın ve ekleme `Pages/EditContact.razor` :

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Örnek uygulama, bileşen bırakıldığında bağlamın elden çıkarılmasını sağlar:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Son olarak, [`OnInitializedAsync`](xref:blazor/components/lifecycle) Yeni bağlam oluşturmak için geçersiz kılınır. Örnek uygulamada, [`OnInitializedAsync`](xref:blazor/components/lifecycle) kişiyi aynı yönteme yükler:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

Yukarıdaki örnekte:

* , `Busy` Olarak ayarlandığında `true` , zaman uyumsuz işlemler başlayabilir. , `Busy` ' A geri ayarlandığında `false` , zaman uyumsuz işlemlerin bitmesi gerekir.
* Bir blokta ek hata işleme mantığı yerleştirin `catch` .

:::moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [EF Core belgeleri](/ef/)
