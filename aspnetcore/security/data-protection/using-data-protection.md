---
title: ASP.NET Core 'de veri koruma API 'Lerini kullanmaya başlayın
author: rick-anderson
description: Bir uygulamadaki verileri korumak ve korumayı kaldırmak için ASP.NET Core veri koruma API 'Lerini kullanmayı öğrenin.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 8c3f3c7fb21434cf335591c41741f0ce868df33e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664439"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="db317-103">ASP.NET Core 'de veri koruma API 'Lerini kullanmaya başlayın</span><span class="sxs-lookup"><span data-stu-id="db317-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="db317-104">En basit olan verileri korumak aşağıdaki adımlardan oluşur:</span><span class="sxs-lookup"><span data-stu-id="db317-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="db317-105">Bir veri koruma sağlayıcısından veri koruyucusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="db317-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="db317-106">`Protect` yöntemini korumak istediğiniz verilerle çağırın.</span><span class="sxs-lookup"><span data-stu-id="db317-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="db317-107">`Unprotect` yöntemini, düz metin haline döndürmek istediğiniz verilerle çağırın.</span><span class="sxs-lookup"><span data-stu-id="db317-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="db317-108">ASP.NET Core veya SignalRgibi çoğu çerçeve ve uygulama modelleri, veri koruma sistemini zaten yapılandırıp bağımlılık ekleme yoluyla erişenbir hizmet kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="db317-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="db317-109">Aşağıdaki örnek, bağımlılık ekleme ve veri koruma yığınını kaydetme, veri koruma sağlayıcısını dı aracılığıyla alma, bir koruyucu oluşturma ve verilerin korumasını kaldırma için bir hizmet kapsayıcısını yapılandırmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="db317-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="db317-110">Bir koruyucu oluşturduğunuzda bir veya daha fazla [Amaç dizesi](xref:security/data-protection/consumer-apis/purpose-strings)sağlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="db317-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="db317-111">Amaç dizesi, tüketiciler arasında yalıtım sağlar.</span><span class="sxs-lookup"><span data-stu-id="db317-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="db317-112">Örneğin, "yeşil" bir amaç dizesiyle oluşturulan bir koruyucu, "mor" amacını taşıyan bir koruyucu tarafından belirtilen verilerin korumasını yapamaz.</span><span class="sxs-lookup"><span data-stu-id="db317-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="db317-113">`IDataProtectionProvider` ve `IDataProtector` örnekleri, birden çok çağıranlar için iş parçacığı güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="db317-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="db317-114">Bir bileşen `CreateProtector`çağrısı aracılığıyla bir `IDataProtector` başvuru aldığında, bu başvuruyu `Protect` ve `Unprotect`birden çok çağrı için kullanacaktır.</span><span class="sxs-lookup"><span data-stu-id="db317-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="db317-115">Korunan yük doğrulanamazsa veya çözülemez bir `Unprotect` çağrısı CryptographicException oluşturur.</span><span class="sxs-lookup"><span data-stu-id="db317-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="db317-116">Bazı bileşenler, kaldırma işlemleri sırasında hataları yoksaymak isteyebilir; kimlik doğrulama tanımlama bilgilerini okuyan bir bileşen bu hatayı işleyebilir ve isteği, isteğin hemen başarısız olması yerine hiç bir tanımlama bilgisine sahip olmamış gibi değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="db317-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="db317-117">Bu davranışın, tüm özel durumlara izin vermek yerine CryptographicException özel olarak yakalamalı bileşenler.</span><span class="sxs-lookup"><span data-stu-id="db317-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
