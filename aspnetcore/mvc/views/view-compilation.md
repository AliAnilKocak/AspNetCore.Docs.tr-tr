---
title: "Razor görünüm derleme ve ASP.NET Core içinde ön derlemesi"
author: rick-anderson
description: "MVC Razor görünüm derleme ve ASP.NET Core uygulamalarda ön derlemesi etkinleştirmeyi öğrenin."
manager: wpickett
ms.author: riande
ms.date: 12/13/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/views/view-compilation
ms.openlocfilehash: af1137a087ed145675f38ce5a10fd553044c5582
ms.sourcegitcommit: 493a215355576cfa481773365de021bcf04bb9c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/15/2018
---
# <a name="razor-view-compilation-and-precompilation-in-aspnet-core"></a>Razor görünüm derleme ve ASP.NET Core içinde ön derlemesi

tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

Görünüm çağrıldığında razor görünümleri çalışma zamanında derlenir. ASP.NET Core 1.1.0 ve daha yüksek isteğe bağlı olarak Razor görünümleri derlemek ve uygulamayla dağıtmadan&mdash;ön derlemesi da bilinen bir işlem. ASP.NET Core 2.x proje şablonları ön derlemesi varsayılan olarak etkinleştirir.

> [!IMPORTANT]
> Razor görünüm ön derlemesi şu anda kullanılamıyor gerçekleştirirken bir [müstakil dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) ASP.NET Core 2.0. 2.1 bıraktığında özellik SCDs için kullanılabilir olacak. Daha fazla bilgi için bkz: [görünüm derleme başarısız cross-Windows Linux için derlerken](https://github.com/aspnet/MvcPrecompilation/issues/102).

Ön derleme dikkate alınacak noktalar:

* Görünümleri önceden derleme, bir küçük yayımlanan paket ve daha hızlı başlangıç saati ile sonuçlanır.
* Görünümleri ön derleme yap sonra Razor dosyaları düzenleyemezsiniz. Düzenlenen görünümleri yayımlanan pakete mevcut olmayacaktır. 

Önceden derlenmiş görünümleri dağıtmak için:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Projeniz .NET Framework hedefliyorsa, bir paket başvuru eklemek [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):

```xml
<PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.ViewCompilation" Version="2.0.0" PrivateAssets="All" />
```

Projeniz .NET Core hedefliyorsa, hiçbir değişiklik gereklidir.

ASP.NET Core 2.x proje şablonları örtük olarak ayarlamak `MvcRazorCompileOnPublish` için `true` varsayılan olarak, yani bu düğüm güvenle kaldırılabilir gelen *.csproj* dosya. Açık olmasını isterseniz, ayarı hiçbir zarar yoktur `MvcRazorCompileOnPublish` özelliğine `true`. Aşağıdaki *.csproj* örnek bu ayar vurgular:

[!code-xml[](view-compilation\sample\MvcRazorCompileOnPublish2.csproj?highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Ayarlama `MvcRazorCompileOnPublish` için `true`ve bir paket başvuru eklemek `Microsoft.AspNetCore.Mvc.Razor.ViewCompilation`. Aşağıdaki *.csproj* örnek bu ayarları vurgular:

[!code-xml[](view-compilation\sample\MvcRazorCompileOnPublish.csproj?highlight=5,12)]

---

Uygulama için hazırlama bir [framework bağımlı dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd) proje kökündeki aşağıdaki gibi bir komutu çalıştırarak:

```console
dotnet publish -c Release
```

A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor views, is produced when precompilation succeeds. Örneğin, aşağıdaki ekran görüntüsünde içeriğini gösterir *Index.cshtml* içine *WebApplication1.PrecompiledViews.dll*:

![DLL içindeki Razor görünümleri](view-compilation/_static/razor-views-in-dll.png)
