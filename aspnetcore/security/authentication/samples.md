---
title: ASP.NET Core için kimlik doğrulama örnekleri
author: rick-anderson
description: ASP.NET Core deposundaki kimlik doğrulama örneklerine bağlantılar sağlar.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: d49aef198e926d88f1a6727f84b06f0861c8812d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187293"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core için kimlik doğrulama örnekleri

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core deposu](https://github.com/aspnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talep dönüştürme](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [Tanımlama bilgisi kimlik doğrulaması](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı-ıauthorizationpolicyprovider](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [İsteğe bağlı tanımlama bilgisi ve başka bir kimlik doğrulama düzeni arasında seçim yapma](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırın

* Bir [dal](https://github.com/aspnet/AspNetCore)seçin. Örneğin, `Tag:v3.0.0`
* [ASP.NET Core deposunu](https://github.com/aspnet/AspNetCore)kopyalayın veya indirin.
* ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://www.microsoft.com/net/download/all) sürümünü yüklediğinizi doğrulayın.
* *Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile `dotnet run`çalıştırın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core deposu](https://github.com/aspnet/AspNetCore) , *aspnetcore/src/Security/Samples* klasöründe aşağıdaki kimlik doğrulama örneklerini içerir:

* [Talep dönüştürme](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Tanımlama bilgisi kimlik doğrulaması](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Özel ilke sağlayıcısı-ıauthorizationpolicyprovider](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Dinamik kimlik doğrulama şemaları ve seçenekleri](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Dış talepler](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [İsteğe bağlı tanımlama bilgisi ve başka bir kimlik doğrulama düzeni arasında seçim yapma](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Statik dosyalara erişimi kısıtlar](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Örnekleri çalıştırın

* Bir [dal](https://github.com/aspnet/AspNetCore)seçin. Örneğin, `release/2.2`
* [ASP.NET Core deposunu](https://github.com/aspnet/AspNetCore)kopyalayın veya indirin.
* ASP.NET Core deposunun kopyası ile eşleşen [.NET Core SDK](https://www.microsoft.com/net/download/all) sürümünü yüklediğinizi doğrulayın.
* *Aspnetcore/src/Security/Samples* içindeki bir örneğe gidin ve örneği ile `dotnet run`çalıştırın.

::: moniker-end
