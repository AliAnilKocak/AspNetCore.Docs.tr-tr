---
title: "Kimlik birincil anahtar veri türünü yapılandırın"
author: AdrienTorris
description: "Bu makalede, ASP.NET Core kimliği birincil anahtar için istenen veri türü yapılandırma adımlarını açıklar."
keywords: ASP.NET Core, kimlik, birincil anahtar
ms.author: scaddie
manager: wpickett
ms.date: 09/28/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: 5734a9aa86fb2831bd054593ad41c3e3bda4729e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
# <a name="configure-the-aspnet-core-identity-primary-key-data-type"></a>ASP.NET Core kimliği birincil anahtar veri türünü yapılandırın

ASP.NET Core kimlik, bir birincil anahtar temsil etmek için kullanılan veri türü yapılandırmanıza olanak sağlar. Kimlik kullanır `string` varsayılan veri türü. Bu davranışı geçersiz kılabilirsiniz.

## <a name="customize-the-primary-key-data-type"></a>Birincil anahtar veri türünü özelleştirme

1. Özel bir uygulamasını oluşturma [IdentityUser](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuser-1) sınıfı. Kullanıcı nesneleri oluşturmak için kullanılacak türünü temsil eder. Aşağıdaki örnekte, varsayılan `string` türü ile değiştirilir `Guid`.

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4&range=7-13)]

1. Özel bir uygulamasını oluşturma [IdentityRole](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.identity.entityframeworkcore.identityrole-1) sınıfı. Bu rol nesneleri oluşturmak için kullanılacak türünü temsil eder. Aşağıdaki örnekte, varsayılan `string` türü ile değiştirilir `Guid`.
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3&range=7-12)]
    
1. Özel veritabanı bağlamı sınıfının oluşturun. Kimliği için kullanılan Entity Framework veritabanı bağlamı sınıfının devralır. `TUser` Ve `TRole` bağımsız değişkenleri önceki adımda sırasıyla oluşturduğunuz özel kullanıcı ve rol sınıflarını başvuru. `Guid` Veri türü için birincil anahtar tanımlandı.

    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]
    
1. Özel veritabanı bağlamı sınıfının kimliği hizmeti uygulamanın başlangıç sınıfında eklerken kaydedin.

    # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x çekirdek](#tab/aspnetcore2x)
    
    `AddEntityFrameworkStores` Accept yöntemi olmayan bir `TKey` bağımsız olarak mı ASP.NET Core 1.x. Birincil anahtarın veri türü çözümleyerek algılanır `DbContext` nesnesi.
    
    [!code-csharp[Main](identity/sample/src/ASPNETv2-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=6-8&range=25-37)]
    
    # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET 1.x çekirdek](#tab/aspnetcore1x)
    
    `AddEntityFrameworkStores` Yöntemi kabul eden bir `TKey` birincil anahtarın veri türü belirten bağımsız değişkeni.
    
    [!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-55)]
    
    ---

## <a name="test-the-changes"></a>Değişiklikleri test

Yapılandırma değişiklikleri tamamladıktan sonra birincil anahtar temsil eden özellik yeni veri türünü gösterir. Aşağıdaki örnek, bir MVC denetleyicisi özelliğinde erişme gösterir.

[!code-csharp[Main](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Controllers/AccountController.cs?name=snippet_GetCurrentUserId&highlight=6)]
