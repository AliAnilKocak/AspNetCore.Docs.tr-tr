---
title: ASP.NET Core için topluluk OSS kimlik doğrulama seçenekleri
author: rick-anderson
description: ASP.NET Core için açık kaynak kimlik doğrulama seçenekleri bulur.
ms.author: riande
ms.date: 03/12/2018
uid: security/authentication/community
ms.openlocfilehash: 8b3800631cb71f6bd5120157c89765f6d72628ee
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36276574"
---
# <a name="community-oss-authentication-options-for-aspnet-core"></a><span data-ttu-id="cd0b7-103">ASP.NET Core için topluluk OSS kimlik doğrulama seçenekleri</span><span class="sxs-lookup"><span data-stu-id="cd0b7-103">Community OSS authentication options for ASP.NET Core</span></span>

<span data-ttu-id="cd0b7-104">Bu sayfa, ASP.NET Core için topluluk tarafından sağlanan, açık kaynaklı kimlik doğrulama seçenekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-104">This page contains community-provided, open source authentication options for ASP.NET Core.</span></span> <span data-ttu-id="cd0b7-105">Bu sayfa kullanılabilir duruma yeni sağlayıcıları olarak düzenli aralıklarla güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-105">This page is periodically updated as new providers become available.</span></span>

# <a name="oss-authentication-providers"></a><span data-ttu-id="cd0b7-106">OSS kimlik doğrulama sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="cd0b7-106">OSS authentication providers</span></span>

<span data-ttu-id="cd0b7-107">Aşağıdaki liste alfabetik olarak sıralanan.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-107">The list below is sorted alphabetically.</span></span>

| <span data-ttu-id="cd0b7-108">Ad</span><span class="sxs-lookup"><span data-stu-id="cd0b7-108">Name</span></span> | <span data-ttu-id="cd0b7-109">Açıklama</span><span class="sxs-lookup"><span data-stu-id="cd0b7-109">Description</span></span> |
| ---- | ----------- |
| [<span data-ttu-id="cd0b7-110">AspNet.Security.OpenIdConnect.Server (ASOS)</span><span class="sxs-lookup"><span data-stu-id="cd0b7-110">AspNet.Security.OpenIdConnect.Server (ASOS)</span></span>](https://github.com/aspnet-contrib/AspNet.Security.OpenIdConnect.Server) | <span data-ttu-id="cd0b7-111">ASOS bir alt düzey, Protokolü ilk Openıd Connect server ASP.NET Core ve OWIN/Katana çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-111">ASOS is a low-level, protocol-first OpenID Connect server framework for ASP.NET Core and OWIN/Katana.</span></span> |
| [<span data-ttu-id="cd0b7-112">Cierge</span><span class="sxs-lookup"><span data-stu-id="cd0b7-112">Cierge</span></span>](https://github.com/pwdless/Cierge) | <span data-ttu-id="cd0b7-113">Cierge kullanıcı kaydolma, oturum açma, profilleri, yönetim ve sosyal oturum açmalar işleyen bir Openıd Connect bir sunucudur.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-113">Cierge is an OpenID Connect server that handles user signup, login, profiles, management, and social logins.</span></span> |
| [<span data-ttu-id="cd0b7-114">Gluu sunucu</span><span class="sxs-lookup"><span data-stu-id="cd0b7-114">Gluu Server</span></span>](https://gluu.org/) | <span data-ttu-id="cd0b7-115">Kurumsal hazır açmak kaynak yazılım kimliği, Yönetim (IAM) ve çoklu oturum açma (SSO) erişimi.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-115">Enterprise ready, open source software for identity, access management (IAM), and single sign-on (SSO).</span></span> <span data-ttu-id="cd0b7-116">Daha fazla bilgi için bkz: [Gluu ürün belgelerine](https://gluu.org/docs/).</span><span class="sxs-lookup"><span data-stu-id="cd0b7-116">For more information, see the [Gluu Product Documentation](https://gluu.org/docs/).</span></span> |
| [<span data-ttu-id="cd0b7-117">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="cd0b7-117">IdentityServer</span></span>](https://identityserver.io/) | <span data-ttu-id="cd0b7-118">IdentityServer resmi olarak Openıd Foundation tarafından ve .NET Foundation idare altında sertifikalı ASP.NET Core için Openıd Connect ve OAuth 2.0 bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-118">IdentityServer is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core, officially certified by the OpenID Foundation and under governance of the .NET Foundation.</span></span> <span data-ttu-id="cd0b7-119">Daha fazla bilgi için bkz: ['na Hoş Geldiniz IdentityServer4 (belge)](https://identityserver4.readthedocs.io/en/release/).</span><span class="sxs-lookup"><span data-stu-id="cd0b7-119">For more information, see [Welcome to IdentityServer4 (Documentation)](https://identityserver4.readthedocs.io/en/release/).</span></span> |
| [<span data-ttu-id="cd0b7-120">OpenIddict</span><span class="sxs-lookup"><span data-stu-id="cd0b7-120">OpenIddict</span></span>](https://github.com/openiddict/openiddict-core) | <span data-ttu-id="cd0b7-121">OpenIddict bir kullanımı kolay Openıd Connect için ASP.NET Core sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="cd0b7-121">OpenIddict is an easy-to-use OpenID Connect server for ASP.NET Core.</span></span> |

<span data-ttu-id="cd0b7-122">Bir sağlayıcı eklemek için [bu sayfayı Düzenle](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span><span class="sxs-lookup"><span data-stu-id="cd0b7-122">To add a provider, [edit this page](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span></span>
