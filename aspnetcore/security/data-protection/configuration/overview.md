---
title: "ASP.NET çekirdek veri korumasını yapılandırma"
author: rick-anderson
description: "Veri koruma ASP.NET Core yapılandırmayı öğrenin."
ms.author: riande
manager: wpickett
ms.date: 07/17/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/overview
ms.openlocfilehash: c19b1a9cba89a02420c8792b575827d439bc262b
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/19/2018
---
# <a name="configuring-data-protection-in-aspnet-core"></a>ASP.NET çekirdek veri korumasını yapılandırma

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

Veri koruma sistem başlatıldığında geçerli [varsayılan ayarları](xref:security/data-protection/configuration/default-settings) işletimsel ortamına bağlı. Bu ayarlar genellikle tek bir makinede çalışan uygulamalar için uygundur. Burada bir geliştirici kendi uygulama birden fazla makine arasında veya uyumluluk nedenleriyle yayıldığı için varsayılan ayarları, belki de değiştirmek isteyebilirsiniz durumlar vardır. Bu senaryolar için veri koruması sistemi zengin yapılandırma API'si sunar.

Bir genişletme yöntemi yoktur [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) döndüren bir [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder`genişletme yöntemleri, veri korumayı yapılandırmak için seçenekleri zincir olduğunu gösterir.

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Bir UNC paylaşımında anahtarları depolamak için *LOCALAPPDATA %* varsayılan konumu, sistemiyle yapılandırma [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Anahtar Kalıcılık konumunu değiştirirseniz, DPAPI uygun şifreleme mekanizması olup olmadığını bilmiyor beri sistem artık otomatik olarak REST, anahtarları şifreler.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Herhangi bir çağırarak REST anahtarlarınızı korumak için sistem yapılandırabilirsiniz [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) yapılandırma API'leri. Anahtarları bir UNC paylaşımında depolar ve belirli bir X.509 sertifikası ile REST Bu anahtarları şifreler aşağıdaki örnekte, göz önünde bulundurun:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

Bkz: [adresindeki anahtar şifreleme Rest](xref:security/data-protection/implementation/key-encryption-at-rest) örnekler ve tartışma için yerleşik anahtar şifreleme mekanizmaları hakkında daha fazla için.

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

14 gün bir anahtar yaşam süresi 90 gün varsayılan yerine kullanılacak sistemini yapılandırmak için kullanın [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Aynı fiziksel anahtar deposu paylaşıyorsanız bile varsayılan olarak, veri koruma sisteminde birbirinden, uygulamaları yalıtır. Bu, uygulamaların diğer kişilerin korumalı yüklerini anlama engeller. Korumalı yüklerini iki uygulamalar arasında paylaşmak için kullanın [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) her uygulama için aynı değere sahip:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Bir uygulamayı otomatik olarak sona erme yaklaştıkça (yeni anahtarlar oluştur) anahtarları alma için burada istemediğiniz bir senaryo olabilir. Bunun bir örneği burada yalnızca birincil uygulama anahtar yönetimi endişelerini sorumludur ve ikincil uygulamalar yalnızca anahtar halkası salt okunur bir görünümünü sahip bir birincil/ikincil ilişkisinde, ayarladığınız uygulamalar olabilir. İkincil uygulamalar sistemiyle yapılandırarak anahtar halkası salt okunur olarak işlemek için yapılandırılabilir [DisableAutomaticKeyGeneration](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.disableautomatickeygeneration):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Uygulama başına yalıtımı

Veri koruma sisteminde ASP.NET Core ana bilgisayar tarafından sağlandığında, uygulamalarla aynı çalışan işlem hesabı altında çalışan ve aynı ana anahtar malzemesini kullanıyorsanız bile otomatik olarak uygulamaları birbirinden, yalıtır. Bu System.Web 's IsolateApps değiştiricisi biraz benzer  **\<machineKey >** öğesi.

Yalıtım mekanizması çalıştığı yerel makinede bulunan her bir uygulama benzersiz bir kiracı, böylece dikkate alarak [Idataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) belirli bir uygulamanın otomatik olarak içeren bir Ayrıştırıcıyı olarak uygulama kimliği için kökü. Uygulamanın benzersiz Kimliğini iki yerde birinden gelir:

1. Uygulama IIS barındırılıyorsa, uygulamanın yapılandırma yolu benzersiz tanımlayıcısı değil. Bir uygulamayı bir web çiftliği ortamında dağıttıysanız, bu değer IIS ortamlara benzer şekilde web grubundaki tüm makinelerde yapılandırıldığını varsayarak kararlı olmalıdır.

2. Uygulama IIS'de barındırılan değil, uygulamanın fiziksel yolu benzersiz tanımlayıcısı değil.

Benzersiz tanımlayıcı sıfırlar varlığını sürdürmesi için tasarlanmış &mdash; hem tek tek uygulama ve makine.

Bu yalıtım mekanizması uygulamaları kötü amaçlı olmadığını varsayar. Kötü amaçlı bir uygulama her zaman aynı çalışan işlem hesabı altında çalışan herhangi bir uygulamayı etkileyebilir. Uygulamaları birbirini güvenilmeyen olduğu paylaşılan bir barındırma ortamında, barındırma sağlayıcısı anahtar depoları uygulamaların temel ayırarak dahil uygulamalar arasında işletim sistemi düzeyinde yalıtım emin olmak için önlem almanız gerekir.

Veri koruma sisteminde ASP.NET Core ana bilgisayarı tarafından sağlanan değil (örneğin, aracılığıyla örneği varsa `DataProtectionProvider` somut türü) uygulaması yalıtımı varsayılan olarak devre dışıdır. Uygulama yalıtımı devre dışı bırakıldığında, uygun sağladıkları sürece tüm uygulamalar aynı anahtar malzemesini tarafından yedeklenen yüklerini paylaşabilirsiniz [amacıyla](xref:security/data-protection/consumer-apis/purpose-strings). Bu ortamdaki uygulama yalıtımı sağlamak için çağrı [SetApplicationName](#setapplicationname) yapılandırmasında yöntemi nesnesi ve her uygulama için benzersiz bir ad sağlayın.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>UseCryptographicAlgorithms algoritmalarıyla değiştirme

Veri koruma yığını yeni oluşturulan anahtarları tarafından kullanılan varsayılan algoritma değiştirmenize izin verir. Bunu yapmanın en kolay yolu çağırmaktır [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) yapılandırma geri aramadan:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

---

EncryptionAlgorithm AES 256 CBC varsayılandır ve ValidationAlgorithm HMACSHA256 varsayılandır. Varsayılan ilke bir sistem yöneticisi tarafından ayarlanabilir bir [makine genelinde İlkesi](xref:security/data-protection/configuration/machine-wide-policy), ancak için açık bir çağrı `UseCryptographicAlgorithms` varsayılan ilkeyi geçersiz kılar.

Çağırma `UseCryptographicAlgorithms` önceden tanımlanmış bir yerleşik listeden istenilen algoritması belirtmenize olanak tanır. Algoritma uygulama hakkında endişelenmeniz gerekmez. Yukarıdaki senaryoda, veri koruma sisteminde Windows üzerinde çalışan, AES CNG uyarlamasını kullanmayı dener. Aksi takdirde, geri yönetilen döner [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) sınıfı.

Uygulaması yapılan bir çağrı aracılığıyla el ile belirtebilirsiniz [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Algoritmalar değiştirme anahtar halkası varolan anahtarların etkilemez. Yalnızca yeni üretilen anahtarlar da etkiler.

### <a name="specifying-custom-managed-algorithms"></a>Özel yönetilen algoritmaları belirtme

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Özel yönetilen algoritmaları belirtmek için Oluştur bir [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) uygulama türleri işaret örneği:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Özel yönetilen algoritmaları belirtmek için Oluştur bir [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) uygulama türleri işaret örneği:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

---

Genellikle \*türü özellikleri somut için işaret etmelidir (aracılığıyla bir public parametresiz ctor) instantiable uygulamaları [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) ve [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)ancak Sistem özel-durumlarda bazı değerler gibi `typeof(Aes)` kolaylık sağlamak için.

> [!NOTE]
> SymmetricAlgorithm ≥ 128 bit anahtar uzunluğuna ve ≥ 64 bit bir blok boyutu olmalıdır ve PKCS #7 doldurma CBC modunda şifrelemeyle desteklemesi gerekir. Bir Özet boyutunu KeyedHashAlgorithm olmalıdır > = 128 bit ve karma algoritma ait Özet uzunluğuna eşit uzunlukta anahtarları desteklemesi gerekir. KeyedHashAlgorithm HMAC olması kesinlikle gerekli değildir.

### <a name="specifying-custom-windows-cng-algorithms"></a>Özel Windows CNG algoritmaları belirtme

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

İle HMAC doğrulama CBC modunda şifreleme kullanarak özel bir Windows CNG algoritması belirtmek için oluşturma bir [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) algoritmik bilgi içeren örneği:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

İle HMAC doğrulama CBC modunda şifreleme kullanarak özel bir Windows CNG algoritması belirtmek için oluşturma bir [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) algoritmik bilgi içeren örneği:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

---

> [!NOTE]
> Simetrik blok şifreleme algoritması anahtar uzunluğu olmalıdır > = 128 bit, bir blok boyutu > = 64 bit ve PKCS #7 doldurma CBC modunda şifrelemeyle desteklemesi gerekir. Karma algoritması Özet boyutunu olmalıdır > = 128 bit ve BCRYPT ile açılmakta desteklemesi gerekir\_Algoritma\_İŞLEMEK\_HMAC\_BAYRAĞI bayrağı. \*Sağlayıcısı özellikleri ayarlanabilir varsayılan sağlayıcı için belirtilen algoritmasını kullanabilmesi için null. Bkz: [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) daha fazla bilgi için.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Doğrulama ile Galois/sayaç modu şifreleme kullanarak özel bir Windows CNG algoritması belirtmek için oluşturma bir [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) algoritmik bilgi içeren örneği:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Doğrulama ile Galois/sayaç modu şifreleme kullanarak özel bir Windows CNG algoritması belirtmek için oluşturma bir [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) algoritmik bilgi içeren örneği:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

---

> [!NOTE]
> Simetrik blok şifreleme algoritması anahtar uzunluğu olmalıdır > = 128 bit, tam olarak 128 bit blok boyutunu ve GCM şifreleme desteklemesi gerekir. Ayarlayabileceğiniz [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) özelliği belirtilen algoritması için varsayılan sağlayıcıyı kullanmak için null. Bkz: [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) daha fazla bilgi için.

### <a name="specifying-other-custom-algorithms"></a>Özel diğer algoritmalar belirtme

Birinci sınıf bir API gösterilmeyen de, veri koruma neredeyse her türlü algoritması belirtme izin vermek için Genişletilebilir sistemidir. Örneğin, bir donanım güvenlik modülü (HSM) içinde yer alan tüm anahtarları tutmak için ve şifreleme ve şifre çözme yordamları çekirdeğin özel bir uygulama sunmak amacıyla mümkündür. Bkz: [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) içinde [çekirdek şifreleme genişletilebilirlik](xref:security/data-protection/extensibility/core-crypto) daha fazla bilgi için.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Bir Docker kapsayıcısı barındırdığında kalıcı anahtarları

İçinde barındırdığında bir [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) kapsayıcı, anahtarları saklanabilir ya da:

* Paylaşılan bir birim veya ana bilgisayar takılı bir birim gibi kapsayıcının ömür ötesinde devam ederse Docker birimdir bir klasör.
* Bir dış sağlayıcı gibi [Azure anahtar kasası](https://azure.microsoft.com/services/key-vault/) veya [Redis](https://redis.io/).

## <a name="see-also"></a>Ayrıca bkz.

* [Olmayan dı kullanan senaryolar](xref:security/data-protection/configuration/non-di-scenarios)
* [Makine geniş İlkesi](xref:security/data-protection/configuration/machine-wide-policy)
