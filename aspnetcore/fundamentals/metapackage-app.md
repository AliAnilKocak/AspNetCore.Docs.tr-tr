---
title: ASP.NET Core için Microsoft. AspNetCore. app metapackage
author: Rick-Anderson
description: Microsoft. AspNetCore. app Shared Framework
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511385"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>ASP.NET Core için Microsoft. AspNetCore. app

::: moniker range=">= aspnetcore-3.0"

 ASP.NET Core paylaşılan çerçeve (`Microsoft.AspNetCore.App`) Microsoft tarafından geliştirilen ve desteklenen derlemeler içerir. `Microsoft.AspNetCore.App`, [.NET Core 3,0 veya sonraki BIR SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) yüklendiğinde yüklenir. *Paylaşılan çerçeve* , makinede yüklü olan derlemeler ( *. dll* dosyaları) kümesidir ve bir çalışma zamanı bileşeni ve hedefleme paketi içerir. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* `Microsoft.NET.Sdk.Web` SDK 'Yı hedefleyen projeler `Microsoft.AspNetCore.App` çerçevesine örtülü olarak başvurur.

Bu projeler için ek başvuru gerekli değildir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

ASP.NET Core paylaşılan çerçeve:

* Üçüncü taraf bağımlılıklarını içermez.
* ASP.NET Core ekibi tarafından desteklenen tüm paketleri içerir.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu özellik, .NET Core 2. x 'i hedefleyen ASP.NET Core 2. x gerektirir.

ASP.NET Core için [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) :

* [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion. LINQ](https://www.nuget.org/packages/Remotion.Linq/)ve [x zaman uyumsuz](https://www.nuget.org/packages/System.Interactive.Async/)dışında üçüncü taraf bağımlılıklarını içermez. Bu üçüncü taraf bağımlılıklar, ana çerçeveler özelliklerinin çalışmasını sağlamak için gereklidir.
* ASP.NET Core ekibine, üçüncü taraf bağımlılıklar (daha önce bahsedilen) dışında, desteklenen tüm paketleri içerir.
* Entity Framework Core ekibine, üçüncü taraf bağımlılıklar (daha önce bahsedilen) dışında, desteklenen tüm paketleri içerir.

ASP.NET Core 2. x ve Entity Framework Core 2. x ' in tüm özellikleri `Microsoft.AspNetCore.App` paketine dahildir. ASP.NET Core 2. x ' i hedefleyen varsayılan proje şablonları bu paketi kullanın. ASP.NET Core 2. x ve Entity Framework Core 2. x ' i hedefleyen uygulamaların `Microsoft.AspNetCore.App` paketini kullanması önerilir.

`Microsoft.AspNetCore.App` metapackage sürüm numarası, en düşük ASP.NET Core sürümü ve Entity Framework Core sürümünü temsil eder.

`Microsoft.AspNetCore.App` metapackage 'in kullanılması, uygulamanızı koruyan sürüm kısıtlamalarını sağlar:

* `Microsoft.AspNetCore.App`bir pakette geçişli (doğrudan) bağımlılığı olan bir paket varsa ve bu sürüm numaraları farklıysa, NuGet bir hata oluşturur.
* Uygulamanıza eklenen diğer paketler, `Microsoft.AspNetCore.App`yer alan paketlerin sürümünü değiştiremez.
* Sürüm tutarlılığı, güvenilir bir deneyim sağlar. `Microsoft.AspNetCore.App`, ilişkili bitlerin test edilmemiş sürüm birleşimlerinin aynı uygulamada birlikte kullanılmaları önleyecek şekilde tasarlandı.

`Microsoft.AspNetCore.App` metapackage kullanan uygulamalar, ASP.NET Core paylaşılan çerçeveden otomatik olarak faydalanır. `Microsoft.AspNetCore.App` metapackage 'yi kullandığınızda, başvurulan ASP.NET Core NuGet paketlerindeki **hiçbir** varlık uygulama ile dağıtılır&mdash;ASP.NET Core paylaşılan Framework bu varlıkları içerir. Paylaşılan çerçevede bulunan varlıklar, uygulama başlatma süresini artırmak için önceden derlenmiş. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Aşağıdaki proje dosyası, ASP.NET Core için `Microsoft.AspNetCore.App` metapackage öğesine başvurur ve tipik bir ASP.NET Core 2,2 şablonunu temsil eder:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

Yukarıdaki biçimlendirme tipik bir ASP.NET Core 2. x şablonunu temsil eder. `Microsoft.AspNetCore.App` paketi başvurusu için bir sürüm numarası belirtmez. Sürüm belirtilmediğinde, SDK tarafından [örtük](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) bir sürüm belirtilir, diğer bir deyişle `Microsoft.NET.Sdk.Web`. SDK tarafından belirtilen örtük sürüme güvenmek ve paket başvurusunda sürüm numarasını açıkça ayarlamamanız önerilir. Bu yaklaşım hakkında sorularınız varsa, [Microsoft. AspNetCore. app örtük sürümü Için tartışmada](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorumu bırakın.

Örtük sürüm, taşınabilir uygulamalar için `major.minor.0` olarak ayarlanır. Paylaşılan Framework toplaması-iletme mekanizması, uygulamayı yüklü paylaşılan Çerçeveler arasındaki en son uyumlu sürümde çalıştırır. Geliştirme, test ve üretimde aynı sürümün kullanıldığını güvence altına almak için, paylaşılan Framework 'ün aynı sürümünün tüm ortamlarda yüklü olduğundan emin olun. Kendi içindeki uygulamalar için, örtük sürüm numarası yüklü SDK 'da paketlenmiş paylaşılan Framework `major.minor.patch` ayarlanır.

`Microsoft.AspNetCore.App` başvurusunda bir sürüm numarası belirtilmesi, paylaşılan Çerçeve sürümünün seçilmeyeceği garantisi **vermez** . Örneğin, "2.2.1" sürümünün belirtildiğini, ancak "2.2.3" nin yüklü olduğunu varsayalım. Bu durumda, uygulama "2.2.3" kullanacaktır. Önerilmese de, iletmeyi (Patch ve/veya Minor) devre dışı bırakabilirsiniz. DotNet ana bilgisayar alma hakkında daha fazla bilgi ve davranışını yapılandırma hakkında daha fazla bilgi için bkz. [DotNet Host top Forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

örtük sürüm `Microsoft.AspNetCore.App`kullanmak için `<Project Sdk` `Microsoft.NET.Sdk.Web` olarak ayarlanmalıdır. `<Project Sdk="Microsoft.NET.Sdk">` (sondaki `.Web`olmadan) kullanıldığında:

* Aşağıdaki uyarı oluşturulur:

  *Uyarı NU1604: proje bağımlılığı Microsoft. AspNetCore. uygulama, kapsamlı bir alt sınır içermez. Tutarlı geri yükleme sonuçlarının sağlanması için bağımlılık sürümüne bir alt sınır ekleyin.*

* Bu, .NET Core 2,1 SDK ile ilgili bilinen bir sorundur.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Güncelleştirme ASP.NET Core

`Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) , NuGet 'den güncelleştirilmiş geleneksel bir paket değildir. `Microsoft.NETCore.App`benzer şekilde, `Microsoft.AspNetCore.App`, NuGet dışında işlenen özel sürüm oluşturma semantiğinin bulunduğu paylaşılan bir çalışma zamanını temsil eder. Daha fazla bilgi için bkz. [paketler, Metapackages ve çerçeveler](/dotnet/core/packages).

ASP.NET Core güncelleştirmek için:

* Geliştirme makinelerinde ve yapı sunucularında: [.NET Core SDK](https://dotnet.microsoft.com/download)indirin ve yükleyin.
* Dağıtım sunucularında: [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download)indirin ve yükleyin.

 Uygulamalar, uygulama yeniden başlatıldığında en son yüklenen sürüme ileri alınacaktır. Proje dosyasındaki `Microsoft.AspNetCore.App` sürüm numarasını güncelleştirmek gerekli değildir. Daha fazla bilgi için bkz. [çerçeveye bağımlı uygulamalar ileri alma](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Uygulamanız `Microsoft.AspNetCore.All`önceden kullandıysanız [Microsoft. AspNetCore. All 'Dan Microsoft. aspnetcore. App ' e geçiş](xref:fundamentals/metapackage#migrate)konusuna bakın.

::: moniker-end
