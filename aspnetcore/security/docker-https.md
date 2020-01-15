---
title: HTTPS üzerinden Docker ile görüntüleri barındırma ASP.NET Core
author: rick-anderson
description: HTTPS üzerinden Docker ile ASP.NET Core görüntülerini nasıl barındıralabileceğinizi öğrenin
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Let's Encrypt
uid: security/docker-https
ms.openlocfilehash: 07e2791e5b26975c71323f8cb41a4b0fbe0cdf11
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75952133"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="0291f-103">HTTPS üzerinden Docker ile görüntüleri barındırma ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0291f-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="0291f-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0291f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0291f-105">ASP.NET Core [Varsayılan olarak https](/aspnet/core/security/enforcing-ssl)kullanır.</span><span class="sxs-lookup"><span data-stu-id="0291f-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="0291f-106">[Https](https://en.wikipedia.org/wiki/HTTPS) , güven, kimlik ve şifreleme için [sertifikalara](https://en.wikipedia.org/wiki/Public_key_certificate) bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="0291f-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="0291f-107">Bu belgede önceden oluşturulmuş kapsayıcı görüntülerinin HTTPS ile nasıl çalıştırılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0291f-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="0291f-108">Geliştirme senaryoları için bkz. [https üzerinden Docker ile ASP.NET Core uygulamaları geliştirme](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="0291f-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="0291f-109">Bu örnek, [Docker Istemcisinin](https://www.docker.com/products/docker) [Docker 17,06](https://docs.docker.com/release-notes/docker-ce) veya sonraki bir sürümünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0291f-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0291f-110">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="0291f-110">Prerequisites</span></span>

<span data-ttu-id="0291f-111">Bu belgedeki bazı yönergeler için [.NET Core 2,2 SDK](https://www.microsoft.com/net/download) veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0291f-111">The [.NET Core 2.2 SDK](https://www.microsoft.com/net/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="0291f-112">Sertifikalar</span><span class="sxs-lookup"><span data-stu-id="0291f-112">Certificates</span></span>

<span data-ttu-id="0291f-113">Bir etki alanı için [Üretim barındırma](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) için bir [sertifika yetkilisinden](https://wikipedia.org/wiki/Certificate_authority) bir sertifika gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0291f-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="0291f-114">[Let's Encrypt](https://letsencrypt.org/) , ücretsiz sertifikalar sunan bir sertifika yetkilisindir.</span><span class="sxs-lookup"><span data-stu-id="0291f-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="0291f-115">Bu belge, `localhost`üzerinde önceden oluşturulmuş görüntüleri barındırmak için [otomatik olarak imzalanan geliştirme sertifikaları](https://en.wikipedia.org/wiki/Self-signed_certificate) kullanır.</span><span class="sxs-lookup"><span data-stu-id="0291f-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="0291f-116">Yönergeler, üretim sertifikalarını kullanmaya benzerdir.</span><span class="sxs-lookup"><span data-stu-id="0291f-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="0291f-117">Üretim sertifikaları için:</span><span class="sxs-lookup"><span data-stu-id="0291f-117">For production certs:</span></span>

* <span data-ttu-id="0291f-118">`dotnet dev-certs` aracı gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0291f-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="0291f-119">Sertifikaların, yönergelerde kullanılan konumda depolanması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0291f-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="0291f-120">Herhangi bir konumun çalışması gerekir, ancak bu sertifikalar site dizininizde depolanarak önerilmez.</span><span class="sxs-lookup"><span data-stu-id="0291f-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="0291f-121">Aşağıdaki bölümde yer alan yönergeler, Docker 'ın `-v` komut satırı seçeneğini kullanarak sertifikaları kapsayıcılara bağlama.</span><span class="sxs-lookup"><span data-stu-id="0291f-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="0291f-122">Bir *Dockerfile*içinde `COPY` komutuyla kapsayıcı görüntülerine sertifika ekleyebilirsiniz, ancak bu önerilmez.</span><span class="sxs-lookup"><span data-stu-id="0291f-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="0291f-123">Sertifikaları bir görüntüye kopyalamak aşağıdaki nedenlerden dolayı önerilmez:</span><span class="sxs-lookup"><span data-stu-id="0291f-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="0291f-124">Geliştirici sertifikaları ile test etmek için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="0291f-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="0291f-125">Üretim sertifikaları ile barındırmak için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="0291f-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="0291f-126">Sertifika açıklanmasının önemli bir riski vardır.</span><span class="sxs-lookup"><span data-stu-id="0291f-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="0291f-127">HTTPS ile önceden oluşturulmuş kapsayıcı görüntülerini çalıştırma</span><span class="sxs-lookup"><span data-stu-id="0291f-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="0291f-128">İşletim sistemi yapılandırmanız için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="0291f-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="0291f-129">Linux kapsayıcıları kullanan Windows</span><span class="sxs-lookup"><span data-stu-id="0291f-129">Windows using Linux containers</span></span>

<span data-ttu-id="0291f-130">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="0291f-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="0291f-131">Yukarıdaki komutlarda `{ password here }` bir parolayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0291f-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="0291f-132">Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0291f-132">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="0291f-133">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0291f-133">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="0291f-134">macOS veya Linux</span><span class="sxs-lookup"><span data-stu-id="0291f-134">macOS or Linux</span></span>

<span data-ttu-id="0291f-135">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="0291f-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="0291f-136">`dotnet dev-certs https --trust` yalnızca macOS ve Windows 'ta desteklenir.</span><span class="sxs-lookup"><span data-stu-id="0291f-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="0291f-137">Linux 'ta sertifikalarınızın desteklediği şekilde sertifika almanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="0291f-137">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="0291f-138">Büyük olasılıkla, tarayıcınızda sertifikaya güvenmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="0291f-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="0291f-139">Yukarıdaki komutlarda `{ password here }` bir parolayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0291f-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="0291f-140">Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0291f-140">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="0291f-141">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0291f-141">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="0291f-142">Windows kapsayıcıları kullanan pencereler</span><span class="sxs-lookup"><span data-stu-id="0291f-142">Windows using Windows containers</span></span>

<span data-ttu-id="0291f-143">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="0291f-143">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="0291f-144">Yukarıdaki komutlarda `{ password here }` bir parolayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0291f-144">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="0291f-145">Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0291f-145">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="0291f-146">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0291f-146">The password must match the password used for the certificate.</span></span>
