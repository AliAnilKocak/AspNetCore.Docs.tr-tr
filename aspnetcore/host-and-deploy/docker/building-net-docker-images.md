---
title: ASP.NET Core için Docker görüntüleri
author: rick-anderson
description: Docker kayıt defterinden yayınlanan .NET Core Docker görüntülerini nasıl kullanacağınızı öğrenin. Görüntüleri çekin ve kendi görüntülerinizi oluşturun.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 5bed5e9a4a6109a45badcef7c0d4e03eb2312bf0
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146349"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="802da-104">ASP.NET Core için Docker görüntüleri</span><span class="sxs-lookup"><span data-stu-id="802da-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="802da-105">Bu öğreticide, Docker kapsayıcılarında ASP.NET Core uygulamasının nasıl çalıştırılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="802da-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="802da-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="802da-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="802da-107">Microsoft .NET Core Docker görüntüleri hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="802da-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="802da-108">ASP.NET Core örnek uygulaması indirin</span><span class="sxs-lookup"><span data-stu-id="802da-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="802da-109">Örnek uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="802da-109">Run the sample app locally</span></span>
> * <span data-ttu-id="802da-110">Linux kapsayıcılarında örnek uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="802da-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="802da-111">Örnek uygulamayı Windows kapsayıcılarında çalıştırma</span><span class="sxs-lookup"><span data-stu-id="802da-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="802da-112">El ile derleyin ve dağıtın</span><span class="sxs-lookup"><span data-stu-id="802da-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="802da-113">Docker görüntülerini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="802da-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="802da-114">Bu öğreticide, ASP.NET Core örnek bir uygulama indirir ve Docker kapsayıcılarında çalıştırırsınız.</span><span class="sxs-lookup"><span data-stu-id="802da-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="802da-115">Örnek, hem Linux hem de Windows kapsayıcılarıyla birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="802da-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="802da-116">Örnek Dockerfile, farklı kapsayıcılarda derlemek ve çalıştırmak için [Docker Multi-Stage derleme özelliğini](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır.</span><span class="sxs-lookup"><span data-stu-id="802da-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="802da-117">Derleme ve çalıştırma kapsayıcıları, Docker Hub 'ında Microsoft tarafından sunulan görüntülerden oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="802da-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="802da-118">Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="802da-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="802da-119">Görüntü, komut satırı araçlarını (CLı) içeren .NET Core SDK içerir.</span><span class="sxs-lookup"><span data-stu-id="802da-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="802da-120">Görüntü yerel geliştirme, hata ayıklama ve birim testi için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="802da-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="802da-121">Geliştirme ve derleme için yüklenen araçlar bunu görece büyük bir görüntü haline getirir.</span><span class="sxs-lookup"><span data-stu-id="802da-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="802da-122">Örnek, uygulamayı çalıştırmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="802da-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="802da-123">Görüntü, ASP.NET Core çalışma zamanını ve kitaplıklarını içerir ve üretimde uygulamaları çalıştırmak için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="802da-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="802da-124">Dağıtım ve uygulama başlatma hızı için tasarlanan görüntü görece küçüktür, bu nedenle Docker kayıt defterinden Docker ana bilgisayarına ağ performansı en iyi duruma getirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="802da-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="802da-125">Yalnızca bir uygulamayı çalıştırmak için gereken ikili dosyalar ve içerikler kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="802da-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="802da-126">İçerik çalıştırılmaya hazırlanıyor ve `Docker run` en hızlı süreyi uygulama başlatma ile etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="802da-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="802da-127">Docker modelinde dinamik kod derlemesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="802da-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="802da-128">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="802da-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="802da-129">.NET Core 2,2 SDK</span><span class="sxs-lookup"><span data-stu-id="802da-129">.NET Core 2.2 SDK</span></span>](https://www.microsoft.com/net/core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="802da-130">.NET Core SDK 3,0</span><span class="sxs-lookup"><span data-stu-id="802da-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="802da-131">Docker Client 18,03 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="802da-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="802da-132">Linux dağıtımları</span><span class="sxs-lookup"><span data-stu-id="802da-132">Linux distributions</span></span>
    * [<span data-ttu-id="802da-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="802da-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="802da-134">Debian</span><span class="sxs-lookup"><span data-stu-id="802da-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="802da-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="802da-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="802da-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="802da-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="802da-137">macOS</span><span class="sxs-lookup"><span data-stu-id="802da-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="802da-138">Windows</span><span class="sxs-lookup"><span data-stu-id="802da-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="802da-139">Git</span><span class="sxs-lookup"><span data-stu-id="802da-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="802da-140">Örnek uygulamayı indirme</span><span class="sxs-lookup"><span data-stu-id="802da-140">Download the sample app</span></span>

* <span data-ttu-id="802da-141">[.NET Core Docker deposunu](https://github.com/dotnet/dotnet-docker)kopyalayarak örneği indirin:</span><span class="sxs-lookup"><span data-stu-id="802da-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="802da-142">Uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="802da-142">Run the app locally</span></span>

* <span data-ttu-id="802da-143">*DotNet-Docker/Samples/aspnetapp/aspnetapp*konumundaki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="802da-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="802da-144">Uygulamayı yerel olarak derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="802da-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="802da-145">Uygulamayı test etmek için bir tarayıcıda `http://localhost:5000` ' a gidin.</span><span class="sxs-lookup"><span data-stu-id="802da-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="802da-146">Uygulamayı durdurmak için komut isteminde CTRL + C tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="802da-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="802da-147">Linux kapsayıcısında çalıştırma</span><span class="sxs-lookup"><span data-stu-id="802da-147">Run in a Linux container</span></span>

* <span data-ttu-id="802da-148">Docker istemcisinde Linux kapsayıcıları ' na geçin.</span><span class="sxs-lookup"><span data-stu-id="802da-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="802da-149">*DotNet-Docker/Samples/aspnetapp*konumundaki Dockerfile klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="802da-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="802da-150">Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="802da-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="802da-151">`build` komutu bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="802da-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="802da-152">Görüntüyü aspnetapp olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="802da-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="802da-153">Geçerli klasörde Dockerfile dosyasını arayın (sonundaki süre).</span><span class="sxs-lookup"><span data-stu-id="802da-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="802da-154">Run komutu bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="802da-154">The run command arguments:</span></span>
  * <span data-ttu-id="802da-155">Bir sözde TTY ayırın ve ekli olmasa bile açık tutun.</span><span class="sxs-lookup"><span data-stu-id="802da-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="802da-156">(`--interactive --tty`ile aynı etkiye sahiptir.)</span><span class="sxs-lookup"><span data-stu-id="802da-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="802da-157">Kapsayıcıyı çıkış sırasında otomatik olarak kaldır.</span><span class="sxs-lookup"><span data-stu-id="802da-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="802da-158">Yerel makinedeki 5000 numaralı bağlantı noktasını kapsayıcıda 80 numaralı bağlantı noktasına eşleyin.</span><span class="sxs-lookup"><span data-stu-id="802da-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="802da-159">Kapsayıcının aspnetcore_sample adı.</span><span class="sxs-lookup"><span data-stu-id="802da-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="802da-160">Aspnetapp görüntüsünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="802da-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="802da-161">Uygulamayı test etmek için bir tarayıcıda `http://localhost:5000` ' a gidin.</span><span class="sxs-lookup"><span data-stu-id="802da-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="802da-162">Bir Windows kapsayıcısında Çalıştır</span><span class="sxs-lookup"><span data-stu-id="802da-162">Run in a Windows container</span></span>

* <span data-ttu-id="802da-163">Docker istemcisinde Windows kapsayıcıları ' na geçin.</span><span class="sxs-lookup"><span data-stu-id="802da-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="802da-164">`dotnet-docker/samples/aspnetapp`konumundaki Docker dosya klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="802da-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="802da-165">Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="802da-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="802da-166">Windows kapsayıcıları için, kapsayıcının IP adresine (`http://localhost:5000` göz atma çalışmayacak) sahip olmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="802da-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="802da-167">Başka bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="802da-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="802da-168">Çalışan kapsayıcıları görmek için `docker ps` çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="802da-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="802da-169">"Aspnetcore_sample" kapsayıcısının orada olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="802da-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="802da-170">Kapsayıcının IP adresini göstermek için `docker exec aspnetcore_sample ipconfig` çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="802da-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="802da-171">Komutun çıktısı şu örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="802da-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="802da-172">Kapsayıcı IPv4 adresini (örneğin, 172.29.245.43) kopyalayın ve uygulamayı test etmek için tarayıcı adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="802da-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="802da-173">El ile derleyin ve dağıtın</span><span class="sxs-lookup"><span data-stu-id="802da-173">Build and deploy manually</span></span>

<span data-ttu-id="802da-174">Bazı senaryolarda, çalışma zamanında gerekli olan uygulama dosyalarını kopyalayarak bir kapsayıcıya uygulama dağıtmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="802da-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="802da-175">Bu bölümde nasıl el ile dağıtım yapılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="802da-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="802da-176">*DotNet-Docker/Samples/aspnetapp/aspnetapp*konumundaki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="802da-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="802da-177">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="802da-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="802da-178">Komut bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="802da-178">The command arguments:</span></span>
  * <span data-ttu-id="802da-179">Uygulamayı yayın modunda (varsayılan olarak hata ayıklama modu) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="802da-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="802da-180">*Yayımlanan* klasörde dosyaları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="802da-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="802da-181">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="802da-181">Run the application.</span></span>

  * <span data-ttu-id="802da-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="802da-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="802da-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="802da-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="802da-184">Giriş sayfasını görmek için `http://localhost:5000` gidin.</span><span class="sxs-lookup"><span data-stu-id="802da-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="802da-185">El ile yayınlanan uygulamayı bir Docker kapsayıcısı içinde kullanmak için yeni bir Dockerfile oluşturun ve kapsayıcıyı derlemek için `docker build .` komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="802da-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="802da-186">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="802da-186">The Dockerfile</span></span>

<span data-ttu-id="802da-187">Daha önce çalıştırdığınız `docker build` komutu tarafından kullanılan *Dockerfile* 'ı aşağıda bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="802da-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="802da-188">`dotnet publish`, bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanır.</span><span class="sxs-lookup"><span data-stu-id="802da-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="802da-189">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="802da-189">The Dockerfile</span></span>

<span data-ttu-id="802da-190">Daha önce çalıştırdığınız `docker build` komutu tarafından kullanılan *Dockerfile* 'ı aşağıda bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="802da-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="802da-191">`dotnet publish`, bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanır.</span><span class="sxs-lookup"><span data-stu-id="802da-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out


FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

```console
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a><span data-ttu-id="802da-192">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="802da-192">Additional resources</span></span>

* [<span data-ttu-id="802da-193">Docker derleme komutu</span><span class="sxs-lookup"><span data-stu-id="802da-193">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="802da-194">Docker Run komutu</span><span class="sxs-lookup"><span data-stu-id="802da-194">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="802da-195">[Docker örneğine ASP.NET Core](https://github.com/dotnet/dotnet-docker) (Bu öğreticide kullanılan bir tane).</span><span class="sxs-lookup"><span data-stu-id="802da-195">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="802da-196">Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="802da-196">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="802da-197">Visual Studio Docker araçlarıyla çalışma</span><span class="sxs-lookup"><span data-stu-id="802da-197">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="802da-198">Visual Studio kodu ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="802da-198">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a><span data-ttu-id="802da-199">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="802da-199">Next steps</span></span>

<span data-ttu-id="802da-200">Örnek uygulamayı içeren git deposu, belgeleri de içerir.</span><span class="sxs-lookup"><span data-stu-id="802da-200">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="802da-201">Depodaki kullanılabilir kaynaklara genel bir bakış için [Readme dosyasına](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="802da-201">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="802da-202">Özellikle, HTTPS 'yi uygulamayı öğrenin:</span><span class="sxs-lookup"><span data-stu-id="802da-202">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="802da-203">HTTPS üzerinden Docker ile ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="802da-203">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
