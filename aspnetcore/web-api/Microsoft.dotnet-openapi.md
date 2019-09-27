---
title: Openapı kullanarak ASP.NET Core uygulamaları geliştirme
author: ryanbrandenburg
description: Openapı dosyalarına başvurular eklemek için ' Microsoft. DotNet-openapı ' aracının nasıl kullanılacağını gösterir.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317770"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Openapı araçlarını kullanarak ASP.NET Core uygulamaları geliştirme

İle Ryan Brandenburg

[Microsoft. DotNet-openapı](https://www.nuget.org/packages/Microsoft.dotnet-openapi) , bir proje Içindeki [openapı](https://github.com/OAI/OpenAPI-Specification) başvurularını yönetmek için [.NET Core küresel bir araçtır](/dotnet/core/tools/global-tools) .

## <a name="installation"></a>Yükleme

Yüklemek `Microsoft.dotnet-openapi`için şu komutu çalıştırın:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Ekle

Bu sayfadaki komutlardan herhangi birini kullanarak bir openapı başvurusu eklemek, *. csproj* dosyasına `<OpenApiReference />` aşağıdakine benzer bir öğe ekler:

```xml
<OpenApiReference Include="openapi.json" />
```

Uygulamanın oluşturulan istemci kodunu çağırması için önceki başvuru gereklidir.

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Dosya Ekle

#### <a name="options"></a>Seçenekler

| Short seçeneği| Long seçeneği| Açıklama | Örnek |
|-------|------|-------|---------|
| -v|--ayrıntılı | Ayrıntılı çıktıyı göster. |DotNet openapı dosya Ekle *-v* .\Openapi.exe |
| -p|--updateProject | Üzerinde çalışılacak proje. |DotNet openapı dosya Ekle *--updateproject .\Ref.exe* .\Openapi.exe |
| -c|--Code-Generator| Başvuruya uygulanacak kod Oluşturucu. Seçenekler ve `NSwagCSharp` ' `NSwagTypeScript`dir. Belirtilmemişse, araçları varsayılan olarak `NSwagCSharp` ayarlanır.`--code-generator`|DotNet openapı Add dosyası .\Openapi.exe JSON--Code-Generator
| -h|--yardım|Yardım bilgilerini göster|DotNet openapı dosya Ekle--yardım|

#### <a name="arguments"></a>Bağımsız Değişkenler

|  Bağımsız Değişken  | Açıklama | Örnek |
|-------------|-------------|---------|
| Kaynak dosya | Başvuru oluşturulacak kaynak. Bir Openapı dosyası olmalıdır. |DotNet openapı dosya ekleme *.\Openapi.exe* |

### <a name="add-url"></a>URL ekle

#### <a name="options"></a>Seçenekler

| Short seçeneği| Long seçeneği| Açıklama | Örnek |
|-------|------|-------------|---------|
| -v|--ayrıntılı | Ayrıntılı çıktıyı göster. |DotNet openapı URL 'si Ekle *-v*`https://contoso.com/openapi.json` |
| -p|--updateProject | Üzerinde çalışılacak proje. |DotNet openapı Add URL *--updateproject .\Ref.exe*`https://contoso.com/openapi.json` |
| -o|--Çıkış-dosya | Openapı dosyasının yerel kopyasının yerleştirileceği yer. |DotNet openapı Add URL `https://contoso.com/openapi.json` *--çıktı-File MyClient. JSON* |
| -c|--Code-Generator| Başvuruya uygulanacak kod Oluşturucu. Seçenekler ve `NSwagCSharp` ' `NSwagTypeScript`dir. |DotNet openapı Add dosyası .\Openapi.exe JSON--Code-Generator
| -h|--yardım|Yardım bilgilerini göster|DotNet openapı URL ekleme--Yardım|

#### <a name="arguments"></a>Bağımsız Değişkenler

|  Bağımsız Değişken  | Açıklama | Örnek |
|-------------|-------------|---------|
| Kaynak-URL | Başvuru oluşturulacak kaynak. Bir URL olmalıdır. |DotNet openapı URL 'si Ekle`https://contoso.com/openapi.json` |

## <a name="remove"></a>Kaldır

Verilen dosya adıyla eşleşen Openapı başvurusunu *. csproj* dosyasından kaldırır. Openapı başvurusu kaldırıldığında, istemciler oluşturulmaz. Local *. JSON* ve *. YAML* dosyaları silinir.

### <a name="options"></a>Seçenekler

| Short seçeneği| Long seçeneği| Açıklama| Örnek |
|-------|------|------------|---------|
| -v|--ayrıntılı | Ayrıntılı çıktıyı göster. |DotNet openapı Remove *-v*|
| -p|--updateProject | Üzerinde çalışılacak proje. |DotNet openapı Remove *--updateproject .\ref. csproj* .\openapi.exe |
| -h|--yardım|Yardım bilgilerini göster|DotNet openapı Remove--Help|

### <a name="arguments"></a>Bağımsız Değişkenler

|  Bağımsız Değişken  | Açıklama| Örnek |
| ------------|------------|---------|
| Kaynak dosya | Başvurunun kaldırılacağı kaynak. |DotNet openapı kaldırma *.\Openapi.exe* |

## <a name="refresh"></a>Yenile

İndirme URL 'sindeki en son içerik kullanılarak indirilen bir dosyanın yerel sürümünü yeniler.

### <a name="options"></a>Seçenekler

| Short seçeneği| Long seçeneği| Açıklama | Örnek |
|-------|------|-------------|---------|
| -v|--ayrıntılı | Ayrıntılı çıktıyı göster. | DotNet openapı yenileme *-v*`https://contoso.com/openapi.json` |
| -p|--updateProject | Üzerinde çalışılacak proje. | DotNet openapı yenileme *--updateproject .\ref.exe*`https://contoso.com/openapi.json` |
| -h|--yardım|Yardım bilgilerini göster|DotNet openapı yenilemesi--yardım|

### <a name="arguments"></a>Bağımsız Değişkenler

|  Bağımsız Değişken  | Açıklama | Örnek |
| ------------|-------------|---------|
| Kaynak-URL | Başvurunun yenileneceği URL. | DotNet openapı yenilemesi`https://contoso.com/openapi.json` |
