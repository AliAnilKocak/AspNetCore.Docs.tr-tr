---
title: ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 kimliği 'ne geçiş
author: isaac2004
description: ASP.NET Core 2,0 kimliğe üyelik kimlik doğrulaması kullanarak var olan ASP.NET uygulamalarını geçirmeyi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659245"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>ASP.NET üyelik kimlik doğrulamasından ASP.NET Core 2,0 kimliği 'ne geçiş

İle [Isaac Levi](https://isaaclevin.com) tarafından

Bu makalede üyelik kimlik doğrulamasını kullanarak ASP.NET uygulamaları için veritabanı şemasının ASP.NET Core 2,0 kimliğine geçirilmesi gösterilmektedir.

> [!NOTE]
> Bu belge, ASP.NET üyelik tabanlı uygulamalar için veritabanı şemasını ASP.NET Core kimliği için kullanılan veritabanı şemasına geçirmek için gereken adımları sağlar. ASP.NET üyelik tabanlı kimlik doğrulamasından ASP.NET Identity geçirme hakkında daha fazla bilgi için bkz. [SQL üyeliğinden mevcut bir uygulamayı ASP.NET Identity geçirme](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). ASP.NET Core kimliği hakkında daha fazla bilgi için bkz. [ASP.NET Core kimliğe giriş](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Üyelik şemasının incelenmesi

ASP.NET 2,0 ' den önce, geliştiriciler, uygulamaları için tüm kimlik doğrulama ve yetkilendirme sürecini oluşturmaya eklendi. ASP.NET 2,0 ile üyelik tanıtılmıştı ve ASP.NET Apps içindeki güvenliği işlemek için ortak bir çözüm sağlar. Geliştiriciler, bir şemayı, [aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) komutuyla bir SQL Server veritabanına önyükleyebiliyor. Bu komutu çalıştırdıktan sonra, veritabanında aşağıdaki tablolar oluşturulmuştur.

  ![Üyelik tabloları](identity/_static/membership-tables.png)

Mevcut uygulamaları ASP.NET Core 2,0 kimliğine geçirmek için, bu tablolardaki verilerin yeni kimlik şeması tarafından kullanılan tablolara geçirilmesi gerekir.

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core Identity 2,0 şeması

ASP.NET Core 2,0, ASP.NET 4,5 ' de tanıtılan [kimlik](/aspnet/identity/index) ilkesini izler. İlke paylaşılsa da, çerçeveler arasındaki uygulama, ASP.NET Core sürümleri arasında bile farklıdır (bkz. [kimlik doğrulama ve kimliği ASP.NET Core 2,0 ' e geçirme](xref:migration/1x-to-2x/index)).

ASP.NET Core 2,0 kimliği için şemayı görüntülemenin en hızlı yolu, yeni bir ASP.NET Core 2,0 uygulaması oluşturmaktır. Visual Studio 2017 'de şu adımları izleyin:

1. **Dosya** > **Yeni** > **Proje**’yi seçin.
1. *Coreıdentitysample*adlı yeni bir **ASP.NET Core Web uygulaması** projesi oluşturun.
1. Açılan listede **ASP.NET Core 2,0** ' i seçin ve ardından **Web uygulaması**' nı seçin. Bu şablon bir [Razor Pages](xref:razor-pages/index) uygulaması oluşturur. **Tamam**' a tıklamadan önce **kimlik doğrulamasını Değiştir**' e tıklayın.
1. Kimlik şablonları için **bireysel kullanıcı hesapları** seçin. Son olarak **Tamam**' a ve ardından **Tamam**' a tıklayın. Visual Studio ASP.NET Core Identity şablonunu kullanarak bir proje oluşturur.
1. **Paket Yöneticisi Konsolu (PMC** ) penceresini açmak için **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.
1. PMC 'de Proje köküne gidin ve [Entity Framework (EF) Core](/ef/core) `Update-Database` komutunu çalıştırın.

    ASP.NET Core 2,0 kimliği, kimlik doğrulama verilerini depolayan veritabanıyla etkileşime geçmek için EF Core kullanır. Yeni oluşturulan uygulamanın çalışması için, bu verileri depolamak üzere bir veritabanı olması gerekir. Yeni bir uygulama oluşturduktan sonra, bir veritabanı ortamında şemayı incelemenize en hızlı yol, [EF Core geçişlerini](/ef/core/managing-schemas/migrations/)kullanarak veritabanını oluşturmaktır. Bu işlem, yerel olarak veya başka bir yerde, bu şemayı taklit eden bir veritabanı oluşturur. Daha fazla bilgi için önceki belgeleri gözden geçirin.

    EF Core komutları *appSettings. JSON*içinde belirtilen veritabanı için bağlantı dizesini kullanır. Aşağıdaki bağlantı dizesi, *ASP-NET-Core-Identity*adlı *localhost* üzerinde bir veritabanını hedefler. Bu ayarda, EF Core `DefaultConnection` bağlantı dizesini kullanacak şekilde yapılandırılır.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. **Görünüm** > **SQL Server Nesne Gezgini**seçin. *AppSettings. JSON*' nin `ConnectionStrings:DefaultConnection` özelliğinde belirtilen veritabanı adına karşılık gelen düğümü genişletin.

    `Update-Database` komutu şemayla belirtilen veritabanını ve uygulama başlatma için gereken tüm verileri oluşturdu. Aşağıdaki görüntüde, önceki adımlarla oluşturulan tablo yapısı gösterilmektedir.

    ![Kimlik tabloları](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Şemayı geçirme

Hem üyelik hem de ASP.NET Core kimlik için tablo yapılarında ve alanlarında hafif farklar vardır. Bu model, ASP.NET ve ASP.NET Core uygulamalarıyla kimlik doğrulama/yetkilendirme için önemli ölçüde değiştirilmiştir. Kimlik ile hala kullanılan önemli nesneler, *Kullanıcılar* ve *rollerdir*. *Kullanıcılar*, *Roller*ve Kullanıcı *rolleri*için eşleme tabloları aşağıda verilmiştir.

### <a name="users"></a>Kullanıcılar

|*Kimlik<br>(dbo. AspNetUsers)*        ||*Üyelik<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Alan Adı**                 |**Tür**|**Alan Adı**                                    |**Tür**|
|`Id`                           |string  |`aspnet_Users.UserId`                             |string  |
|`UserName`                     |string  |`aspnet_Users.UserName`                           |string  |
|`Email`                        |string  |`aspnet_Membership.Email`                         |string  |
|`NormalizedUserName`           |string  |`aspnet_Users.LoweredUserName`                    |string  |
|`NormalizedEmail`              |string  |`aspnet_Membership.LoweredEmail`                  |string  |
|`PhoneNumber`                  |string  |`aspnet_Users.MobileAlias`                        |string  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> Alan eşlemelerinin hepsi, ASP.NET Core kimlik üyeliğinden bire bir ilişkiye benzemez. Yukarıdaki tablo, varsayılan üyelik kullanıcı şemasını alır ve ASP.NET Core Identity şeması ile eşler. Üyelik için kullanılan diğer özel alanların el ile eşlenmesi gerekir. Bu eşlemede, parola ölçütü ve parola salları iki arasında geçiş yapmadan, parola için bir eşleme yoktur. **Parolayı null olarak bırakmanız ve kullanıcılardan parolalarını sıfırlamalarını istemek için önerilir.** ASP.NET Core kimlik ' de, Kullanıcı kilitlenmişse, gelecekte `LockoutEnd` bir tarih olarak ayarlanmalıdır. Bu, geçiş komut dosyasında gösterilmiştir.

### <a name="roles"></a>Roller

|*Kimlik<br>(dbo. AspNetRoles)*        ||*Üyelik<br>(dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Alan Adı**                 |**Tür**|**Alan Adı**   |**Tür**         |
|`Id`                           |string  |`RoleId`         | string          |
|`Name`                         |string  |`RoleName`       | string          |
|`NormalizedName`               |string  |`LoweredRoleName`| string          |

### <a name="user-roles"></a>Kullanıcı Rolleri

|*Kimlik<br>(dbo. AspNetUserRoles)*||*Üyelik<br>(dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Alan Adı**           |**Tür**  |**Alan Adı**|**Tür**                   |
|`RoleId`                 |string    |`RoleId`      |string                     |
|`UserId`                 |string    |`UserId`      |string                     |

*Kullanıcılar* ve *Roller*için bir geçiş betiği oluştururken önceki eşleme tablolarına başvurun. Aşağıdaki örnek, bir veritabanı sunucusunda iki veritabanınız olduğunu varsayar. Bir veritabanı var olan ASP.NET üyelik şemasını ve verilerini içerir. Diğer *Coreıdentitysample* veritabanı, daha önce açıklanan adımlar kullanılarak oluşturulmuştur. Daha ayrıntılı bilgi için açıklamalar satır içi olarak eklenir.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

Önceki betiği tamamladıktan sonra, daha önce oluşturulan ASP.NET Core kimlik uygulaması, üyelik kullanıcıları ile doldurulur. Kullanıcıların oturum açmadan önce parolalarını değiştirmesi gerekir.

> [!NOTE]
> Üyelik sisteminde kullanıcılara, e-posta adresiyle eşleşmeyen Kullanıcı adları varsa, bu, daha önce oluşturulan uygulamanın buna uyum sağlaması için değişiklikler yapmanız gerekir. Varsayılan şablon `UserName` ve `Email` aynı olmasını bekler. Farklı oldukları durumlar için, oturum açma işleminin `Email`yerine `UserName` kullanacak şekilde değiştirilmesi gerekir.

*Pages\Account\Login.cshtml.cs*adresinde bulunan oturum açma sayfasının `PageModel`, *e-posta* özelliğinden `[EmailAddress]` özniteliğini kaldırın. *Kullanıcı adı*olarak yeniden adlandırın. Bu, *görünümün* ve *pagemodel*'de `EmailAddress` belirtildiği yerde bir değişiklik gerektirir. Sonuç aşağıdakine benzer:

 ![Sabit oturum açma](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ASP.NET Core 2,0 kimlik 'e SQL üyeliğinden kullanıcıların bağlantı noktası oluşturmayı öğrendiniz. ASP.NET Core kimlik hakkında daha fazla bilgi için bkz. [kimliğe giriş](xref:security/authentication/identity).
