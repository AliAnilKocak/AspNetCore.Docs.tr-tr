---
title: ASP.NET Core karma parolaları
author: rick-anderson
description: ASP.NET Core veri koruma API'ları kullanarak parola karma öğrenin.
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 63348da144e84d614f274b5d816cbecb020dcab4
ms.sourcegitcommit: 48beecfe749ddac52bc79aa3eb246a2dcdaa1862
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/22/2018
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="97d95-103">ASP.NET Core karma parolaları</span><span class="sxs-lookup"><span data-stu-id="97d95-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="97d95-104">Bir paketi temel veri koruma kodu içerir *Microsoft.AspNetCore.Cryptography.KeyDerivation* şifreleme anahtar türetme işlevler içerir.</span><span class="sxs-lookup"><span data-stu-id="97d95-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="97d95-105">Bu paket, bir tek başına bileşenidir ve veri koruma sisteminde rest üzerinde hiçbir bağımlılığı yoktur.</span><span class="sxs-lookup"><span data-stu-id="97d95-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="97d95-106">Tamamen bağımsız olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="97d95-106">It can be used completely independently.</span></span> <span data-ttu-id="97d95-107">Veri koruma kod kolaylık tabanını yanında kaynağı yok.</span><span class="sxs-lookup"><span data-stu-id="97d95-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="97d95-108">Paket şu anda bir yöntem sunar `KeyDerivation.Pbkdf2` sağlayan kullanarak bir parolayı karma [PBKDF2 algoritması](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="97d95-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="97d95-109">Bu API .NET Framework'ün varolan çok benzer [Rfc2898DeriveBytes türü](https://docs.microsoft.com/dotnet/api/system.security.cryptography.rfc2898derivebytes), ancak üç önemli farklılıklar vardır:</span><span class="sxs-lookup"><span data-stu-id="97d95-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](https://docs.microsoft.com/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="97d95-110">`KeyDerivation.Pbkdf2` Yöntemini destekleyen birden çok PRFs kullanma (şu anda `HMACSHA1`, `HMACSHA256`, ve `HMACSHA512`), ancak `Rfc2898DeriveBytes` destekler yazın `HMACSHA1`.</span><span class="sxs-lookup"><span data-stu-id="97d95-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="97d95-111">`KeyDerivation.Pbkdf2` Yöntemi geçerli işletim sistemini algılar ve bazı durumlarda çok daha iyi performans sağlayan yordamının en iyileştirilmiş uygulama seçmek çalışır.</span><span class="sxs-lookup"><span data-stu-id="97d95-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="97d95-112">(İsteğe bağlı olarak Windows 8'de yaklaşık 10 üretimini x sunar `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="97d95-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="97d95-113">`KeyDerivation.Pbkdf2` Gerektirdiğine çağıran tüm parametreleri belirtin (salt, PRF ve yineleme sayısı).</span><span class="sxs-lookup"><span data-stu-id="97d95-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="97d95-114">`Rfc2898DeriveBytes` Türü için varsayılan değerleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="97d95-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="97d95-115">ASP.NET Core kimliğin için kaynak koduna bakın `PasswordHasher` türü gerçek dünya için kullanım örneği.</span><span class="sxs-lookup"><span data-stu-id="97d95-115">See the source code for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
