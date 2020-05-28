---
<span data-ttu-id="48500-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="48500-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="48500-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="48500-102">'Blazor'</span></span>
- <span data-ttu-id="48500-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="48500-103">'Identity'</span></span>
- <span data-ttu-id="48500-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="48500-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="48500-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="48500-105">'Razor'</span></span>
- <span data-ttu-id="48500-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="48500-106">'SignalR' uid:</span></span> 

---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="48500-107">ASP.NET Core ile Visual Studio kapsayıcı araçları</span><span class="sxs-lookup"><span data-stu-id="48500-107">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="48500-108">Visual Studio 2017 ve üzeri sürümleri, .NET Core 'u hedefleyen ASP.NET Core Kapsayıcılı uygulama oluşturma, hata ayıklama ve çalıştırma desteği sağlar.</span><span class="sxs-lookup"><span data-stu-id="48500-108">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="48500-109">Hem Windows hem de Linux kapsayıcıları desteklenir.</span><span class="sxs-lookup"><span data-stu-id="48500-109">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="48500-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48500-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="48500-111">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="48500-111">Prerequisites</span></span>

* [<span data-ttu-id="48500-112">Docker for Windows</span><span class="sxs-lookup"><span data-stu-id="48500-112">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="48500-113">**.NET Core platformlar arası geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="48500-113">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="48500-114">Yükleme ve kurulum</span><span class="sxs-lookup"><span data-stu-id="48500-114">Installation and setup</span></span>

<span data-ttu-id="48500-115">Docker yüklemesi için öncelikle [Docker for Windows: yüklemeden önce bilmeniz](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)gereken bilgileri gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="48500-115">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="48500-116">Ardından, [Windows Için Docker](https://docs.docker.com/docker-for-windows/install/)'ı yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="48500-116">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="48500-117">Docker for Windows içindeki **[paylaşılan sürücüler](https://docs.docker.com/docker-for-windows/#shared-drives)** , birim eşlemesini ve hata ayıklamayı destekleyecek şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="48500-117">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="48500-118">Sistem tepsisinin Docker simgesine sağ tıklayın, **Ayarlar**' ı seçin ve **paylaşılan sürücüler**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="48500-118">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="48500-119">Docker 'ın dosyaları depoladığı sürücüyü seçin.</span><span class="sxs-lookup"><span data-stu-id="48500-119">Select the drive where Docker stores files.</span></span> <span data-ttu-id="48500-120">**Uygula**’ya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="48500-120">Click **Apply**.</span></span>

![Kapsayıcılar için yerel C sürücüsü paylaşımını seçme iletişim kutusu](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="48500-122">Visual Studio 2017 sürümleri 15,6 ve sonraki sürümler, **paylaşılan sürücülerin** yapılandırılmadığı zaman sorar.</span><span class="sxs-lookup"><span data-stu-id="48500-122">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="48500-123">Docker kapsayıcısına proje ekleme</span><span class="sxs-lookup"><span data-stu-id="48500-123">Add a project to a Docker container</span></span>

<span data-ttu-id="48500-124">ASP.NET Core bir projeyi Kapsayıcılı hale getirmek için, projenin .NET Core 'u hedeflemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="48500-124">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="48500-125">Hem Linux hem de Windows kapsayıcıları desteklenir.</span><span class="sxs-lookup"><span data-stu-id="48500-125">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="48500-126">Bir projeye Docker desteği eklerken, bir Windows veya Linux kapsayıcısı seçin.</span><span class="sxs-lookup"><span data-stu-id="48500-126">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="48500-127">Docker ana bilgisayarı aynı kapsayıcı türünü çalıştırıyor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="48500-127">The Docker host must be running the same container type.</span></span> <span data-ttu-id="48500-128">Çalışan Docker örneğindeki kapsayıcı türünü değiştirmek için, sistem tepsisinin Docker simgesine sağ tıklayın ve **Windows kapsayıcılarına geç...** ' i seçin veya **Linux kapsayıcılarına geç..**..</span><span class="sxs-lookup"><span data-stu-id="48500-128">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="48500-129">Yeni uygulama</span><span class="sxs-lookup"><span data-stu-id="48500-129">New app</span></span>

<span data-ttu-id="48500-130">**ASP.NET Core Web uygulaması** proje şablonlarıyla yeni bir uygulama oluştururken **Docker desteğini etkinleştir** onay kutusunu seçin:</span><span class="sxs-lookup"><span data-stu-id="48500-130">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Docker desteğini etkinleştir onay kutusu](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="48500-132">Hedef çerçeve .NET Core ise, **Işletim sistemi** açılır listesi bir kapsayıcı türü seçimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="48500-132">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="48500-133">Mevcut uygulama</span><span class="sxs-lookup"><span data-stu-id="48500-133">Existing app</span></span>

<span data-ttu-id="48500-134">.NET Core 'u hedefleyen ASP.NET Core projeleri için, araç aracılığıyla Docker desteği eklemenin iki seçeneği vardır.</span><span class="sxs-lookup"><span data-stu-id="48500-134">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="48500-135">Visual Studio 'da projeyi açın ve aşağıdaki seçeneklerden birini belirleyin:</span><span class="sxs-lookup"><span data-stu-id="48500-135">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="48500-136">**Proje** menüsünden **Docker desteği** ' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="48500-136">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="48500-137">**Çözüm Gezgini** projeye sağ tıklayın ve **Add**  >  **Docker desteği**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="48500-137">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="48500-138">Visual Studio kapsayıcı araçları, .NET Framework hedefleme ASP.NET Core var olan bir projeye Docker eklemeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="48500-138">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="48500-139">Dockerfile genel bakış</span><span class="sxs-lookup"><span data-stu-id="48500-139">Dockerfile overview</span></span>

<span data-ttu-id="48500-140">Bir *Dockerfile*, son bir Docker görüntüsü oluşturmak için tarif, proje köküne eklenir.</span><span class="sxs-lookup"><span data-stu-id="48500-140">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="48500-141">İçindeki komutları anlamak için [Dockerfile başvurusuna](https://docs.docker.com/engine/reference/builder/) bakın.</span><span class="sxs-lookup"><span data-stu-id="48500-141">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="48500-142">Bu belirli *Dockerfile* , dört farklı, adlandırılmış derleme aşamaları ile [çok aşamalı bir yapı](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır:</span><span class="sxs-lookup"><span data-stu-id="48500-142">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="48500-143">Yukarıdaki *Dockerfile* , [Microsoft/DotNet](https://hub.docker.com/r/microsoft/dotnet/) görüntüsünü temel alır.</span><span class="sxs-lookup"><span data-stu-id="48500-143">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="48500-144">Bu temel görüntü, ASP.NET Core çalışma zamanı ve NuGet paketlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="48500-144">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="48500-145">Paketler, başlangıç performansını geliştirmek için derlenen tek zaman (JıT).</span><span class="sxs-lookup"><span data-stu-id="48500-145">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="48500-146">Yeni proje iletişim kutusunun **https Için Yapılandır** onay kutusu Işaretlendiğinde, *dockerfile* iki bağlantı noktasını kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="48500-146">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="48500-147">HTTP trafiği için bir bağlantı noktası kullanılır; diğer bağlantı noktası HTTPS için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="48500-147">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="48500-148">Onay kutusu işaretli değilse, HTTP trafiği için tek bir bağlantı noktası (80) gösterilir.</span><span class="sxs-lookup"><span data-stu-id="48500-148">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="48500-149">Yukarıdaki *Dockerfile* , [Microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) görüntüsünü temel alır.</span><span class="sxs-lookup"><span data-stu-id="48500-149">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="48500-150">Bu temel görüntüde, başlangıç performansını geliştirmek için tek seferlik (JıT) derlenen ASP.NET Core NuGet paketleri bulunur.</span><span class="sxs-lookup"><span data-stu-id="48500-150">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="48500-151">Bir uygulamaya kapsayıcı Orchestrator desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="48500-151">Add container orchestrator support to an app</span></span>

<span data-ttu-id="48500-152">Visual Studio 2017, tek kapsayıcı düzenleme çözümü olarak [Docker Compose](https://docs.docker.com/compose/overview/) 15,7 veya önceki bir sürümü destekler.</span><span class="sxs-lookup"><span data-stu-id="48500-152">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="48500-153">Docker Compose yapıtlar, **Add**  >  **Docker desteği**Ekle aracılığıyla eklenir.</span><span class="sxs-lookup"><span data-stu-id="48500-153">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="48500-154">Visual Studio 2017 sürümleri 15,8 veya üzeri bir düzenleme çözümünü yalnızca sorulduğunda ekleyin.</span><span class="sxs-lookup"><span data-stu-id="48500-154">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="48500-155">**Çözüm Gezgini** ' de projeye sağ tıklayın ve **Add**  >  **kapsayıcı Orchestrator desteği**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="48500-155">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="48500-156">Aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="48500-156">The following choices are available:</span></span> 

* [<span data-ttu-id="48500-157">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="48500-157">Docker Compose</span></span>](#docker-compose)
* [<span data-ttu-id="48500-158">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="48500-158">Service Fabric</span></span>](#service-fabric)
* [<span data-ttu-id="48500-159">Kubernetes/Held</span><span class="sxs-lookup"><span data-stu-id="48500-159">Kubernetes/Helm </span></span>](https://helm.sh/)

### <a name="docker-compose"></a><span data-ttu-id="48500-160">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="48500-160">Docker Compose</span></span>

<span data-ttu-id="48500-161">Visual Studio kapsayıcı araçları, aşağıdaki dosyalarla çözüme bir *Docker-Compose* projesi ekler:</span><span class="sxs-lookup"><span data-stu-id="48500-161">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="48500-162">*Docker-Compose. dcproj*: projeyi temsil eden dosya.</span><span class="sxs-lookup"><span data-stu-id="48500-162">*docker-compose.dcproj*: The file representing the project.</span></span> <span data-ttu-id="48500-163">`<DockerTargetOS>`Kullanılacak işletim sistemini belirleyen bir öğesi içerir.</span><span class="sxs-lookup"><span data-stu-id="48500-163">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="48500-164">*. dockerıgnore*: derleme bağlamı oluşturulurken dışlanacak dosya ve Dizin düzenlerini listeler.</span><span class="sxs-lookup"><span data-stu-id="48500-164">*.dockerignore*: Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="48500-165">*Docker-Compose. yml*: sırasıyla ve ile oluşturulan ve çalıştıran görüntülerin koleksiyonunu tanımlamak için kullanılan temel [Docker Compose](https://docs.docker.com/compose/overview/) dosyası `docker-compose build` `docker-compose run` .</span><span class="sxs-lookup"><span data-stu-id="48500-165">*docker-compose.yml*: The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="48500-166">*Docker-Compose. override. yml*: isteğe bağlı bir dosya, Docker Compose tarafından okunan hizmetler için yapılandırma geçersiz kılmalarıyla.</span><span class="sxs-lookup"><span data-stu-id="48500-166">*docker-compose.override.yml*: An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="48500-167">Visual Studio `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` Bu dosyaları birleştirmek için yürütülür.</span><span class="sxs-lookup"><span data-stu-id="48500-167">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="48500-168">*Docker-Compose. yml* dosyası, proje çalışırken oluşturulan görüntünün adına başvurur:</span><span class="sxs-lookup"><span data-stu-id="48500-168">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="48500-169">Yukarıdaki örnekte, `image: hellodockertools` `hellodockertools:dev` uygulama **hata ayıklama** modunda çalıştırıldığında görüntüyü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="48500-169">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="48500-170">`hellodockertools:latest`Uygulama **yayın** modunda çalıştığında görüntü oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="48500-170">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="48500-171">Görüntü kayıt defterine itilse, görüntü adını [Docker Hub](https://hub.docker.com/) Kullanıcı adı (örneğin,) ile önek yapın `dockerhubusername/hellodockertools` .</span><span class="sxs-lookup"><span data-stu-id="48500-171">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="48500-172">Alternatif olarak, yapılandırmaya bağlı olarak, görüntü adını özel kayıt defteri URL 'sini (örneğin,) içerecek şekilde değiştirin `privateregistry.domain.com/hellodockertools` .</span><span class="sxs-lookup"><span data-stu-id="48500-172">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="48500-173">Yapı yapılandırmasına (örneğin, hata ayıklama veya sürüm) göre farklı davranışlar istiyorsanız, yapılandırmaya özgü *Docker-Compose* dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="48500-173">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="48500-174">Dosyalar derleme yapılandırmasına göre adlandırılmalıdır (örneğin, *Docker-Compose. vs. Debug. yıml* ve *Docker-Compose. vs. Release. yıml*) ve *Docker-Compose-override. yıml* dosyasıyla aynı konuma yerleştirildi.</span><span class="sxs-lookup"><span data-stu-id="48500-174">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="48500-175">Yapılandırmaya özgü geçersiz kılma dosyalarını kullanarak, hata ayıklama ve yayın derleme yapılandırmaları için farklı yapılandırma ayarları (ortam değişkenleri veya giriş noktaları gibi) belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="48500-175">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="48500-176">Docker Compose Visual Studio 'da çalıştırma seçeneğini göstermek için, Docker projesinin başlangıç projesi olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="48500-176">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="48500-177">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="48500-177">Service Fabric</span></span>

<span data-ttu-id="48500-178">Temel [önkoşullara](#prerequisites)ek olarak, [Service Fabric](/azure/service-fabric/) Orchestration çözümü aşağıdaki önkoşulları ister:</span><span class="sxs-lookup"><span data-stu-id="48500-178">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="48500-179">[MICROSOFT Azure SERVICE fabrıc SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) sürüm 2,6 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="48500-179">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="48500-180">Visual Studio 'nun **Azure geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="48500-180">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="48500-181">Service Fabric, Windows 'da yerel geliştirme kümesinde Linux kapsayıcıları çalıştırmayı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="48500-181">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="48500-182">Proje zaten bir Linux kapsayıcısı kullanıyorsa, Visual Studio Windows kapsayıcılarına geçiş yapmanızı ister.</span><span class="sxs-lookup"><span data-stu-id="48500-182">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="48500-183">Visual Studio kapsayıcı araçları aşağıdaki görevleri yapılır:</span><span class="sxs-lookup"><span data-stu-id="48500-183">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="48500-184">Çözüme bir \* &lt; project_name &gt; uygulaması\* **Service Fabric uygulama** projesi ekler.</span><span class="sxs-lookup"><span data-stu-id="48500-184">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="48500-185">ASP.NET Core projesine bir *dockerfile* ve *. dockerıgnore* dosyası ekler.</span><span class="sxs-lookup"><span data-stu-id="48500-185">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="48500-186">ASP.NET Core projesinde zaten bir *Dockerfile* varsa, *dockerfile. orijinal*olarak yeniden adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="48500-186">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="48500-187">Aşağıdakine benzer yeni bir *Dockerfile*oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="48500-187">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="48500-188">`<IsServiceFabricServiceProject>`ASP.NET Core projenin *. csproj* dosyasına bir öğe ekler:</span><span class="sxs-lookup"><span data-stu-id="48500-188">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="48500-189">ASP.NET Core projesine bir *PackageRoot* klasörü ekler.</span><span class="sxs-lookup"><span data-stu-id="48500-189">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="48500-190">Klasör, yeni hizmet için hizmet bildirimini ve ayarlarını içerir.</span><span class="sxs-lookup"><span data-stu-id="48500-190">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="48500-191">Daha fazla bilgi için bkz. bir [Windows kapsayıcısında .NET uygulamasını Azure Service Fabric dağıtma](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="48500-191">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="48500-192">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="48500-192">Debug</span></span>

<span data-ttu-id="48500-193">Araç çubuğundaki hata ayıklama açılır listesinden **Docker** ' ı seçin ve uygulamada hata ayıklamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="48500-193">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="48500-194">**Çıkış** penceresinin **Docker** görünümü aşağıdaki işlemleri gerçekleşirken gösterir:</span><span class="sxs-lookup"><span data-stu-id="48500-194">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="48500-195">*Microsoft/DotNet* çalışma zamanı görüntüsünün *2,1-aspnetcore-Runtime* etiketi elde edilir (zaten önbellekte değilse).</span><span class="sxs-lookup"><span data-stu-id="48500-195">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="48500-196">Görüntüde ASP.NET Core ve .NET Core çalışma zamanları ile ilişkili Kitaplıklar yüklenir.</span><span class="sxs-lookup"><span data-stu-id="48500-196">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="48500-197">Üretimde ASP.NET Core uygulamaları çalıştırmak için en iyi duruma getirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="48500-197">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="48500-198">`ASPNETCORE_ENVIRONMENT`Ortam değişkeni kapsayıcı içinde olarak ayarlanır `Development` .</span><span class="sxs-lookup"><span data-stu-id="48500-198">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="48500-199">Dinamik olarak atanan iki bağlantı noktası sunulur: bir HTTP ve diğeri HTTPS için.</span><span class="sxs-lookup"><span data-stu-id="48500-199">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="48500-200">Localhost 'a atanan bağlantı noktası `docker ps` komutla sorgulanabilir.</span><span class="sxs-lookup"><span data-stu-id="48500-200">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="48500-201">Uygulama kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="48500-201">The app is copied to the container.</span></span>
* <span data-ttu-id="48500-202">Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktası kullanılarak kapsayıcıya eklenmiş hata ayıklayıcı ile başlatılır.</span><span class="sxs-lookup"><span data-stu-id="48500-202">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="48500-203">Uygulamanın elde edilen Docker görüntüsü *dev*olarak etiketlendi.</span><span class="sxs-lookup"><span data-stu-id="48500-203">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="48500-204">Görüntü, *Microsoft/DotNet* temel görüntüsünün *2,1-aspnetcore-Runtime* etiketine dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="48500-204">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="48500-205">`docker images`Komutunu **Paket Yöneticisi konsolu** (PMC) penceresinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="48500-205">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="48500-206">Makinedeki görüntüler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="48500-206">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="48500-207">*Microsoft/aspnetcore* çalışma zamanı görüntüsü alındı (zaten önbellekte yoksa).</span><span class="sxs-lookup"><span data-stu-id="48500-207">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="48500-208">`ASPNETCORE_ENVIRONMENT`Ortam değişkeni kapsayıcı içinde olarak ayarlanır `Development` .</span><span class="sxs-lookup"><span data-stu-id="48500-208">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="48500-209">80 numaralı bağlantı noktası, localhost için dinamik olarak atanmış bir bağlantı noktasıyla gösterilir ve eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="48500-209">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="48500-210">Bağlantı noktası Docker ana bilgisayarı tarafından belirlenir ve `docker ps` komutla sorgulanabilir.</span><span class="sxs-lookup"><span data-stu-id="48500-210">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="48500-211">Uygulama kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="48500-211">The app is copied to the container.</span></span>
* <span data-ttu-id="48500-212">Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktası kullanılarak kapsayıcıya eklenmiş hata ayıklayıcı ile başlatılır.</span><span class="sxs-lookup"><span data-stu-id="48500-212">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="48500-213">Uygulamanın elde edilen Docker görüntüsü *dev*olarak etiketlendi.</span><span class="sxs-lookup"><span data-stu-id="48500-213">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="48500-214">Görüntü, *Microsoft/aspnetcore* temel görüntüsünü temel alır.</span><span class="sxs-lookup"><span data-stu-id="48500-214">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="48500-215">`docker images`Komutunu **Paket Yöneticisi konsolu** (PMC) penceresinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="48500-215">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="48500-216">Makinedeki görüntüler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="48500-216">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="48500-217">**Hata ayıklama** yapılandırmalarının, yinelemeli deneyim sağlamak için birim bağlama kullanması nedeniyle *geliştirme* görüntüsünde uygulama içeriği eksik.</span><span class="sxs-lookup"><span data-stu-id="48500-217">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="48500-218">Bir görüntüyü göndermek için **yayın** yapılandırmasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="48500-218">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="48500-219">`docker ps`Komutu PMC 'de çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="48500-219">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="48500-220">Uygulamanın, kapsayıcıyı kullanarak çalıştığını unutmayın:</span><span class="sxs-lookup"><span data-stu-id="48500-220">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="48500-221">Düzenle ve devam et</span><span class="sxs-lookup"><span data-stu-id="48500-221">Edit and continue</span></span>

<span data-ttu-id="48500-222">Statik dosya ve görünümlerde yapılan değişiklikler Razor , derleme adımına gerek kalmadan otomatik olarak güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="48500-222">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="48500-223">Güncelleştirmeyi görüntülemek için değişikliği yapıp tarayıcıyı kaydedin ve yenileyin.</span><span class="sxs-lookup"><span data-stu-id="48500-223">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="48500-224">Kod dosyası değişiklikleri derleme gerektirir ve kapsayıcı içinde Kestrel yeniden başlatılır.</span><span class="sxs-lookup"><span data-stu-id="48500-224">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="48500-225">Değişikliği yaptıktan sonra, `CTRL+F5` işlemi gerçekleştirmek ve uygulamayı kapsayıcı içinde başlatmak için öğesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="48500-225">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="48500-226">Docker kapsayıcısı yeniden derlenmez veya durdurulmaz.</span><span class="sxs-lookup"><span data-stu-id="48500-226">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="48500-227">`docker ps`Komutu PMC 'de çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="48500-227">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="48500-228">Özgün kapsayıcının 10 dakikadan önce çalışmaya devam ettiğini fark edin:</span><span class="sxs-lookup"><span data-stu-id="48500-228">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="48500-229">Docker görüntülerini yayımlama</span><span class="sxs-lookup"><span data-stu-id="48500-229">Publish Docker images</span></span>

<span data-ttu-id="48500-230">Uygulamanın geliştirme ve hata ayıklama döngüsünü tamamladıktan sonra, Visual Studio kapsayıcı araçları uygulamanın üretim görüntüsünü oluşturmaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="48500-230">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="48500-231">Yapılandırma açılır öğesini değiştirerek uygulamayı **serbest bırakın** ve oluşturun.</span><span class="sxs-lookup"><span data-stu-id="48500-231">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="48500-232">Araç, Docker Hub 'dan (zaten önbellekte değilse) derleme/yayımlama görüntüsünü alır.</span><span class="sxs-lookup"><span data-stu-id="48500-232">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="48500-233">Bir görüntü, özel kayıt defterine veya Docker Hub 'ına gönderilebilecek *en son* etiketle oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="48500-233">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="48500-234">`docker images`Görüntülerin listesini görmek için bu komutu PMC 'de çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="48500-234">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="48500-235">Aşağıdakine benzer bir çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="48500-235">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="48500-236">`microsoft/aspnetcore-build` `microsoft/aspnetcore` Önceki çıktıda listelenen ve görüntüleri `microsoft/dotnet` .NET Core 2,1 ' deki görüntülerle değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="48500-236">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="48500-237">Daha fazla bilgi için bkz. [Docker depoları geçiş duyurusu](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="48500-237">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="48500-238">`docker images`Komut, havuz adları ve etiketleri *\<none>* (Yukarıda listelenmeyen) ile tanımlanmış olan aracı görüntülerini döndürür.</span><span class="sxs-lookup"><span data-stu-id="48500-238">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="48500-239">Bu adlandırılmamış görüntüler, [çok aşamalı derleme](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *dockerfile*tarafından üretilir.</span><span class="sxs-lookup"><span data-stu-id="48500-239">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="48500-240">Son görüntünün oluşturulması verimliliği artırır &mdash; ancak değişiklikler gerçekleştiğinde yalnızca gerekli katmanlar yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="48500-240">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="48500-241">Ara görüntülere artık gerek kalmadığında [Docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) komutunu kullanarak bunları silin.</span><span class="sxs-lookup"><span data-stu-id="48500-241">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="48500-242">*Geliştirme* görüntüsüne kıyasla üretim veya yayın görüntüsünün boyutunun daha küçük olması için bir beklentisi olabilir.</span><span class="sxs-lookup"><span data-stu-id="48500-242">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="48500-243">Birim eşleme nedeniyle, hata ayıklayıcı ve uygulama, kapsayıcıda değil, yerel makineden çalıştırılıyor.</span><span class="sxs-lookup"><span data-stu-id="48500-243">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="48500-244">*En son* görüntü, uygulamayı bir konak makinesinde çalıştırmak için gerekli uygulama kodunu paketlendi.</span><span class="sxs-lookup"><span data-stu-id="48500-244">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="48500-245">Bu nedenle, Delta uygulama kodunun boyutudur.</span><span class="sxs-lookup"><span data-stu-id="48500-245">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48500-246">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="48500-246">Additional resources</span></span>

* [<span data-ttu-id="48500-247">Visual Studio ile kapsayıcı geliştirme</span><span class="sxs-lookup"><span data-stu-id="48500-247">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="48500-248">Azure Service Fabric: geliştirme ortamınızı hazırlama</span><span class="sxs-lookup"><span data-stu-id="48500-248">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="48500-249">Bir Windows kapsayıcısında .NET uygulamasını Azure 'a dağıtma Service Fabric</span><span class="sxs-lookup"><span data-stu-id="48500-249">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="48500-250">Docker ile Visual Studio geliştirme sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="48500-250">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="48500-251">Visual Studio kapsayıcı araçları GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="48500-251">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
* [<span data-ttu-id="48500-252">Docker ve küçük kapsayıcılar kullanılarak GC</span><span class="sxs-lookup"><span data-stu-id="48500-252">GC using Docker and small containers</span></span>](xref:performance/memory#sc)
