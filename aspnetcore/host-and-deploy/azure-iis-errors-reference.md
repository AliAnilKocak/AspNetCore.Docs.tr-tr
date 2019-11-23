---
title: ASP.NET Core ile Azure App Service ve IIS için ortak hatalar başvurusu
author: guardrex
description: Azure Apps hizmeti ve IIS 'de ASP.NET Core uygulamaları barındırırken sık karşılaşılan hatalara yönelik sorun giderme önerisi alın.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/11/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 047ef23bd2f4d349d2d342d17764c7edd3e0de4a
ms.sourcegitcommit: 4649814d1ae32248419da4e8f8242850fd8679a5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975683"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="70ec0-103">ASP.NET Core ile Azure App Service ve IIS için ortak hatalar başvurusu</span><span class="sxs-lookup"><span data-stu-id="70ec0-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="70ec0-104">Tarafından [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70ec0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="70ec0-105">Bu konuda yaygın hatalar açıklanmakta ve Azure Apps hizmetinde ve IIS 'de ASP.NET Core uygulamalar barındırırken belirli hatalar için sorun giderme önerileri sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="70ec0-105">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="70ec0-106">Genel sorun giderme kılavuzu için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="70ec0-106">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="70ec0-107">Aşağıdaki bilgileri toplayın:</span><span class="sxs-lookup"><span data-stu-id="70ec0-107">Collect the following information:</span></span>

* <span data-ttu-id="70ec0-108">Tarayıcı davranışı (durum kodu ve hata iletisi)</span><span class="sxs-lookup"><span data-stu-id="70ec0-108">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="70ec0-109">Uygulama olay günlüğü girdileri</span><span class="sxs-lookup"><span data-stu-id="70ec0-109">Application Event Log entries</span></span>
  * <span data-ttu-id="70ec0-110">Azure App Service &ndash; <xref:test/troubleshoot-azure-iis>bakın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-110">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="70ec0-111">IIS</span><span class="sxs-lookup"><span data-stu-id="70ec0-111">IIS</span></span>
    1. <span data-ttu-id="70ec0-112">**Windows** menüsünde **Başlat** ' ı seçin, *Olay Görüntüleyicisi*yazın ve **ENTER**tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-112">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="70ec0-113">**Olay Görüntüleyicisi** açıldıktan sonra, kenar çubuğundan **Windows günlükleri** > **uygulaması** ' nı genişletin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-113">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="70ec0-114">ASP.NET Core modülü stdout ve hata ayıklama günlüğü girdileri</span><span class="sxs-lookup"><span data-stu-id="70ec0-114">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="70ec0-115">Azure App Service &ndash; <xref:test/troubleshoot-azure-iis>bakın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-115">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="70ec0-116">IIS &ndash; ASP.NET Core modülü konusunun [günlük oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) ve [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümlerindeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-116">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="70ec0-117">Hata bilgilerini aşağıdaki yaygın hatalarla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-117">Compare error information to the following common errors.</span></span> <span data-ttu-id="70ec0-118">Bir eşleşme bulunursa, sorun giderme talimatını izleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-118">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="70ec0-119">Bu konudaki hataların listesi ayrıntılı değildir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-119">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="70ec0-120">Burada listelenmeyen bir hatayla karşılaşırsanız, bu konunun en altındaki **içerik geri bildirim** düğmesini kullanarak yeni bir sorun açın ve hatayı yeniden oluşturma hakkında ayrıntılı yönergeler kullanın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-120">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="70ec0-121">İşletim sistemi yükseltmesi 32-bit ASP.NET Core modülünü kaldırdı</span><span class="sxs-lookup"><span data-stu-id="70ec0-121">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="70ec0-122">**Uygulama günlüğü:** **C:\windows\system32\inetsrv\aspnetcore.dll** modül dll 'si yüklenemedi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-122">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="70ec0-123">Veriler hatadır.</span><span class="sxs-lookup"><span data-stu-id="70ec0-123">The data is the error.</span></span>

<span data-ttu-id="70ec0-124">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-124">Troubleshooting:</span></span>

<span data-ttu-id="70ec0-125">Bir işletim sistemi yükseltmesi sırasında **C:\Windows\SysWOW64\inetsrv** dizininde işletim sistemi olmayan dosyalar korunmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-125">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="70ec0-126">ASP.NET Core modülü bir işletim sistemi yükseltmesinden önce yüklendiyse ve sonra herhangi bir uygulama havuzu bir işletim sistemi yükseltmesinden sonra 32 bit modda çalıştıktan sonra bu sorunla karşılaşılmıştır.</span><span class="sxs-lookup"><span data-stu-id="70ec0-126">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="70ec0-127">Bir işletim sistemi yükseltmesinden sonra ASP.NET Core modülünü onarın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-127">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="70ec0-128">Bkz. [.NET Core barındırma paketi 'Ni yüklemeyi](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="70ec0-128">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="70ec0-129">Yükleyici çalıştırıldığında **Onar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-129">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="70ec0-130">Eksik site uzantısı, 32-bit (x86) ve 64-bit (x64) site uzantıları yüklü veya yanlış işlem bit genişliği ayarlanmış</span><span class="sxs-lookup"><span data-stu-id="70ec0-130">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="70ec0-131">*Azure Uygulama Hizmetleri tarafından barındırılan uygulamalar için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="70ec0-131">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="70ec0-132">**Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-132">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="70ec0-133">**Uygulama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="70ec0-133">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="70ec0-134">InProcess istek işleyicisi bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-134">Could not find inprocess request handler.</span></span> <span data-ttu-id="70ec0-135">Hostfxr çağırmadan yakalanan çıkış: herhangi bir uyumlu çerçeve sürümü bulmak mümkün değildi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-135">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="70ec0-136">Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-\*' sürümü bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-136">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="70ec0-137">'/LM/W3SVC/1416782824/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8000FFFF '.</span><span class="sxs-lookup"><span data-stu-id="70ec0-137">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="70ec0-138">**ASP.NET Core modülü stdout günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-138">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="70ec0-139">Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-\*' sürümü bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-139">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-140">**ASP.NET Core modülü hata ayıklama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="70ec0-140">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="70ec0-141">Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-141">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="70ec0-142">Başarısız HRESULT döndürüldü: 0x8000FFFF.</span><span class="sxs-lookup"><span data-stu-id="70ec0-142">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="70ec0-143">InProcess istek işleyicisi bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-143">Could not find inprocess request handler.</span></span> <span data-ttu-id="70ec0-144">Uyumlu bir çerçeve sürümü bulmak mümkün değildi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-144">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="70ec0-145">Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-\*' sürümü bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-145">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-146">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-146">Troubleshooting:</span></span>

* <span data-ttu-id="70ec0-147">Uygulamayı bir önizleme çalışma zamanı üzerinde çalıştırıyorsanız, uygulamanın ve uygulamanın çalışma zamanının bit durumuyla eşleşen 32-bit (x86) **veya** 64 bit (x64) site uzantısını da yükler.</span><span class="sxs-lookup"><span data-stu-id="70ec0-147">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="70ec0-148">**Uzantı veya birden çok çalışma zamanı sürümünü yüklemeyin.**</span><span class="sxs-lookup"><span data-stu-id="70ec0-148">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="70ec0-149">ASP.NET Core {RUNTIME VERSION} (x86) çalışma zamanı</span><span class="sxs-lookup"><span data-stu-id="70ec0-149">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="70ec0-150">ASP.NET Core {RUNTIME VERSION} (x64) çalışma zamanı</span><span class="sxs-lookup"><span data-stu-id="70ec0-150">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="70ec0-151">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-151">Restart the app.</span></span> <span data-ttu-id="70ec0-152">Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-152">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="70ec0-153">Uygulamayı bir önizleme çalışma zamanında çalıştırmak ve 32-bit (x86) ve 64 bit (x64) [site uzantıları](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) yüklüyse, uygulamanın bit durumuyla eşleşmeyen site uzantısını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-153">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="70ec0-154">Site uzantısını kaldırdıktan sonra uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-154">After removing the site extension, restart the app.</span></span> <span data-ttu-id="70ec0-155">Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-155">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="70ec0-156">Uygulamayı bir önizleme çalışma zamanında çalıştırmak ve site uzantısının bit kullanımı uygulamayla eşleşiyorsa, önizleme sitesi uzantısının *çalışma zamanı sürümünün* uygulamanın çalışma zamanı sürümüyle eşleştiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-156">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="70ec0-157">**Uygulamanın uygulama ayarlarındaki** **platformunun** uygulamanın bit durumuyla eşleştiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="70ec0-157">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="70ec0-158">Daha fazla bilgi için bkz. <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span><span class="sxs-lookup"><span data-stu-id="70ec0-158">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="70ec0-159">X86 uygulaması dağıtıldı, ancak uygulama havuzu 32-bit uygulamalar için etkinleştirilmemiş</span><span class="sxs-lookup"><span data-stu-id="70ec0-159">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="70ec0-160">**Tarayıcı:** HTTP hatası 500,30-Işlem Içi Işlem başlatma hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-160">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="70ec0-161">**Uygulama günlüğü:** ' {PATH} ' fiziksel köküne sahip '/LM/W3SVC/5/ROOT ' uygulaması beklenmeyen yönetilen özel duruma ulaştı, özel durum kodu = ' 0xe0434352 '.</span><span class="sxs-lookup"><span data-stu-id="70ec0-161">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="70ec0-162">Daha fazla bilgi için lütfen stderr günlüklerine bakın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-162">Please check the stderr logs for more information.</span></span> <span data-ttu-id="70ec0-163">' {PATH} ' fiziksel köküne sahip '/LM/W3SVC/5/ROOT ' uygulaması clr ve yönetilen uygulamayı yükleyemedi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-163">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="70ec0-164">CLR Worker iş parçacığından erken çıkıldı</span><span class="sxs-lookup"><span data-stu-id="70ec0-164">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="70ec0-165">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boştur.</span><span class="sxs-lookup"><span data-stu-id="70ec0-165">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-166">**ASP.NET Core modülü hata ayıklama günlüğü:** Başarısız HRESULT döndürüldü: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="70ec0-166">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="70ec0-167">Bu senaryo, kendi içinde bulunan bir uygulama yayımlanırken SDK tarafından yakalanarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="70ec0-167">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="70ec0-168">RID platform hedefi ile eşleşmezse SDK bir hata üretir (örneğin, proje dosyasında `<PlatformTarget>x86</PlatformTarget>` ile RID `win10-x64`).</span><span class="sxs-lookup"><span data-stu-id="70ec0-168">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="70ec0-169">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-169">Troubleshooting:</span></span>

<span data-ttu-id="70ec0-170">X86 çerçevesine bağımlı bir dağıtım (`<PlatformTarget>x86</PlatformTarget>`) için IIS uygulama havuzunu 32 bitlik uygulamalar için etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-170">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="70ec0-171">IIS Yöneticisi 'nde, uygulama havuzunun **Gelişmiş ayarlarını** açın ve **32 bitlik uygulamaları** **doğru**olarak etkinleştir ayarını yapın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-171">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="70ec0-172">Platform RID ile çakışıyor</span><span class="sxs-lookup"><span data-stu-id="70ec0-172">Platform conflicts with RID</span></span>

* <span data-ttu-id="70ec0-173">**Tarayıcı:** HTTP hatası 502,5-Işlem hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-173">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="70ec0-174">**Uygulama günlüğü:** ' C:\{PATH} ' fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması,\' ' "C:\{PATH} {ASSEMBLY} komut satırı ile işleme başlatamadı. {exe | dll} "', ErrorCode = ' 0x80004005: ff.</span><span class="sxs-lookup"><span data-stu-id="70ec0-174">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="70ec0-175">**ASP.NET Core modülü stdout günlüğü:** İşlenmeyen özel durum: System. BadImageFormatException: ' {ASSEMBLY}. dll ' dosyası veya bütünleştirilmiş kodu yüklenemedi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-175">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="70ec0-176">Bir programı hatalı biçimde yükleme girişiminde bulunuldu.</span><span class="sxs-lookup"><span data-stu-id="70ec0-176">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="70ec0-177">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-177">Troubleshooting:</span></span>

* <span data-ttu-id="70ec0-178">Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-178">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="70ec0-179">İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-179">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="70ec0-180">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="70ec0-180">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="70ec0-181">Bu özel durum, bir uygulamayı yükseltirken ve daha yeni derlemeler dağıtıldığında bir Azure Apps dağıtımı için oluşursa, önceki dağıtımdan tüm dosyaları el ile silin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-181">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="70ec0-182">Kalan uyumsuz derlemeler, yükseltilen bir uygulama dağıtıldığında `System.BadImageFormatException` özel durumuyla sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-182">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="70ec0-183">URI uç noktası yanlış veya durdurulmuş Web sitesi</span><span class="sxs-lookup"><span data-stu-id="70ec0-183">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="70ec0-184">**Tarayıcı:** ERR_CONNECTION_REFUSED **--veya--** bağlantı kurulamıyor</span><span class="sxs-lookup"><span data-stu-id="70ec0-184">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="70ec0-185">**Uygulama günlüğü:** Giriş yok</span><span class="sxs-lookup"><span data-stu-id="70ec0-185">**Application Log:** No entry</span></span>

* <span data-ttu-id="70ec0-186">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-186">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-187">**ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-187">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-188">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-188">Troubleshooting:</span></span>

* <span data-ttu-id="70ec0-189">Uygulamanın kullanımda olduğu doğru URI uç noktasını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-189">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="70ec0-190">Bağlamaları denetleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-190">Check the bindings.</span></span>

* <span data-ttu-id="70ec0-191">IIS Web sitesinin *durdurulmuş* durumda olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-191">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="70ec0-192">CoreWebEngine veya W3SVC sunucu özellikleri devre dışı</span><span class="sxs-lookup"><span data-stu-id="70ec0-192">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="70ec0-193">**Işletim sistemi özel durumu:** ASP.NET Core modülünü kullanmak için IIS 7,0 CoreWebEngine ve W3SVC özelliklerinin yüklü olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-193">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="70ec0-194">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-194">Troubleshooting:</span></span>

<span data-ttu-id="70ec0-195">Uygun rol ve özelliklerin etkinleştirildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-195">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="70ec0-196">Bkz. [IIS yapılandırması](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="70ec0-196">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="70ec0-197">Yanlış web sitesi fiziksel yolu veya uygulaması eksik</span><span class="sxs-lookup"><span data-stu-id="70ec0-197">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="70ec0-198">**Tarayıcı:** 403 Yasak-erişim reddedildi **--veya--** 403,14 yasak-Web sunucusu bu dizinin içeriğini listebir şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="70ec0-198">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="70ec0-199">**Uygulama günlüğü:** Giriş yok</span><span class="sxs-lookup"><span data-stu-id="70ec0-199">**Application Log:** No entry</span></span>

* <span data-ttu-id="70ec0-200">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-200">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-201">**ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-201">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-202">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-202">Troubleshooting:</span></span>

<span data-ttu-id="70ec0-203">IIS Web sitesi **temel ayarları** ve fiziksel uygulama klasörü ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-203">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="70ec0-204">Uygulamanın IIS Web sitesi **fiziksel yolundaki**klasörde olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-204">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="70ec0-205">Yanlış rol, ASP.NET Core modülü yüklü değil veya yanlış izinler</span><span class="sxs-lookup"><span data-stu-id="70ec0-205">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="70ec0-206">**Tarayıcı:** 500,19 Iç sunucu hatası-sayfanın ilgili yapılandırma verileri geçersiz olduğundan istenen sayfaya erişilemiyor.</span><span class="sxs-lookup"><span data-stu-id="70ec0-206">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="70ec0-207">**--Veya--** Bu sayfa görüntülenemiyor</span><span class="sxs-lookup"><span data-stu-id="70ec0-207">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="70ec0-208">**Uygulama günlüğü:** Giriş yok</span><span class="sxs-lookup"><span data-stu-id="70ec0-208">**Application Log:** No entry</span></span>

* <span data-ttu-id="70ec0-209">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-209">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-210">**ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-210">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-211">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-211">Troubleshooting:</span></span>

* <span data-ttu-id="70ec0-212">Doğru rolün etkin olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-212">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="70ec0-213">Bkz. [IIS yapılandırması](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="70ec0-213">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="70ec0-214">**Programlar & Özellikler** veya **uygulamalar & özellikleri** açın ve **Windows Server barındırma** 'nın yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-214">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="70ec0-215">Yüklü programlar listesinde **Windows Server barındırma** yoksa, .NET Core barındırma paketi ' ni indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-215">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="70ec0-216">Geçerli .NET Core barındırma Paket Yükleyici (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="70ec0-216">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="70ec0-217">Daha fazla bilgi için bkz. [.NET Core barındırma paketini yüklemeye](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="70ec0-217">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="70ec0-218">**Uygulama havuzunun** > **Işlem modeli** > **kimliğinin** **applicationpokaydentity** olarak ayarlandığından emin olun veya özel kimliğin uygulamanın dağıtım klasörüne erişmek için doğru izinlere sahip olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="70ec0-218">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="70ec0-219">ASP.NET Core barındırma paketini kaldırdıysanız ve barındırma paketinin önceki bir sürümünü yüklediyseniz *ApplicationHost. config* dosyası ASP.NET Core modülü için bir bölüm içermez.</span><span class="sxs-lookup"><span data-stu-id="70ec0-219">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="70ec0-220">*ApplicationHost. config* dosyasını *% windir%/system32/inetsrv/config* konumunda açın ve `<configuration><configSections><sectionGroup name="system.webServer">` bölüm grubunu bulun.</span><span class="sxs-lookup"><span data-stu-id="70ec0-220">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="70ec0-221">Bölüm grubunda ASP.NET Core modülünün bölümü eksikse, Bölüm öğesini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="70ec0-221">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="70ec0-222">Alternatif olarak, ASP.NET Core barındıran paketin en son sürümünü de yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-222">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="70ec0-223">En son sürüm, desteklenen ASP.NET Core uygulamalarla geriye dönük olarak uyumludur.</span><span class="sxs-lookup"><span data-stu-id="70ec0-223">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="70ec0-224">Hatalı processPath, eksik yol değişkeni, barındırma paketi yüklü değil, sistem/IIS yeniden başlatılmadı, VC + + yeniden dağıtılabilir yüklü değil veya DotNet. exe erişim ihlali</span><span class="sxs-lookup"><span data-stu-id="70ec0-224">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-225">**Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-225">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="70ec0-226">**Uygulama günlüğü:** ' C:\{PATH} ' fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması,\' ' "{...}" komut satırı ile işlem başlatamadı</span><span class="sxs-lookup"><span data-stu-id="70ec0-226">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="70ec0-227">', ErrorCode = ' 0x80070002:0.</span><span class="sxs-lookup"><span data-stu-id="70ec0-227">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="70ec0-228">' {PATH} ' uygulaması başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-228">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="70ec0-229">' {PATH} ' konumunda yürütülebilir dosya bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-229">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="70ec0-230">'/LM/W3SVC/2/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8007023e '.</span><span class="sxs-lookup"><span data-stu-id="70ec0-230">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="70ec0-231">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-231">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="70ec0-232">**ASP.NET Core modülü hata ayıklama günlüğü:** Olay günlüğü: ' {PATH} ' uygulaması başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-232">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="70ec0-233">' {PATH} ' konumunda yürütülebilir dosya bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-233">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="70ec0-234">Başarısız HRESULT döndürüldü: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="70ec0-234">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="70ec0-235">**Tarayıcı:** HTTP hatası 502,5-Işlem hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-235">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="70ec0-236">**Uygulama günlüğü:** ' C:\{PATH} ' fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması,\' ' "{...}" komut satırı ile işlem başlatamadı</span><span class="sxs-lookup"><span data-stu-id="70ec0-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="70ec0-237">', ErrorCode = ' 0x80070002:0.</span><span class="sxs-lookup"><span data-stu-id="70ec0-237">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="70ec0-238">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boştur.</span><span class="sxs-lookup"><span data-stu-id="70ec0-238">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-239">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-239">Troubleshooting:</span></span>

* <span data-ttu-id="70ec0-240">Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-240">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="70ec0-241">İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-241">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="70ec0-242">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="70ec0-242">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="70ec0-243">Çerçeveye bağlı bir dağıtım (FDD) veya kendi kendine dahil olan bir dağıtım için `.\{ASSEMBLY}.exe` `dotnet` olduğunu doğrulamak için *Web. config* 'deki `<aspNetCore>` öğesindeki *processPath* ÖZNITELIĞINI kontrol edin [(SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="70ec0-243">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="70ec0-244">FDD için, *DotNet. exe* ' nin yol ayarları aracılığıyla erişilebilir olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-244">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="70ec0-245">*C:\Program Files\dotnet\\* sistem yolu ayarlarında bulunduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-245">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="70ec0-246">FDD için, *DotNet. exe* ' yi uygulama havuzunun Kullanıcı kimliği için erişilebilir olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-246">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="70ec0-247">Uygulama havuzu Kullanıcı kimliğinin *C:\Program Files\dotnet* dizinine erişimi olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-247">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="70ec0-248">*C:\Program Files\dotnet* ve uygulama dizinlerindeki uygulama havuzu Kullanıcı kimliği için yapılandırılmış reddetme kuralı olmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-248">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="70ec0-249">Bir FDD dağıtılmış ve IIS 'nin yeniden başlatılmasına gerek kalmadan .NET Core yüklenmiş olabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-249">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="70ec0-250">Bir komut isteminden net **stop was/y** ve ardından **net start w3svc** ' i yürüterek sunucuyu YENIDEN başlatın ya da IIS 'yi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-250">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="70ec0-251">Bir FDD, barındırma sistemine .NET Core çalışma zamanı yüklenmeden dağıtılmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-251">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="70ec0-252">.NET Core çalışma zamanı yüklenmemişse, sistemde **.NET Core barındırma paketi yükleyicisini** çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-252">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="70ec0-253">Geçerli .NET Core barındırma Paket Yükleyici (doğrudan indirme)</span><span class="sxs-lookup"><span data-stu-id="70ec0-253">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="70ec0-254">Daha fazla bilgi için bkz. [.NET Core barındırma paketini yüklemeye](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="70ec0-254">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="70ec0-255">Belirli bir çalışma zamanı gerekliyse, [.net download arşivleri](https://dotnet.microsoft.com/download/archives) ' nden çalışma zamanını indirin ve sisteme yükleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-255">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="70ec0-256">Bir komut isteminden net **stop idi** ve ardından **net start w3svc** ' i yürüterek SISTEMI yeniden başlatarak veya IIS 'yi yeniden başlatarak yüklemeyi doldurun.</span><span class="sxs-lookup"><span data-stu-id="70ec0-256">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="70ec0-257">\<aspNetCore > öğesinin bağımsız değişkenleri yanlış</span><span class="sxs-lookup"><span data-stu-id="70ec0-257">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-258">**Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-258">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="70ec0-259">**Uygulama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="70ec0-259">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="70ec0-260">Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-260">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="70ec0-261">InProcess istek işleyicisi bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-261">Could not find inprocess request handler.</span></span> <span data-ttu-id="70ec0-262">Hostfxr çağırmadan yakalanan çıkış: DotNet SDK komutlarını çalıştırmak mı istediniz?</span><span class="sxs-lookup"><span data-stu-id="70ec0-262">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="70ec0-263">Lütfen '/LM/W3SVC/3/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8000FFFF ': https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409.</span><span class="sxs-lookup"><span data-stu-id="70ec0-263">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="70ec0-264">**ASP.NET Core modülü stdout günlüğü:** DotNet SDK komutlarını çalıştırmak mı istediniz?</span><span class="sxs-lookup"><span data-stu-id="70ec0-264">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="70ec0-265">Lütfen ' dan DotNet SDK 'Yı yükledikten sonra: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="70ec0-265">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="70ec0-266">**ASP.NET Core modülü hata ayıklama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="70ec0-266">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="70ec0-267">Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-267">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="70ec0-268">Başarısız HRESULT döndürüldü: 0x8000FFFF, InProcess istek işleyicisi bulamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-268">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="70ec0-269">Hostfxr çağırmadan yakalanan çıkış: DotNet SDK komutlarını çalıştırmak mı istediniz?</span><span class="sxs-lookup"><span data-stu-id="70ec0-269">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="70ec0-270">Lütfen şu kaynaktan DotNet SDK 'Yı yüklersiniz: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 başarısız HRESULT döndürüldü: 0x8000FFFF</span><span class="sxs-lookup"><span data-stu-id="70ec0-270">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="70ec0-271">**Tarayıcı:** HTTP hatası 502,5-Işlem hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-271">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="70ec0-272">**Uygulama günlüğü:** ' C:\{PATH} ' fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması\' ' "DotNet" komut satırı ile işleme başlatılamadı.\{ASSEMBLY}. dll ', ErrorCode = ' 0x80004005:80008081.</span><span class="sxs-lookup"><span data-stu-id="70ec0-272">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="70ec0-273">**ASP.NET Core modülü stdout günlüğü:** Yürütülecek uygulama yok: ' yol\{DERLEMESI}. dll '</span><span class="sxs-lookup"><span data-stu-id="70ec0-273">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="70ec0-274">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-274">Troubleshooting:</span></span>

* <span data-ttu-id="70ec0-275">Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-275">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="70ec0-276">İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-276">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="70ec0-277">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="70ec0-277">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="70ec0-278">Framework 'e bağımlı bir dağıtım (FDD) için `.\{ASSEMBLY}.dll` (a) olduğunu doğrulamak üzere *Web. config* içindeki `<aspNetCore>` öğesindeki *arguments* özniteliğini inceleyin; ya da (b) yok, boş bir dize (`arguments=""`) veya bağımsız bir dağıtım için (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) uygulamanın bağımsız değişkenlerinin bir listesi (SCD).</span><span class="sxs-lookup"><span data-stu-id="70ec0-278">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="70ec0-279">Eksik .NET Core paylaşılan çerçevesi</span><span class="sxs-lookup"><span data-stu-id="70ec0-279">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="70ec0-280">**Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-280">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="70ec0-281">**Uygulama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="70ec0-281">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="70ec0-282">Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="70ec0-282">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="70ec0-283">InProcess istek işleyicisi bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-283">Could not find inprocess request handler.</span></span> <span data-ttu-id="70ec0-284">Hostfxr çağırmadan yakalanan çıkış: herhangi bir uyumlu çerçeve sürümü bulmak mümkün değildi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-284">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="70ec0-285">Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION} ' sürümü bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-285">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="70ec0-286">'/LM/W3SVC/5/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8000FFFF '.</span><span class="sxs-lookup"><span data-stu-id="70ec0-286">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="70ec0-287">**ASP.NET Core modülü stdout günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi.</span><span class="sxs-lookup"><span data-stu-id="70ec0-287">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="70ec0-288">Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION} ' sürümü bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-288">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="70ec0-289">**ASP.NET Core modülü hata ayıklama günlüğü:** Başarısız HRESULT döndürüldü: 0x8000FFFF</span><span class="sxs-lookup"><span data-stu-id="70ec0-289">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="70ec0-290">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-290">Troubleshooting:</span></span>

<span data-ttu-id="70ec0-291">Çerçeveye bağımlı bir dağıtım (FDD) için, sistemde doğru çalışma zamanının yüklü olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-291">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="70ec0-292">Uygulama havuzu durduruldu</span><span class="sxs-lookup"><span data-stu-id="70ec0-292">Stopped Application Pool</span></span>

* <span data-ttu-id="70ec0-293">**Tarayıcı:** 503 Hizmet kullanılamıyor</span><span class="sxs-lookup"><span data-stu-id="70ec0-293">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="70ec0-294">**Uygulama günlüğü:** Giriş yok</span><span class="sxs-lookup"><span data-stu-id="70ec0-294">**Application Log:** No entry</span></span>

* <span data-ttu-id="70ec0-295">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-295">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-296">**ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-296">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-297">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-297">Troubleshooting:</span></span>

<span data-ttu-id="70ec0-298">Uygulama havuzunun *durdurulmuş* durumda olmadığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="70ec0-298">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="70ec0-299">Alt uygulama bir \<işleyicileri içerir > Bölüm</span><span class="sxs-lookup"><span data-stu-id="70ec0-299">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="70ec0-300">**Tarayıcı:** HTTP hatası 500,19-Iç sunucu hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-300">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="70ec0-301">**Uygulama günlüğü:** Giriş yok</span><span class="sxs-lookup"><span data-stu-id="70ec0-301">**Application Log:** No entry</span></span>

* <span data-ttu-id="70ec0-302">**ASP.NET Core modülü stdout günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal işlemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-302">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="70ec0-303">Alt uygulamanın günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-303">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-304">**ASP.NET Core modülü hata ayıklama günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal işlemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-304">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="70ec0-305">Alt uygulamanın günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-305">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-306">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-306">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="70ec0-307">Alt uygulamanın *Web. config* dosyasının `<handlers>` bir bölüm içermediğinden veya alt uygulamanın üst uygulamanın işleyicilerini almadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="70ec0-307">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="70ec0-308">*Web. config* dosyasının üst uygulamanın `<system.webServer>` bölümü bir `<location>` öğesinin içine yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-308">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="70ec0-309"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> özelliği, [\<location >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların üst uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını göstermek için `false` olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="70ec0-309">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="70ec0-310">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="70ec0-310">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="70ec0-311">Alt uygulamanın *Web. config* dosyasının `<handlers>` bir bölüm içermediğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="70ec0-311">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="70ec0-312">stdout günlük yolu yanlış</span><span class="sxs-lookup"><span data-stu-id="70ec0-312">stdout log path incorrect</span></span>

* <span data-ttu-id="70ec0-313">**Tarayıcı:** Uygulama normal olarak yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="70ec0-313">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-314">**Uygulama günlüğü:** C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-314">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="70ec0-315">Özel durum iletisi: {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84 konumunda HRESULT 0x80070005 döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="70ec0-315">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="70ec0-316">C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi durdurulamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-316">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="70ec0-317">Özel durum iletisi: HRESULT 0x80070002 {PATH} konumunda döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="70ec0-317">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="70ec0-318">{PATH} \ aspnetcorev2_inprocess. dll içinde stdout yeniden yönlendirmesi başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-318">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="70ec0-319">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-319">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="70ec0-320">**ASP.NET Core modülü hata ayıklama günlüğü:** C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-320">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="70ec0-321">Özel durum iletisi: {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84 konumunda HRESULT 0x80070005 döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="70ec0-321">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="70ec0-322">C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi durdurulamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-322">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="70ec0-323">Özel durum iletisi: HRESULT 0x80070002 {PATH} konumunda döndürüldü.</span><span class="sxs-lookup"><span data-stu-id="70ec0-323">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="70ec0-324">{PATH} \ aspnetcorev2_inprocess. dll içinde stdout yeniden yönlendirmesi başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-324">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="70ec0-325">**Uygulama günlüğü:** Uyarı: stdoutLogFile \\oluşturulamadı?\{yol} \ path_doesnt_exist \ stdout_ {Işlem KIMLIĞI} _ {zaman DAMGASı}. günlük, hata kodu =-2147024893.</span><span class="sxs-lookup"><span data-stu-id="70ec0-325">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="70ec0-326">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="70ec0-326">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-327">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-327">Troubleshooting:</span></span>

* <span data-ttu-id="70ec0-328">*Web. config* dosyasının `<aspNetCore>` öğesinde belirtilen `stdoutLogFile` yolu yok.</span><span class="sxs-lookup"><span data-stu-id="70ec0-328">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="70ec0-329">Daha fazla bilgi için bkz. [ASP.NET Core modülü: günlük oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="70ec0-329">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="70ec0-330">Uygulama havuzu kullanıcısının stdout günlük yoluna yazma erişimi yok.</span><span class="sxs-lookup"><span data-stu-id="70ec0-330">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="70ec0-331">Uygulama yapılandırması genel sorunu</span><span class="sxs-lookup"><span data-stu-id="70ec0-331">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="70ec0-332">**Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası **--veya--** HTTP hatası 500,30-Ancm Işlem Içi başlatma hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-332">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="70ec0-333">**Uygulama günlüğü:** Değişken</span><span class="sxs-lookup"><span data-stu-id="70ec0-333">**Application Log:** Variable</span></span>

* <span data-ttu-id="70ec0-334">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boş veya, uygulamanın noktası başarısız olana kadar normal girdilerle oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="70ec0-334">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="70ec0-335">**ASP.NET Core modülü hata ayıklama günlüğü:** Değişken</span><span class="sxs-lookup"><span data-stu-id="70ec0-335">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="70ec0-336">**Tarayıcı:** HTTP hatası 502,5-Işlem hatası</span><span class="sxs-lookup"><span data-stu-id="70ec0-336">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="70ec0-337">**Uygulama günlüğü:** ' C:\{PATH} fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması\' ' "C:\{PATH}\{ASSEMBLY} komut satırı ile oluşturulan işlem oluşturdu. {exe | dll} "', ancak belirtilen ' {PORT} ' bağlantı noktasında kilitlenen veya yanıt vermeyen ya da bu bağlantı noktası üzerinde dinleme yapamadı, ErrorCode = ' {ERROR CODE} '</span><span class="sxs-lookup"><span data-stu-id="70ec0-337">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="70ec0-338">**ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boştur.</span><span class="sxs-lookup"><span data-stu-id="70ec0-338">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="70ec0-339">Sorun Giderme:</span><span class="sxs-lookup"><span data-stu-id="70ec0-339">Troubleshooting:</span></span>

<span data-ttu-id="70ec0-340">Büyük olasılıkla uygulama yapılandırması veya programlama sorunu nedeniyle işlem başlatılamadı.</span><span class="sxs-lookup"><span data-stu-id="70ec0-340">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="70ec0-341">Daha fazla bilgi için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="70ec0-341">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>
