---
title: "Çekirdek şifreleme genişletilebilirliği"
author: rick-anderson
description: "IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer ve en üst düzey Fabrika açıklanmaktadır."
ms.author: riande
manager: wpickett
ms.date: 8/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 8a3f4cf267998ddc7f393401059ca9d83ef2d8e7
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/24/2018
---
# <a name="core-cryptography-extensibility"></a><span data-ttu-id="f4181-103">Çekirdek şifreleme genişletilebilirliği</span><span class="sxs-lookup"><span data-stu-id="f4181-103">Core cryptography extensibility</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="f4181-104">Aşağıdaki arabirimleri hiçbirini uygulama türleri iş parçacığı açısından güvenli olması için birden çok arayanlar.</span><span class="sxs-lookup"><span data-stu-id="f4181-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="f4181-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="f4181-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="f4181-106">**IAuthenticatedEncryptor** şifreleme alt sistemi temel yapı bloğu bir arabirimdir.</span><span class="sxs-lookup"><span data-stu-id="f4181-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="f4181-107">Genellikle bir IAuthenticatedEncryptor anahtar başına yoktur ve tüm şifreleme anahtar malzemesi ve şifreleme işlemlerini gerçekleştirmek için gerekli olan algoritmik bilgileri IAuthenticatedEncryptor örneği sarmalar.</span><span class="sxs-lookup"><span data-stu-id="f4181-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="f4181-108">Adından da anlaşılacağı gibi türü kimliği doğrulanmış şifreleme ve şifre çözme hizmetleri sağlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="f4181-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="f4181-109">Aşağıdaki iki API'leri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f4181-109">It exposes the following two APIs.</span></span>

* <span data-ttu-id="f4181-110">Şifre çözme (ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="f4181-110">Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

* <span data-ttu-id="f4181-111">Şifreleme (ArraySegment<byte> düz metin, ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="f4181-111">Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

<span data-ttu-id="f4181-112">Şifreleme yöntemi enciphered düz metin ve bir kimlik doğrulama etiketi içeren blob döndürür.</span><span class="sxs-lookup"><span data-stu-id="f4181-112">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="f4181-113">AAD son yükü kurtarılabilir olması gerekmez ancak kimlik doğrulaması etiketi ek kimliği doğrulanmış veriler (AAD) kapsamalıdır.</span><span class="sxs-lookup"><span data-stu-id="f4181-113">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="f4181-114">Şifre çözme yöntemi kimlik doğrulaması etiketi doğrular ve deciphered yükü döndürür.</span><span class="sxs-lookup"><span data-stu-id="f4181-114">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="f4181-115">Tüm hataları (ArgumentNullException dışında ve benzer) için CryptographicException homogenized.</span><span class="sxs-lookup"><span data-stu-id="f4181-115">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="f4181-116">IAuthenticatedEncryptor örneği gerçekte anahtar malzemesi içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f4181-116">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="f4181-117">Örneğin, uygulama için tüm işlemleri için bir HSM atayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f4181-117">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="f4181-118">Bir IAuthenticatedEncryptor oluşturma</span><span class="sxs-lookup"><span data-stu-id="f4181-118">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f4181-119">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f4181-119">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f4181-120">**IAuthenticatedEncryptorFactory** arabirimi oluşturmak bildiği bir türü temsil eden bir [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneği.</span><span class="sxs-lookup"><span data-stu-id="f4181-120">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="f4181-121">Kendi API aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="f4181-121">Its API is as follows.</span></span>

* <span data-ttu-id="f4181-122">CreateEncryptorInstance (IKey anahtarı): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="f4181-122">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="f4181-123">Verilen tüm IKey örneği için kendi CreateEncryptorInstance yöntemi tarafından oluşturulan tüm kimliği doğrulanmış encryptors eşdeğer olarak düşünülmesi gereken örnek kod aşağıda.</span><span class="sxs-lookup"><span data-stu-id="f4181-123">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f4181-124">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f4181-124">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="f4181-125">**IAuthenticatedEncryptorDescriptor** arabirimi oluşturmak bildiği bir türü temsil eden bir [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneği.</span><span class="sxs-lookup"><span data-stu-id="f4181-125">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="f4181-126">Kendi API aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="f4181-126">Its API is as follows.</span></span>

* <span data-ttu-id="f4181-127">CreateEncryptorInstance(): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="f4181-127">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="f4181-128">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="f4181-128">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="f4181-129">IAuthenticatedEncryptor gibi IAuthenticatedEncryptorDescriptor örneği belirli bir anahtarı sarmalama varsayılır.</span><span class="sxs-lookup"><span data-stu-id="f4181-129">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="f4181-130">Verilen tüm IAuthenticatedEncryptorDescriptor örneği için kendi CreateEncryptorInstance yöntemi tarafından oluşturulan tüm kimliği doğrulanmış encryptors eşdeğer olarak düşünülmesi gereken, yani örnek kod aşağıda.</span><span class="sxs-lookup"><span data-stu-id="f4181-130">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="f4181-131">IAuthenticatedEncryptorDescriptor (ASP.NET 2.x yalnızca çekirdek)</span><span class="sxs-lookup"><span data-stu-id="f4181-131">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f4181-132">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f4181-132">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f4181-133">**IAuthenticatedEncryptorDescriptor** arabirimi kendisini XML biçimine dışa bildiği bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f4181-133">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="f4181-134">Kendi API aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="f4181-134">Its API is as follows.</span></span>

* <span data-ttu-id="f4181-135">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="f4181-135">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f4181-136">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f4181-136">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="f4181-137">XML seri hale getirme</span><span class="sxs-lookup"><span data-stu-id="f4181-137">XML Serialization</span></span>

<span data-ttu-id="f4181-138">IAuthenticatedEncryptor IAuthenticatedEncryptorDescriptor arasındaki birincil fark, tanımlayıcı Şifreleyici oluşturma ve geçerli bağımsız değişkenlerle tedarik bilir ' dir.</span><span class="sxs-lookup"><span data-stu-id="f4181-138">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="f4181-139">Uygulama SymmetricAlgorithm ve KeyedHashAlgorithm dayanır IAuthenticatedEncryptor göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="f4181-139">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="f4181-140">Bu tür tüketmeye Şifreleyici'nın iş, ancak uygun bir uygulamayı yeniden başlatılırsa kendisini yeniden oluşturmak nasıl açıklama gerçekten yazılamıyor şekilde bu mutlaka, bu tür nereden geldiğini bilmiyor.</span><span class="sxs-lookup"><span data-stu-id="f4181-140">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="f4181-141">Bu en üstünde daha yüksek bir düzeye tanımlayıcısı görür.</span><span class="sxs-lookup"><span data-stu-id="f4181-141">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="f4181-142">Şifreleyici örneğinin nasıl oluşturulacağını tanımlayıcısı bilir bu yana (örneğin, bunu gerekli algoritmaları oluşturma bilen), böylece uygulamanın sıfırladıktan sonra Şifreleyici örneği yeniden bu bilgi XML formundaki serileştirebilen.</span><span class="sxs-lookup"><span data-stu-id="f4181-142">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="f4181-143">Tanımlayıcı kendi ExportToXml yordamı seri hale getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f4181-143">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="f4181-144">Bu yordam iki özellikler içeren bir XmlSerializedDescriptorInfo döndürür: tanımlayıcısı ve temsil eden tür XElement temsili bir [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) olabilen karşılık gelen XElement verilen bu tanımlayıcısı resurrect için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f4181-144">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="f4181-145">Serileştirilmiş tanımlayıcısı şifreleme anahtar malzemesi gibi hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="f4181-145">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="f4181-146">Veri koruma sisteminde depolama alanına kalıcı önce bilgilerini şifrelemek için yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f4181-146">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="f4181-147">Bu yararlanmak için tanımlayıcı (xmlns "http://schemas.asp.net/2015/03/dataProtection"), değer "true" öznitelik adı "boşluğu requiresEncryption" ile hassas bilgiler içeren öğe işaretlemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f4181-147">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="f4181-148">Bu öznitelik ayarlamak için bir yardımcı API yoktur.</span><span class="sxs-lookup"><span data-stu-id="f4181-148">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="f4181-149">XElement.MarkAsRequiresEncryption() Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel ad alanında bulunan uzantısı yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="f4181-149">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="f4181-150">Durumları serileştirilmiş tanımlayıcısı hassas bilgileri nerede içermiyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="f4181-150">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="f4181-151">Yeniden bir HSM'de depolanan bir şifreleme anahtarını durumunu göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="f4181-151">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="f4181-152">Tanımlayıcı anahtar malzemesi HSM malzeme düz metin biçiminde kullanıma olmaz bu yana kendisine serileştirilirken yazamıyor.</span><span class="sxs-lookup"><span data-stu-id="f4181-152">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="f4181-153">Bunun yerine, tanımlayıcı (HSM bu şekilde verme izin veriyorsa) anahtar veya anahtar HSM'ın kendi benzersiz tanımlayıcısı anahtar Sarmalanan sürümünü kullanıma yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f4181-153">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="f4181-154">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="f4181-154">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="f4181-155">**IAuthenticatedEncryptorDescriptorDeserializer** arabirimi bir XElement IAuthenticatedEncryptorDescriptor örneğinden seri durumdan çıkarılacak bildiği bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f4181-155">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="f4181-156">Tek bir yöntem sunar:</span><span class="sxs-lookup"><span data-stu-id="f4181-156">It exposes a single method:</span></span>

* <span data-ttu-id="f4181-157">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="f4181-157">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="f4181-158">ImportFromXml yöntemi tarafından döndürülen XElement alır [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) ve özgün IAuthenticatedEncryptorDescriptor bir eşdeğerini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f4181-158">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="f4181-159">IAuthenticatedEncryptorDescriptorDeserializer uygulayan türleri aşağıdaki iki ortak oluşturuculardan birine sahip olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="f4181-159">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="f4181-160">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="f4181-160">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="f4181-161">.ctor()</span><span class="sxs-lookup"><span data-stu-id="f4181-161">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="f4181-162">Oluşturucuya geçirilen IServiceProvider null olabilir.</span><span class="sxs-lookup"><span data-stu-id="f4181-162">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="f4181-163">Üst düzey Fabrika</span><span class="sxs-lookup"><span data-stu-id="f4181-163">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f4181-164">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f4181-164">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f4181-165">**AlgorithmConfiguration** sınıfı temsil eder, nasıl oluşturulacağını bildiği bir tür [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) örnekleri.</span><span class="sxs-lookup"><span data-stu-id="f4181-165">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="f4181-166">Tek bir API sunar.</span><span class="sxs-lookup"><span data-stu-id="f4181-166">It exposes a single API.</span></span>

* <span data-ttu-id="f4181-167">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="f4181-167">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="f4181-168">Üst düzey Fabrika olarak AlgorithmConfiguration düşünün.</span><span class="sxs-lookup"><span data-stu-id="f4181-168">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="f4181-169">Yapılandırma şablon olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="f4181-169">The configuration serves as a template.</span></span> <span data-ttu-id="f4181-170">Algoritmik bilgi sarmalar (örn., bu yapılandırma bir AES 128 GCM ana anahtar ile tanımlayıcıları üretir), ancak henüz belirli bir anahtarı ile ilişkilendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="f4181-170">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="f4181-171">CreateNewDescriptor olarak adlandırılır, yalnızca bu çağrı için yeni anahtar malzemesi oluşturulduğunda ve yeni IAuthenticatedEncryptorDescriptor üretilir, bu anahtar malzeme ve malzeme kullanmak için gereken algoritmik bilgileri sarmalar.</span><span class="sxs-lookup"><span data-stu-id="f4181-171">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="f4181-172">Anahtar malzemesi yazılımda oluşturulabilir (ve bellekte tutulan), onu oluşturulabilir ve bir HSM ve benzeri içinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="f4181-172">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="f4181-173">CreateNewDescriptor herhangi iki çağrıları eşdeğer IAuthenticatedEncryptorDescriptor örnekleri hiçbir zaman oluşturmalısınız önemli noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="f4181-173">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="f4181-174">Gibi AlgorithmConfiguration türü anahtar oluşturma yordamları için giriş noktası olarak hizmet [çalışırken otomatik anahtar](../implementation/key-management.md#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="f4181-174">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#key-expiration-and-rolling).</span></span> <span data-ttu-id="f4181-175">Tüm Gelecekteki anahtarların uygulamasını değiştirmek için KeyManagementOptions AuthenticatedEncryptorConfiguration özelliğini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f4181-175">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f4181-176">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f4181-176">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="f4181-177">**IAuthenticatedEncryptorConfiguration** arabirimi temsil eder, nasıl oluşturulacağını bildiği bir tür [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) örnekleri.</span><span class="sxs-lookup"><span data-stu-id="f4181-177">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="f4181-178">Tek bir API sunar.</span><span class="sxs-lookup"><span data-stu-id="f4181-178">It exposes a single API.</span></span>

* <span data-ttu-id="f4181-179">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="f4181-179">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="f4181-180">Üst düzey Fabrika olarak IAuthenticatedEncryptorConfiguration düşünün.</span><span class="sxs-lookup"><span data-stu-id="f4181-180">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="f4181-181">Yapılandırma şablon olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="f4181-181">The configuration serves as a template.</span></span> <span data-ttu-id="f4181-182">Algoritmik bilgi sarmalar (örn., bu yapılandırma bir AES 128 GCM ana anahtar ile tanımlayıcıları üretir), ancak henüz belirli bir anahtarı ile ilişkilendirilmiş.</span><span class="sxs-lookup"><span data-stu-id="f4181-182">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="f4181-183">CreateNewDescriptor olarak adlandırılır, yalnızca bu çağrı için yeni anahtar malzemesi oluşturulduğunda ve yeni IAuthenticatedEncryptorDescriptor üretilir, bu anahtar malzeme ve malzeme kullanmak için gereken algoritmik bilgileri sarmalar.</span><span class="sxs-lookup"><span data-stu-id="f4181-183">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="f4181-184">Anahtar malzemesi yazılımda oluşturulabilir (ve bellekte tutulan), onu oluşturulabilir ve bir HSM ve benzeri içinde tutulur.</span><span class="sxs-lookup"><span data-stu-id="f4181-184">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="f4181-185">CreateNewDescriptor herhangi iki çağrıları eşdeğer IAuthenticatedEncryptorDescriptor örnekleri hiçbir zaman oluşturmalısınız önemli noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="f4181-185">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="f4181-186">Gibi IAuthenticatedEncryptorConfiguration türü anahtar oluşturma yordamları için giriş noktası olarak hizmet [çalışırken otomatik anahtar](../implementation/key-management.md#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="f4181-186">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#key-expiration-and-rolling).</span></span> <span data-ttu-id="f4181-187">Tüm Gelecekteki anahtarların uygulamasını değiştirmek için hizmet kapsayıcısında IAuthenticatedEncryptorConfiguration tek kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f4181-187">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
