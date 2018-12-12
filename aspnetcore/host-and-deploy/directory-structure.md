---
title: ASP.NET Core dizin yapısı
author: guardrex
description: Dizin yapısı, yayımlanmış ASP.NET Core uygulamaları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: host-and-deploy/directory-structure
ms.openlocfilehash: ee0bebb8b5c688f8471d6420d1641b87ac271f6c
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284571"
---
# <a name="aspnet-core-directory-structure"></a>ASP.NET Core dizin yapısı

Tarafından [Luke Latham](https://github.com/guardrex)

*Yayımlama* dizinini içeren uygulamanın dağıtılabilir varlıklar tarafından üretilen [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutu. Dizini içerir:

* Uygulama dosyaları
* Yapılandırma dosyaları
* Statik varlıklar
* Paketler
* Bir çalışma zamanı ([müstakil dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) yalnızca)

| Uygulama türü | Dizin yapısı |
| -------- | ------------------- |
| [Framework bağımlı dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li>Yayımlama&dagger;<ul><li>Günlükleri&dagger; (isteğe bağlı stdout günlükleri almak için gerektirdiği durumlar haricinde)</li><li>Görünümleri&dagger; (MVC uygulamaları; görünümleri önceden derlenmiş değil)</li><li>Sayfaları&dagger; (sayfaları önceden derlenmiş değil, MVC veya Razor sayfaları uygulamaları;)</li><li>wwwroot&dagger;</li><li>*\.DLL dosyaları</li><li>{DERLEME adı}.deps.JSON</li><li>{DERLEME adı} .dll</li><li>{DERLEME adı} .pdb</li><li>{} DERLEME ADI. Views.dll</li><li>{} DERLEME ADI. Views.pdb</li><li>{DERLEME adı}.runtimeconfig.json</li><li>Web.config (IIS dağıtımlar)</li></ul></li></ul> |
| [Kendi içinde dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>Yayımlama&dagger;<ul><li>Günlükleri&dagger; (isteğe bağlı stdout günlükleri almak için gerektirdiği durumlar haricinde)</li><li>Görünümleri&dagger; (MVC uygulamaları; görünümleri önceden derlenmiş değil)</li><li>Sayfaları&dagger; (sayfaları önceden derlenmiş değil, MVC veya Razor sayfaları uygulamaları;)</li><li>wwwroot&dagger;</li><li>\*.dll dosyaları</li><li>{DERLEME adı}.deps.JSON</li><li>{DERLEME adı} .dll</li><li>{DERLEME adı} .exe</li><li>{DERLEME adı} .pdb</li><li>{} DERLEME ADI. Views.dll</li><li>{} DERLEME ADI. Views.pdb</li><li>{DERLEME adı}.runtimeconfig.json</li><li>Web.config (IIS dağıtımlar)</li></ul></li></ul> |

&dagger;Bir dizini gösterir

*Yayımlama* dizini temsil eder *içerik kök yolu*ayrıca adlı *uygulama temel yolu*, dağıtım. Dilediğiniz adı verilir *yayımlama* dizin sunucusuna dağıtılan uygulamanın konumuna barındırılan uygulamasının fiziksel yolu sunucunun görür.

*Wwwroot* dizini varsa yalnızca içeren statik varlıklar.

Stdout *günlükleri* aşağıdaki iki yaklaşımdan birini kullanarak dağıtım için dizin oluşturulabilir:

* Aşağıdaki `<Target>` proje dosyasına öğe:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   `<MakeDir>` Öğesi boş bir oluşturur *günlükleri* yayımlanan çıkış klasöründe. Öğesini kullanan `PublishDir` özelliği klasörü oluşturmak için hedef konumu belirlenemiyor. Web dağıtımı gibi çeşitli dağıtım yöntemleri, dağıtım sırasında boş klasörler atlayın. `<WriteLinesToFile>` Öğesi bir dosya oluşturur *günlükleri* klasörü, sunucu klasörünün dağıtım garanti eder. Çalışan işlemi, hedef klasöre yazma erişimi yoksa, bu yaklaşımı kullanarak klasör oluşturma başarısız olur.

* Fiziksel olarak oluşturma *günlükleri* dağıtım sunucusunda dizin.

Dağıtım dizini okuma/Yürütme izinleri gerektirir. *Günlükleri* dizin okuma/yazma izinleri gerektirir. Dosyaları yazılacağı ek dizinleri okuma/yazma izinleri gerektirir.

## <a name="additional-resources"></a>Ek kaynaklar

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET core uygulama dağıtımı](/dotnet/core/deploying/)
* [Hedef çerçeveler](/dotnet/standard/frameworks)
* [.NET core RID Kataloğu](/dotnet/core/rid-catalog)
