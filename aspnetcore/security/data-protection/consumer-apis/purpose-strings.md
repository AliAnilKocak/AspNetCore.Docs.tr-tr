---
title: "Amaç dizeleri"
author: rick-anderson
description: "Bu belgenin amacı dizeleri ASP.NET Core veri koruma API'ları nasıl kullanıldığını ayrıntıları verilmektedir."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: b4a0db801ecc1c4ba0762f0c9faf7429b4ac097b
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/30/2018
---
# <a name="purpose-strings"></a><span data-ttu-id="1a855-103">Amaç dizeleri</span><span class="sxs-lookup"><span data-stu-id="1a855-103">Purpose Strings</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="1a855-104">Tüketen bileşenleri `IDataProtectionProvider` benzersiz bir geçmelidir *amacıyla* parametresi `CreateProtector` yöntemi.</span><span class="sxs-lookup"><span data-stu-id="1a855-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="1a855-105">Amacıyla *parametresi* kök şifreleme anahtarları aynı olsa bile, şifreleme tüketicileri arasında yalıtım sağlar gibi veri koruma sisteminde güvenlik devralınır.</span><span class="sxs-lookup"><span data-stu-id="1a855-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="1a855-106">Bir tüketici bir amaç belirttiğinde amacı dize bu tüketiciye şifreleme alt anahtarlarını benzersiz çıkarmaya kök şifreleme anahtarları ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1a855-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="1a855-107">Bu diğer şifreleme tüketicileri uygulamadaki tüketiciden yalıtır: başka bir bileşeni, yükü okuyabilir ve herhangi diğer bileşenin yüklerini okunamıyor.</span><span class="sxs-lookup"><span data-stu-id="1a855-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="1a855-108">Bu yalıtım da bileşen saldırısı uyuşmazlığa tüm kategorileri işler.</span><span class="sxs-lookup"><span data-stu-id="1a855-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Amaç diyagramı örneği](purpose-strings/_static/purposes.png)

<span data-ttu-id="1a855-110">Yukarıdaki diyagramda `IDataProtector` örnekleri A ve B **olamaz** birbirlerinin yükü, yalnızca okuma kendi.</span><span class="sxs-lookup"><span data-stu-id="1a855-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="1a855-111">Amaç dize gizli olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1a855-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="1a855-112">Yalnızca başka bir iyi çalışan bileşen aynı amacı dize erişiminizi sağlayacaktır anlamda benzersiz olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a855-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="1a855-113">Veri Koruma API tüketen bileşen ad alanı ve tür adını kullanarak bir iyi, bu bilgileri hiçbir zaman çakışacak yöntem olduğu gibi udur.</span><span class="sxs-lookup"><span data-stu-id="1a855-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="1a855-114">Taşıyıcı belirteçlerini minting için sorumlu olan Contoso yazılan bir bileşen Contoso.Security.BearerToken kendi amacı dize olarak kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="1a855-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="1a855-115">Veya, kendi amacı dize olarak Contoso.Security.BearerToken.v1 - bile daha iyi - kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1a855-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="1a855-116">Sürüm numarası ekleme Contoso.Security.BearerToken.v2 amacı kullanmak gelecekteki bir sürümüne sağlar ve yüklerini Git oldukça farklı sürümlerini birbirinden tamamen yalıtılmış olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1a855-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="1a855-117">Amacıyla parametresi itibaren `CreateProtector` bir dize dizisi yukarıdaki yerine olarak belirtilmiş `[ "Contoso.Security.BearerToken", "v1" ]`.</span><span class="sxs-lookup"><span data-stu-id="1a855-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="1a855-118">Bu amaçlar hiyerarşisini kurma verir ve veri koruma sisteminde çoklu kiracı senaryolarıyla olasılığını açar.</span><span class="sxs-lookup"><span data-stu-id="1a855-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="1a855-119">Bileşenleri tek bir kaynak amacıyla zinciri için girdi olarak güvenilmeyen kullanıcı girişi izin vermemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="1a855-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="1a855-120">Örneğin, bir bileşenin güvenli iletiler depolamak için sorumlu Contoso.Messaging.SecureMessage göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="1a855-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="1a855-121">Güvenli ileti sistemi bileşeni çağırmak için olsaydı `CreateProtector([ username ])`, kötü niyetli bir kullanıcının bir hesap kullanıcı adı "Contoso.Security.BearerToken" ile çağırmak için bileşen alma girişimi oluşturup `CreateProtector([ "Contoso.Security.BearerToken" ])`, böylece yanlışlıkla güvenli Mesajlaşma neden kimlik doğrulama belirteçleri algılanan Naneli yüklerini sisteme.</span><span class="sxs-lookup"><span data-stu-id="1a855-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="1a855-122">İleti sistemi bileşeni için daha iyi bir amacıyla zinciri olacaktır `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, uygun yalıtım sağlar.</span><span class="sxs-lookup"><span data-stu-id="1a855-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="1a855-123">Tarafından sağlanan yalıtım ve davranışlarını `IDataProtectionProvider`, `IDataProtector`, ve amacıyla aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="1a855-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="1a855-124">İçin bir verilen `IDataProtectionProvider` nesnesi `CreateProtector` yöntemi oluşturacak bir `IDataProtector` nesneyi benzersiz olarak bağlı hem de `IDataProtectionProvider` ve yöntemine geçildi amacıyla parametresini oluşturulan bir nesne.</span><span class="sxs-lookup"><span data-stu-id="1a855-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="1a855-125">Amaç parametresi null olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="1a855-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="1a855-126">(Amacıyla bir dizi olarak belirtilirse, bu dizinin sıfır uzunluğunda olmalıdır ve dizinin tüm öğeleri null olmayan olmalıdır anlamına gelir.) Boş dize amacı teknik olarak izin verilir, ancak önerilmez.</span><span class="sxs-lookup"><span data-stu-id="1a855-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="1a855-127">(Sıralı bir karşılaştırıcı kullanarak) aynı dizeleri aynı sırada içerir ve yalnızca, iki amaca bağımsız değişkenleri eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="1a855-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="1a855-128">Tek amaçlı bağımsız değişkeni, karşılık gelen tek öğe amacıyla diziye eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="1a855-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="1a855-129">İki `IDataProtector` eşdeğerini oluşturulmuştur ve yalnızca, nesneleri eşdeğer `IDataProtectionProvider` nesneleri eşdeğer amacıyla parametrelere sahip.</span><span class="sxs-lookup"><span data-stu-id="1a855-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="1a855-130">İçin bir verilen `IDataProtector` nesnesi, bir çağrı `Unprotect(protectedData)` özgün döndürülecek `unprotectedData` ve yalnızca, `protectedData := Protect(unprotectedData)` için eşdeğer bir `IDataProtector` nesnesi.</span><span class="sxs-lookup"><span data-stu-id="1a855-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="1a855-131">Biz burada bazı bileşeni ile başka bir bileşen çakışma bilinen bir amaç dize bilerek seçer çalışması düşünüyorsunuz değil.</span><span class="sxs-lookup"><span data-stu-id="1a855-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="1a855-132">Bu tür bir bileşen temelde kötü amaçlı olarak kabul edilir ve bu sistem kötü amaçlı kod içinde çalışan işlemi zaten çalışıyor gerektiğinde, güvenlik garantileri sağlamaya yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="1a855-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
