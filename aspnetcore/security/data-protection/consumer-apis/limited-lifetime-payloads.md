---
title: ASP.NET Core korumalı yükleri ömrü sınırla
author: rick-anderson
description: ASP.NET Core veri koruma API'ları kullanarak korumalı bir yükü ömrü sınırlamak öğrenin.
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 324887b3d29de989ad855c4e78fd5a235fdb560e
ms.sourcegitcommit: 48beecfe749ddac52bc79aa3eb246a2dcdaa1862
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/22/2018
ms.locfileid: "30072029"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="9b6ac-103">ASP.NET Core korumalı yükleri ömrü sınırla</span><span class="sxs-lookup"><span data-stu-id="9b6ac-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="9b6ac-104">Burada belirlenen bir süre sonra süresi dolar korumalı bir yükü oluşturmak için uygulama geliştiricisi istediği senaryo vardır.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="9b6ac-105">Örneğin, korumalı yükü yalnızca bir saat için geçerli olacağını bir parola sıfırlama belirteci temsil edebilir.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="9b6ac-106">Geliştirici bir katıştırılmış sona erme tarihini içerir, kendi yük biçimi oluşturmak kesinlikle mümkündür ve Gelişmiş geliştiriciler bunu yine de yapmak isteyebilirsiniz, ancak bu süre sonu yönetme geliştiriciler çoğunluğu için can sıkıcı büyüyebilir.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="9b6ac-107">Bu bizim Geliştirici hedef kitlesi, paket kolaylaştırmak için [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) otomatik olarak ayarlanmış bir süre sonra süresi dolacak yüklerini oluşturmak için yardımcı programı API'lerini içerir.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="9b6ac-108">Bu API'leri kapatarak askıda `ITimeLimitedDataProtector` türü.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="9b6ac-109">API kullanımı</span><span class="sxs-lookup"><span data-stu-id="9b6ac-109">API usage</span></span>

<span data-ttu-id="9b6ac-110">`ITimeLimitedDataProtector` Koruma ve koruması kaldırıldığında, zaman sınırlı / Self süresi dolan yükü için çekirdek arabirimi bir arabirimdir.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="9b6ac-111">Örneği oluşturmak için bir `ITimeLimitedDataProtector`, önce bir normal örneği gerekir. [Idataprotector](xref:security/data-protection/consumer-apis/overview) belirli bir amaç ile oluşturulmuş.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="9b6ac-112">Bir kez `IDataProtector` örneği kullanılabilir, çağrı `IDataProtector.ToTimeLimitedDataProtector` bir koruyucu yerleşik sona erme özellikleriyle geri almak için genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="9b6ac-113">`ITimeLimitedDataProtector` Aşağıdaki API yüzeyi ve genişletme yöntemlerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="9b6ac-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="9b6ac-114">CreateProtector (dize amacı): ITimeLimitedDataProtector - bu API benzer varolan `IDataProtectionProvider.CreateProtector` oluşturmak için kullanılabilir olduğunu [amaçla zincirleri](xref:security/data-protection/consumer-apis/purpose-strings) kök zaman sınırlı koruyucusu gelen.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="9b6ac-115">(Byte [] düz metin, DateTimeOffset sona erme) korunmasına: byte]</span><span class="sxs-lookup"><span data-stu-id="9b6ac-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="9b6ac-116">Koruma (byte [] düz metin, TimeSpan ömrü): byte]</span><span class="sxs-lookup"><span data-stu-id="9b6ac-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="9b6ac-117">(Byte [] düz) korumak: byte]</span><span class="sxs-lookup"><span data-stu-id="9b6ac-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="9b6ac-118">(String düz metin, DateTimeOffset sona erme) korunmasına: dize</span><span class="sxs-lookup"><span data-stu-id="9b6ac-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="9b6ac-119">Koruma (dize düz metin, TimeSpan ömrü): dize</span><span class="sxs-lookup"><span data-stu-id="9b6ac-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="9b6ac-120">(String düz) korumak: dize</span><span class="sxs-lookup"><span data-stu-id="9b6ac-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="9b6ac-121">Çekirdek yanı sıra `Protect` yalnızca düz metin, alması yöntemleri çalışmayıp'ın sona erme tarihi belirterek izin veren yeni aşırı.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="9b6ac-122">Sona erme tarihini mutlak bir tarih belirtilebilir (aracılığıyla bir `DateTimeOffset`) veya göreli zaman olarak (geçerli sisteminden zaman aracılığıyla bir `TimeSpan`).</span><span class="sxs-lookup"><span data-stu-id="9b6ac-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="9b6ac-123">Bir süre sonu almaz bir aşırı çağrılırsa, yükü dolmayacak varsayılır.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="9b6ac-124">(Byte [] protectedData, DateTimeOffset sona erme çıkışı) korumasını: byte]</span><span class="sxs-lookup"><span data-stu-id="9b6ac-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="9b6ac-125">(Byte [] protectedData) korumasını: byte]</span><span class="sxs-lookup"><span data-stu-id="9b6ac-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="9b6ac-126">(DateTimeOffset sona erme çıkışı dize protectedData) korumasını: dize</span><span class="sxs-lookup"><span data-stu-id="9b6ac-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="9b6ac-127">(String protectedData) korumasını: dize</span><span class="sxs-lookup"><span data-stu-id="9b6ac-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="9b6ac-128">`Unprotect` Yöntemleri özgün korumasız veri döndürür.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="9b6ac-129">Yükü henüz Süresi dolmamışsa mutlak zaman aşımı çıkış parametresi özgün korumasız veri birlikte isteğe bağlı olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="9b6ac-130">Yükü süresi dolmuşsa, tüm aşırı Unprotect yöntemi CryptographicException özel durum oluşturacak.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="9b6ac-131">Bu uzun vadeli veya belirsiz Kalıcılık gerektiren yüklerini korumak için bu API'leri kullanın belirlemeleri değil.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="9b6ac-132">"I bir ay sonra kalıcı olarak kurtarılamaz olmasını korumalı yükü için göze alabilir?"</span><span class="sxs-lookup"><span data-stu-id="9b6ac-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="9b6ac-133">iyi altın kural hizmet verebilir; yanıt ise hiçbir sonra geliştiriciler alternatif API'leri göz önünde bulundurmalısınız.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="9b6ac-134">Örnek kullanır [dı olmayan kod yollarının](xref:security/data-protection/configuration/non-di-scenarios) veri koruma sisteminde başlatmasını için.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="9b6ac-135">Bu örneği çalıştırmak için önce Microsoft.AspNetCore.DataProtection.Extensions paketine başvuru eklediğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="9b6ac-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
