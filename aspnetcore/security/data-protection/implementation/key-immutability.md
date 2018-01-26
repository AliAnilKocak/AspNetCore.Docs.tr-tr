---
title: "Anahtar girişi ve ayarlarını değiştirme"
author: rick-anderson
description: "Bu belge ASP.NET Core veri koruma anahtarı girişi API'leri uygulama ayrıntılarını özetlemektedir."
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 425b8ba9769c2b5ac635693b045e52c110f25205
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2018
---
# <a name="key-immutability-and-changing-settings"></a><span data-ttu-id="1c562-103">Anahtar girişi ve ayarlarını değiştirme</span><span class="sxs-lookup"><span data-stu-id="1c562-103">Key Immutability and changing settings</span></span>

<span data-ttu-id="1c562-104">Yedekleme deposu için bir nesneyi kalıcı sonra kendi gösterimi sonsuza kadar düzeltilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1c562-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="1c562-105">Yeni veri yedekleme deposu eklenebilir, ancak var olan verileri asla dönüşür.</span><span class="sxs-lookup"><span data-stu-id="1c562-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="1c562-106">Bu davranış birincil amacı, veri bozulması engellemektir.</span><span class="sxs-lookup"><span data-stu-id="1c562-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="1c562-107">Bu davranış bir sonucu bir anahtarı yedekleme deposu yazıldıktan sonra değişmez olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="1c562-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="1c562-108">İptal olsa kullanarak kendi oluşturma, etkinleştirme ve sona erme tarihleri hiçbir zaman, değiştirilebilir `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="1c562-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="1c562-109">Ayrıca, temel alınan algoritmik bilgileri, ana anahtar malzemesini ve geri kalan özellikleri şifreleme de değişmez.</span><span class="sxs-lookup"><span data-stu-id="1c562-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="1c562-110">Geliştirici anahtar Kalıcılık etkileyen herhangi bir ayar değişirse, bu değişiklikleri yürürlüğe bir anahtar oluşturulur, zamana kadar için açık bir çağrı aracılığıyla ya da Git olmaz `IKeyManager.CreateNewKey` veya veri koruma sisteminin kendi aracılığıyla [otomatik anahtarı nesil](key-management.md#data-protection-implementation-key-management) davranışı.</span><span class="sxs-lookup"><span data-stu-id="1c562-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](key-management.md#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="1c562-111">Anahtar Kalıcılık etkileyen ayarlar aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="1c562-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="1c562-112">Varsayılan anahtar yaşam süresi</span><span class="sxs-lookup"><span data-stu-id="1c562-112">The default key lifetime</span></span>](key-management.md#data-protection-implementation-key-management)

* [<span data-ttu-id="1c562-113">Anahtar şifreleme rest mekanizması</span><span class="sxs-lookup"><span data-stu-id="1c562-113">The key encryption at rest mechanism</span></span>](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)

* [<span data-ttu-id="1c562-114">Anahtarı içinde yer alan algoritmik bilgileri</span><span class="sxs-lookup"><span data-stu-id="1c562-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="1c562-115">Bu ayarların zaman çalışırken sonraki otomatik anahtarı'den önceki kazandırın gerekiyorsa, açık bir çağrı yapmayı düşünün `IKeyManager.CreateNewKey` yeni bir anahtar oluşturma zorlamak için.</span><span class="sxs-lookup"><span data-stu-id="1c562-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="1c562-116">Bir açık etkinleştirme tarihi vermeyi unutmayın ({şimdi + 2 gün} bir iyi değişikliğin yayılması zaman tanıyın için udur) ve çağrısında sona erme tarihi.</span><span class="sxs-lookup"><span data-stu-id="1c562-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="1c562-117">Depo temas tüm uygulamalar aynı ayarlarla belirtmelisiniz `IDataProtectionBuilder` genişletme yöntemleri.</span><span class="sxs-lookup"><span data-stu-id="1c562-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="1c562-118">Aksi takdirde, kalıcı anahtar özelliklerini anahtar oluşturma yordamları çağrılan belirli uygulamanın bağımlı olur.</span><span class="sxs-lookup"><span data-stu-id="1c562-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
