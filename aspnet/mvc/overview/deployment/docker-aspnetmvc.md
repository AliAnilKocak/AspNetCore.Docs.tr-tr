---
uid: mvc/overview/deployment/docker
title: ASP.NET MVC Uygulamalarını Windows Kapsayıcılarına Geçirme
description: Mevcut bir ASP.NET MVC uygulamasını alıp Windows Docker kapsayıcısında çalıştırma hakkında bilgi edinin
keywords: Windows Containers,Docker,ASP.NET MVC
author: BillWagner
ms.author: wiwagn
ms.date: 02/01/2017
ms.topic: article
ms.technology: dotnet-mvc
ms.devlang: dotnet
ms.assetid: c9f1d52c-b4bd-4b5d-b7f9-8f9ceaf778c4
ms.openlocfilehash: fa010e795878b26c79dbe04ef0017373283c4269
ms.sourcegitcommit: 953ff9ea4369f154d6fd0239599279ddd3280009
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403025"
---
# <a name="migrating-aspnet-mvc-applications-to-windows-containers"></a><span data-ttu-id="011d1-104">ASP.NET MVC Uygulamalarını Windows Kapsayıcılarına Geçirme</span><span class="sxs-lookup"><span data-stu-id="011d1-104">Migrating ASP.NET MVC Applications to Windows Containers</span></span>

<span data-ttu-id="011d1-105">Bir Windows kapsayıcısında mevcut .NET Framework tabanlı uygulama çalıştırma uygulamanızı herhangi bir değişiklik gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="011d1-105">Running an existing .NET Framework-based application in a Windows container doesn't require any changes to your app.</span></span> <span data-ttu-id="011d1-106">Bir Windows kapsayıcısında uygulamanızı çalıştırmak için uygulamanızı içeren bir Docker görüntüsü oluşturma ve kapsayıcı başlatın.</span><span class="sxs-lookup"><span data-stu-id="011d1-106">To run your app in a Windows container you create a Docker image containing your app and start the container.</span></span> <span data-ttu-id="011d1-107">Bu konu başlığında, mevcut bir gerçekleştirilecek açıklanmaktadır [ASP.NET MVC uygulaması](http://www.asp.net/mvc) ve bir Windows kapsayıcısında dağıtın.</span><span class="sxs-lookup"><span data-stu-id="011d1-107">This topic explains how to take an existing [ASP.NET MVC application](http://www.asp.net/mvc) and deploy it in a Windows container.</span></span>

<span data-ttu-id="011d1-108">Mevcut bir ASP.NET MVC uygulama ile başlayın ve ardından Visual Studio'yu kullanarak yayımlanan varlıkları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="011d1-108">You start with an existing ASP.NET MVC app, then build the published assets using Visual Studio.</span></span> <span data-ttu-id="011d1-109">Docker, içerir ve uygulamanızı çalıştıran görüntüyü oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="011d1-109">You use Docker to create the image that contains and runs your app.</span></span> <span data-ttu-id="011d1-110">Bir Windows kapsayıcısı içinde çalışan bir siteye göz atın ve uygulamanın çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="011d1-110">You'll browse to the site running in a Windows container and verify the app is working.</span></span>

<span data-ttu-id="011d1-111">Bu makalede, Docker'ın temel bir anlayış varsayılır.</span><span class="sxs-lookup"><span data-stu-id="011d1-111">This article assumes a basic understanding of Docker.</span></span> <span data-ttu-id="011d1-112">Okuyarak Docker hakkında bilgi edinebilirsiniz [Docker'a genel bakış](https://docs.docker.com/engine/understanding-docker/).</span><span class="sxs-lookup"><span data-stu-id="011d1-112">You can learn about Docker by reading the [Docker Overview](https://docs.docker.com/engine/understanding-docker/).</span></span>

<span data-ttu-id="011d1-113">Bir kapsayıcıda çalıştıracaksınız rastgele sorular yanıtlanmaktadır basit bir Web uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="011d1-113">The app you'll run in a container is a simple website that answers questions randomly.</span></span> <span data-ttu-id="011d1-114">Bu uygulama, hiçbir kimlik doğrulaması veya veritabanı depolama alanı ile temel bir MVC uygulamasıdır; olanak tanır odaklanmak web katmanı için bir kapsayıcı devam ediliyor.</span><span class="sxs-lookup"><span data-stu-id="011d1-114">This app is a basic MVC application with no authentication or database storage; it lets you focus on moving the web tier to a container.</span></span> <span data-ttu-id="011d1-115">Gelecekteki konuları taşımak ve kapsayıcılı uygulamaların kalıcı depolamayı yönetmek nasıl gösterir.</span><span class="sxs-lookup"><span data-stu-id="011d1-115">Future topics will show how to move and manage persistent storage in containerized applications.</span></span>

<span data-ttu-id="011d1-116">Uygulamanızı taşımak için aşağıdaki adımları içerir:</span><span class="sxs-lookup"><span data-stu-id="011d1-116">Moving your application involves these steps:</span></span>

1. [<span data-ttu-id="011d1-117">Görüntü varlıkları oluşturmak için bir yayımlama görevi oluşturma.</span><span class="sxs-lookup"><span data-stu-id="011d1-117">Creating a publish task to build the assets for an image.</span></span>](#publish-script)
1. [<span data-ttu-id="011d1-118">Bir Docker görüntüsü oluşturma, uygulamanızı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="011d1-118">Building a Docker image that will run your application.</span></span>](#build-the-image)
1. [<span data-ttu-id="011d1-119">Görüntünüzü çalıştıran bir Docker kapsayıcısı başlatılıyor.</span><span class="sxs-lookup"><span data-stu-id="011d1-119">Starting a Docker container that runs your image.</span></span>](#start-a-container)
1. [<span data-ttu-id="011d1-120">Tarayıcınızı kullanarak uygulamayı doğrulanıyor.</span><span class="sxs-lookup"><span data-stu-id="011d1-120">Verifying the application using your browser.</span></span>](#verify-in-the-browser)

<span data-ttu-id="011d1-121">[Tamamlanmış uygulama](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator) GitHub üzerinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="011d1-121">The [finished application](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator) is on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="011d1-122">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="011d1-122">Prerequisites</span></span>

<span data-ttu-id="011d1-123">Geliştirme makinesi çalıştırılması gerekir</span><span class="sxs-lookup"><span data-stu-id="011d1-123">The development machine must be running</span></span>

- <span data-ttu-id="011d1-124">[Windows 10 Yıldönümü güncelleştirmesi](https://www.microsoft.com/software-download/windows10/) (veya üzeri) veya [Windows Server 2016](https://www.microsoft.com/cloud-platform/windows-server) (veya üzeri).</span><span class="sxs-lookup"><span data-stu-id="011d1-124">[Windows 10 Anniversary Update](https://www.microsoft.com/software-download/windows10/) (or higher) or [Windows Server 2016](https://www.microsoft.com/cloud-platform/windows-server) (or higher).</span></span>
- <span data-ttu-id="011d1-125">[Windows için docker](https://docs.docker.com/docker-for-windows/) -sürüm kararlı 1.13.0 veya 1.12 Beta 26 (veya daha yeni sürümleri)</span><span class="sxs-lookup"><span data-stu-id="011d1-125">[Docker for Windows](https://docs.docker.com/docker-for-windows/) - version Stable 1.13.0 or 1.12 Beta 26 (or newer versions)</span></span>
- <span data-ttu-id="011d1-126">[Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx).</span><span class="sxs-lookup"><span data-stu-id="011d1-126">[Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="011d1-127">Windows Server 2016'yı kullanıyorsanız, yönergelerini izleyin [kapsayıcı konağı dağıtma - Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/deployment/deployment).</span><span class="sxs-lookup"><span data-stu-id="011d1-127">If you are using Windows Server 2016, follow the instructions for [Container Host Deployment - Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/deployment/deployment).</span></span>

<span data-ttu-id="011d1-128">Docker'ı yükleyip sağ tıklatın ve Tepsi simgesi sonra **Windows kapsayıcılarına geç**.</span><span class="sxs-lookup"><span data-stu-id="011d1-128">After installing and starting Docker,  right-click on the tray icon and select **Switch to Windows containers**.</span></span> <span data-ttu-id="011d1-129">Bu, üzerinde Windows temelinde Docker görüntülerini çalıştırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="011d1-129">This is required to run Docker images based on Windows.</span></span> <span data-ttu-id="011d1-130">Bu komutu yürütmek için birkaç saniye sürer:</span><span class="sxs-lookup"><span data-stu-id="011d1-130">This command takes a few seconds to execute:</span></span>

<span data-ttu-id="011d1-131">![Windows kapsayıcı][windows-container]</span><span class="sxs-lookup"><span data-stu-id="011d1-131">![Windows Container][windows-container]</span></span>

## <a name="publish-script"></a><span data-ttu-id="011d1-132">Betik yayımlama</span><span class="sxs-lookup"><span data-stu-id="011d1-132">Publish script</span></span>

<span data-ttu-id="011d1-133">Tek bir yerde bir Docker görüntüsüne yüklemeniz tüm varlıkları toplayın.</span><span class="sxs-lookup"><span data-stu-id="011d1-133">Collect all the assets that you need to load into a Docker image in one place.</span></span> <span data-ttu-id="011d1-134">Visual Studio kullanabileceğiniz **Yayımla** uygulamanız için bir yayımlama profili oluşturmak için komutu.</span><span class="sxs-lookup"><span data-stu-id="011d1-134">You can use the Visual Studio **Publish** command to create a publish profile for your app.</span></span> <span data-ttu-id="011d1-135">Bu profili, bu öğreticide daha sonra hedef görüntünüzü kopyaladığınız bir dizin ağacındaki tüm varlıkları koyacaktır.</span><span class="sxs-lookup"><span data-stu-id="011d1-135">This profile will put all the assets in one directory tree that you copy to your target image later in this tutorial.</span></span>

<span data-ttu-id="011d1-136">**Adımlar yayımlama**</span><span class="sxs-lookup"><span data-stu-id="011d1-136">**Publish Steps**</span></span>

1. <span data-ttu-id="011d1-137">Visual Studio'da web projesine sağ tıklayın ve seçin **Yayımla**.</span><span class="sxs-lookup"><span data-stu-id="011d1-137">Right click on the web project in Visual Studio, and select **Publish**.</span></span>
1. <span data-ttu-id="011d1-138">Tıklayın **özel profil düğmesi**ve ardından **dosya sistemi** yöntemi olarak.</span><span class="sxs-lookup"><span data-stu-id="011d1-138">Click the **Custom profile button**, and then select **File System** as the method.</span></span>
1. <span data-ttu-id="011d1-139">Dizin seçin.</span><span class="sxs-lookup"><span data-stu-id="011d1-139">Choose the directory.</span></span> <span data-ttu-id="011d1-140">Kural gereği, indirilen örneğe kullanan `bin\Release\PublishOutput`.</span><span class="sxs-lookup"><span data-stu-id="011d1-140">By convention, the downloaded sample uses `bin\Release\PublishOutput`.</span></span>

<span data-ttu-id="011d1-141">![Bağlantı yayımlama][publish-connection]</span><span class="sxs-lookup"><span data-stu-id="011d1-141">![Publish Connection][publish-connection]</span></span>

<span data-ttu-id="011d1-142">Açık **dosya yayımlama seçeneği** bölümünü **ayarları** sekmesi. Seçin **yayımlama sırasında ön derleme**.</span><span class="sxs-lookup"><span data-stu-id="011d1-142">Open the **File Publish Options** section of the **Settings** tab. Select **Precompile during publishing**.</span></span> <span data-ttu-id="011d1-143">Bu iyileştirme Docker kapsayıcısı görünümlerde derleme, önceden derlenmiş görünümleri Kopyalamakta olduğunuz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="011d1-143">This optimization means that you'll be compiling views in the Docker container, you are copying the precompiled views.</span></span>

<span data-ttu-id="011d1-144">![Yayımlama ayarları][publish-settings]</span><span class="sxs-lookup"><span data-stu-id="011d1-144">![Publish Settings][publish-settings]</span></span>

<span data-ttu-id="011d1-145">Tıklayın **Yayımla**, ve Visual Studio gerekli tüm varlıkları hedef klasöre kopyalar.</span><span class="sxs-lookup"><span data-stu-id="011d1-145">Click **Publish**, and Visual Studio will copy all the needed assets to the destination folder.</span></span>

## <a name="build-the-image"></a><span data-ttu-id="011d1-146">Görüntüyü oluşturma</span><span class="sxs-lookup"><span data-stu-id="011d1-146">Build the image</span></span>

<span data-ttu-id="011d1-147">Docker görüntünüzü bir Dockerfile içinde tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="011d1-147">Define your Docker image in a Dockerfile.</span></span> <span data-ttu-id="011d1-148">Dockerfile, temel görüntü, ek bileşenler, çalıştırmak istediğiniz uygulamayı ve diğer yapılandırma görüntüleri için yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="011d1-148">The Dockerfile contains instructions for the base image, additional components, the app you want to run, and other configuration images.</span></span>  <span data-ttu-id="011d1-149">Dockerfile giriştir `docker build` oluşturan görüntü komutu.</span><span class="sxs-lookup"><span data-stu-id="011d1-149">The Dockerfile is the input to the `docker build` command, which creates the image.</span></span>

<span data-ttu-id="011d1-150">Temel alan bir görüntü oluşturacaksınız `microsoft/aspnet` görüntü bulunan [Docker Hub](https://hub.docker.com/r/microsoft/aspnet/).</span><span class="sxs-lookup"><span data-stu-id="011d1-150">You will build an image based on the `microsoft/aspnet` image located on [Docker Hub](https://hub.docker.com/r/microsoft/aspnet/).</span></span>
<span data-ttu-id="011d1-151">Temel görüntü `microsoft/aspnet`, bir Windows Server görüntüsüdür.</span><span class="sxs-lookup"><span data-stu-id="011d1-151">The base image, `microsoft/aspnet`, is a Windows Server image.</span></span> <span data-ttu-id="011d1-152">Bu, Windows Server Core, IIS ve ASP.NET 4.6.2 içerir.</span><span class="sxs-lookup"><span data-stu-id="011d1-152">It contains Windows Server Core, IIS and ASP.NET 4.6.2.</span></span> <span data-ttu-id="011d1-153">Bu görüntünün kapsayıcınızda çalıştırdığınızda, IIS otomatik olarak başlatılır ve yüklü Web sitelerini.</span><span class="sxs-lookup"><span data-stu-id="011d1-153">When you run this image in your container, it will automatically start IIS and installed websites.</span></span>

<span data-ttu-id="011d1-154">Dockerfile, görüntüyü oluşturan şöyle görünür:</span><span class="sxs-lookup"><span data-stu-id="011d1-154">The Dockerfile that creates your image looks like this:</span></span>

```console
# The `FROM` instruction specifies the base image. You are
# extending the `microsoft/aspnet` image.

FROM microsoft/aspnet

# The final instruction copies the site you published earlier into the container.
COPY ./bin/Release/PublishOutput/ /inetpub/wwwroot
```

<span data-ttu-id="011d1-155">Var olan hiçbir `ENTRYPOINT` bu Dockerfile içinde komutu.</span><span class="sxs-lookup"><span data-stu-id="011d1-155">There is no `ENTRYPOINT` command in this Dockerfile.</span></span> <span data-ttu-id="011d1-156">Gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="011d1-156">You don't need one.</span></span> <span data-ttu-id="011d1-157">Windows Server, IIS ile çalıştırırken, IIS aspnet temel görüntüde başlamak üzere yapılandırılmış giriş noktası işlemidir.</span><span class="sxs-lookup"><span data-stu-id="011d1-157">When running Windows Server with IIS, the IIS process is the entrypoint, which is configured to start in the aspnet base image.</span></span>

<span data-ttu-id="011d1-158">ASP.NET uygulamanızı çalıştıran görüntüyü oluşturmak için Docker derleme komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="011d1-158">Run the Docker build command to create the image that runs your ASP.NET app.</span></span> <span data-ttu-id="011d1-159">Bunu yapmak için proje dizininde bir PowerShell penceresi açın ve çözüm dizininde değil aşağıdaki komutu yazın:</span><span class="sxs-lookup"><span data-stu-id="011d1-159">To do this, open a PowerShell window in the directory of your project and type the following command in the solution directory:</span></span>

```console
docker build -t mvcrandomanswers .
```

<span data-ttu-id="011d1-160">Bu komut Dockerfile içindeki yönergeleri kullanarak yeni görüntüyü oluşturur adlandırma (-t etiketi) görüntüyü mvcrandomanswers olarak.</span><span class="sxs-lookup"><span data-stu-id="011d1-160">This command will build the new image using the instructions in your Dockerfile, naming (-t tagging) the image as mvcrandomanswers.</span></span> <span data-ttu-id="011d1-161">Bu temel görüntüden çekme içerebilir [Docker Hub](http://hub.docker.com)ve ardından uygulamanızı bu görüntü için ekleme.</span><span class="sxs-lookup"><span data-stu-id="011d1-161">This may include pulling the base image from [Docker Hub](http://hub.docker.com), and then adding your app to that image.</span></span>

<span data-ttu-id="011d1-162">Komut tamamlandıktan sonra çalıştırabileceğiniz `docker images` komutu yeni görüntü üzerindeki bilgileri görmek için:</span><span class="sxs-lookup"><span data-stu-id="011d1-162">Once that command completes, you can run the `docker images` command to see information on the new image:</span></span>

```console
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
mvcrandomanswers              latest              86838648aab6        2 minutes ago       10.1 GB
```

<span data-ttu-id="011d1-163">Resim kimliği makinenize farklı olabilir.</span><span class="sxs-lookup"><span data-stu-id="011d1-163">The IMAGE ID will be different on your machine.</span></span> <span data-ttu-id="011d1-164">Şimdi uygulamayı çalıştıralım.</span><span class="sxs-lookup"><span data-stu-id="011d1-164">Now, let's run the app.</span></span>

## <a name="start-a-container"></a><span data-ttu-id="011d1-165">Bir kapsayıcı başlatma</span><span class="sxs-lookup"><span data-stu-id="011d1-165">Start a container</span></span>

<span data-ttu-id="011d1-166">Aşağıdakini yürüterek bir kapsayıcı başlatma `docker run` komutu:</span><span class="sxs-lookup"><span data-stu-id="011d1-166">Start a container by executing the following `docker run` command:</span></span>

```console
docker run -d --name randomanswers mvcrandomanswers
```

<span data-ttu-id="011d1-167">`-d` Docker görüntü ayrılmış modunda başlatmak için bağımsız değişken bildirir.</span><span class="sxs-lookup"><span data-stu-id="011d1-167">The `-d` argument tells Docker to start the image in detached mode.</span></span> <span data-ttu-id="011d1-168">Docker görüntüsünü geçerli kabuğundan bağlantısı kesilmiş çalıştıran anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="011d1-168">That means the Docker image runs disconnected from the current shell.</span></span>

<span data-ttu-id="011d1-169">Çoğu docker örneklerde - kapsayıcı ve konak bağlantı noktalarını eşleme p görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="011d1-169">In many docker examples, you may see -p to map the container and host ports.</span></span> <span data-ttu-id="011d1-170">Varsayılan ASP.NET görüntü zaten kapsayıcı 80 numaralı bağlantı noktasında dinleyecek ve bunu kullanıma şekilde yapılandırdı.</span><span class="sxs-lookup"><span data-stu-id="011d1-170">The default aspnet image has already configured the container to listen on port 80 and expose it.</span></span> 

<span data-ttu-id="011d1-171">`--name randomanswers` Çalışan kapsayıcıya bir ad verir.</span><span class="sxs-lookup"><span data-stu-id="011d1-171">The `--name randomanswers` gives a name to the running container.</span></span> <span data-ttu-id="011d1-172">Bu ad, komutların çoğu kapsayıcı kimliği yerine kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="011d1-172">You can use this name instead of the container ID in most commands.</span></span>

<span data-ttu-id="011d1-173">`mvcrandomanswers` Başlatmak için görüntüsünü adıdır.</span><span class="sxs-lookup"><span data-stu-id="011d1-173">The `mvcrandomanswers` is the name of the image to start.</span></span>

## <a name="verify-in-the-browser"></a><span data-ttu-id="011d1-174">Tarayıcıda doğrulayın</span><span class="sxs-lookup"><span data-stu-id="011d1-174">Verify in the browser</span></span>

> [!NOTE]
> <span data-ttu-id="011d1-175">Geçerli Windows kapsayıcı sürümü için göz atamazsınız `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="011d1-175">With the current Windows Container release, you can't browse to `http://localhost`.</span></span>
> <span data-ttu-id="011d1-176">Bu bilinen bir WinNAT, davranıştır ve gelecekte çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="011d1-176">This is a known behavior in WinNAT, and it will be resolved in the future.</span></span> <span data-ttu-id="011d1-177">Çözümlenene kadar kapsayıcının IP adresini kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="011d1-177">Until that is addressed, you need to use the IP address of the container.</span></span>

<span data-ttu-id="011d1-178">Kapsayıcı başladıktan sonra çalışan kapsayıcınıza bir tarayıcıdan bağlanabilmek için IP adresini bulun:</span><span class="sxs-lookup"><span data-stu-id="011d1-178">Once the container starts, find its IP address so that you can connect to your running container from a browser:</span></span>

```console
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" randomanswers
172.31.194.61
```

<span data-ttu-id="011d1-179">IPv4 adresi, çalışan kapsayıcıya bağlanmak `http://172.31.194.61` ' de gösterilen örnekteki.</span><span class="sxs-lookup"><span data-stu-id="011d1-179">Connect to the running container using the IPv4 address, `http://172.31.194.61` in the example shown.</span></span> <span data-ttu-id="011d1-180">Bu URL'yi tarayıcınıza yazın ve çalışan site görmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="011d1-180">Type that URL into your browser, and you should see the running site.</span></span>

> [!NOTE]
> <span data-ttu-id="011d1-181">Bazı VPN veya proxy yazılım sitenize gitmesini engelliyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="011d1-181">Some VPN or proxy software may prevent you from navigating to your site.</span></span>
> <span data-ttu-id="011d1-182">Geçici olarak kapsayıcınızı çalıştığından emin olmak için devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="011d1-182">You can temporarily disable it to make sure your container is working.</span></span>

<span data-ttu-id="011d1-183">GitHub üzerinde örnek dizin içeren bir [PowerShell Betiği](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator/run.ps1) , sizin için bu komutları yürütür.</span><span class="sxs-lookup"><span data-stu-id="011d1-183">The sample directory on GitHub contains a [PowerShell script](https://github.com/dotnet/docs/tree/master/samples/framework/docker/MVCRandomAnswerGenerator/run.ps1) that executes these commands for you.</span></span> <span data-ttu-id="011d1-184">Bir PowerShell penceresi açın, dizin değiştirmek, çözüm dizini ve türü için:</span><span class="sxs-lookup"><span data-stu-id="011d1-184">Open a PowerShell window, change directory to your solution directory, and type:</span></span>

```console
./run.ps1
```

<span data-ttu-id="011d1-185">Yukarıdaki komut görüntüyü oluşturur, makinenizde görüntülerin listesini görüntüler, bir kapsayıcısı başlatır ve bu kapsayıcı için IP adresini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="011d1-185">The command above builds the image, displays the list of images on your machine, starts a container, and displays the IP address for that container.</span></span>

<span data-ttu-id="011d1-186">Kapsayıcınızı durdurmak için sorun bir `docker
stop` komutu:</span><span class="sxs-lookup"><span data-stu-id="011d1-186">To stop your container, issue a `docker
stop` command:</span></span>

```console
docker stop randomanswers
```

<span data-ttu-id="011d1-187">Kapsayıcı kaldırmak için sorun bir `docker rm` komutu:</span><span class="sxs-lookup"><span data-stu-id="011d1-187">To remove the container, issue a `docker rm` command:</span></span>

```console
docker rm randomanswers
```

[windows-container]: media/aspnetmvc/SwitchContainer.png "Windows kapsayıcı için geçiş"
[publish-connection]: media/aspnetmvc/PublishConnection.png "Dosya sistemi yayımlama"
[publish-settings]: media/aspnetmvc/PublishSettings.png "Yayımlama ayarları"
