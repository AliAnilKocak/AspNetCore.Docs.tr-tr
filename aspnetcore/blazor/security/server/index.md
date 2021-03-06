---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: Uygulamaları ASP.NET Core uygulamalar olarak güvenli hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: ba9fe3c0149679fa5760c0c9214cd426f1804c31
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626460"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a>Güvenli ASP.NET Core Blazor Server uygulamaları

[Luke Latham](https://github.com/guardrex) tarafından

Blazor Server uygulamalar, güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır. Daha fazla bilgi için, altındaki makalelere bakın <xref:security/index> . Bu genel bakışın altındaki konular özellikle için geçerlidir Blazor Server . 

## <a name="no-locblazor-server-project-template"></a>Blazor Server Proje şablonu

Proje Blazor Server oluşturulduğunda proje şablonu kimlik doğrulaması için yapılandırılabilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/tooling>Kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için Içindeki Visual Studio kılavuzunu izleyin Blazor Server .

**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda ** Blazor Server uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.

Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:

* **Kimlik doğrulaması yok**
* **Bireysel kullanıcı hesapları**: Kullanıcı hesapları depolanabilir:
  * ASP.NET Core sistemi kullanılarak uygulama içinde [Identity](xref:security/authentication/identity) .
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **İş veya okul hesapları**
* **Windows Kimlik Doğrulaması**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<xref:blazor/tooling>Kimlik doğrulama mekanizmasıyla yeni bir proje oluşturmak için içindeki Visual Studio Code kılavuzunu izleyin Blazor Server :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Açıklama |
| ------------------------ | ----------- |
| `None` varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | Uygulamada depolanan kullanıcılar ASP.NET Core Identity |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. İçindeki Mac için Visual Studio kılavuzunu izleyin <xref:blazor/tooling> .

1. **Yeni Blazor Server uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.

1. Uygulama, uygulamasında depolanan bireysel kullanıcılar için oluşturulur ASP.NET Core Identity .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Bir Blazor Server komut kabuğunda aşağıdaki komutu kullanarak kimlik doğrulama mekanizması ile yeni bir proje oluşturun:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Açıklama |
| ------------------------ | ----------- |
| `None` varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | Uygulamada depolanan kullanıcılar ASP.NET Core Identity |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.

---

## <a name="scaffold-no-locidentity"></a>İskele Identity

IdentityBir projeye yapı iskelesi Blazor Server :

* [Mevcut yetkilendirme olmadan](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Yetkilendirme ile](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
