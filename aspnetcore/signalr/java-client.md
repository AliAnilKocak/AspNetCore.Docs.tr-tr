---
title: ASP.NET Core SignalR Java istemcisi
author: mikaelm12
description: ASP.NET Core SignalR Java istemcisinin nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/java-client
ms.openlocfilehash: d7143b2c22ecdc4e68f484aa4c244e1c520beae0
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963791"
---
# <a name="aspnet-core-opno-locsignalr-java-client"></a><span data-ttu-id="c809e-103">ASP.NET Core SignalR Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="c809e-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="c809e-104">X [MIKAEL Mengistu](https://twitter.com/MikaelM_12) tarafından</span><span class="sxs-lookup"><span data-stu-id="c809e-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="c809e-105">Java istemcisi, Android uygulamaları dahil olmak üzere Java kodundan bir ASP.NET Core SignalR sunucusuna bağlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c809e-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="c809e-106">[JavaScript istemcisi](xref:signalr/javascript-client) ve [.NET istemcisi](xref:signalr/dotnet-client)gibi Java istemcisi, bir hub 'a gerçek zamanlı iletiler alıp göndermenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c809e-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="c809e-107">Java istemcisi ASP.NET Core 2,2 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c809e-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="c809e-108">Bu makalede başvurulan örnek Java konsol uygulaması SignalR Java istemcisini kullanır.</span><span class="sxs-lookup"><span data-stu-id="c809e-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="c809e-109">[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c809e-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-java-client-package"></a><span data-ttu-id="c809e-110">SignalR Java istemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="c809e-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="c809e-111">*SignalR-1.0.0* jar dosyası istemcilerin SignalR hub 'lara bağlanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="c809e-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="c809e-112">En son JAR dosyası sürüm numarasını bulmak için bkz. [Maven arama sonuçları](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="c809e-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="c809e-113">Gradle kullanıyorsanız, *Build. Gradle* dosyanızın `dependencies` bölümüne aşağıdaki satırı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c809e-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="c809e-114">Maven kullanıyorsanız, *Pok. xml* dosyanızın `<dependencies>` öğesinin içine aşağıdaki satırları ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c809e-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="c809e-115">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="c809e-115">Connect to a hub</span></span>

<span data-ttu-id="c809e-116">`HubConnection`oluşturmak için `HubConnectionBuilder` kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c809e-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="c809e-117">Hub URL 'SI ve günlük düzeyi bir bağlantı derlenirken yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c809e-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="c809e-118">`build`önce `HubConnectionBuilder` yöntemlerinden herhangi birini çağırarak gerekli seçenekleri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c809e-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="c809e-119">Bağlantıyı `start`başlatın.</span><span class="sxs-lookup"><span data-stu-id="c809e-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="c809e-120">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="c809e-120">Call hub methods from client</span></span>

<span data-ttu-id="c809e-121">Bir `send` çağrısı, hub yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="c809e-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="c809e-122">Hub yöntemi adını ve hub yönteminde tanımlanan tüm bağımsız değişkenleri `send`geçirin.</span><span class="sxs-lookup"><span data-stu-id="c809e-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="c809e-123">Azure SignalR hizmetini *sunucusuz modda*kullanıyorsanız, bir istemciden hub yöntemlerini çağıramezsiniz.</span><span class="sxs-lookup"><span data-stu-id="c809e-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="c809e-124">Daha fazla bilgi için [SignalR hizmeti belgelerine](/azure/azure-signalr/signalr-concept-serverless-development-config)bakın.</span><span class="sxs-lookup"><span data-stu-id="c809e-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="c809e-125">Hub 'dan istemci yöntemlerini çağır</span><span class="sxs-lookup"><span data-stu-id="c809e-125">Call client methods from hub</span></span>

<span data-ttu-id="c809e-126">İstemcide hub 'ın çağırakullanabileceği yöntemleri tanımlamak için `hubConnection.on` kullanın.</span><span class="sxs-lookup"><span data-stu-id="c809e-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="c809e-127">Bağlantıyı başlatmadan önce, oluşturma işleminden sonra yöntemleri tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="c809e-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="c809e-128">Günlüğe kaydetme ekleme</span><span class="sxs-lookup"><span data-stu-id="c809e-128">Add logging</span></span>

<span data-ttu-id="c809e-129">SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c809e-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="c809e-130">Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir.</span><span class="sxs-lookup"><span data-stu-id="c809e-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="c809e-131">Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="c809e-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="c809e-132">Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:</span><span class="sxs-lookup"><span data-stu-id="c809e-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="c809e-133">Bu, güvenle yoksayılabilir.</span><span class="sxs-lookup"><span data-stu-id="c809e-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="c809e-134">Android geliştirme notları</span><span class="sxs-lookup"><span data-stu-id="c809e-134">Android development notes</span></span>

<span data-ttu-id="c809e-135">SignalR istemci özelliklerine Android SDK uyumlulukla ilgili olarak, hedef Android SDK sürümünüzü belirtirken aşağıdaki öğeleri göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="c809e-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="c809e-136">SignalR Java Istemcisi, Android API düzeyi 16 ve sonrasında çalışır.</span><span class="sxs-lookup"><span data-stu-id="c809e-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="c809e-137">Azure [SignalR HIZMETI](/azure/azure-signalr/signalr-overview) TLS 1,2 GEREKTIRDIĞINDEN ve SHA-1 tabanlı şifre paketlerini desteklemediğinden Azure SignalR hizmeti üzerinden bağlanmak IÇIN Android API düzeyi 20 ve üzeri bir sürüm gerekir.</span><span class="sxs-lookup"><span data-stu-id="c809e-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="c809e-138">Android, API düzeyi 20 ' de [SHA-256 (ve üzeri) şifre paketleri için destek ekledi](https://developer.android.com/reference/javax/net/ssl/SSLSocket) .</span><span class="sxs-lookup"><span data-stu-id="c809e-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="c809e-139">Taşıyıcı belirteç kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c809e-139">Configure bearer token authentication</span></span>

<span data-ttu-id="c809e-140">Java istemcisinde SignalR, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a "erişim belirteci fabrikası" sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c809e-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="c809e-141">[Rxjava](https://github.com/ReactiveX/RxJava) [tek bir\<dize >](https://reactivex.io/documentation/single.html)sağlamak Için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın.</span><span class="sxs-lookup"><span data-stu-id="c809e-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="c809e-142">[Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c809e-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="c809e-143">Bilinen sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="c809e-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="c809e-144">Yalnızca JSON Protokolü destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="c809e-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="c809e-145">Taşıma geri dönüşü ve sunucu gönderme olayları aktarımı desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="c809e-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="c809e-146">Yalnızca JSON Protokolü destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="c809e-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="c809e-147">Yalnızca WebSockets taşıması desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c809e-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="c809e-148">Akış henüz desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="c809e-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c809e-149">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c809e-149">Additional resources</span></span>

* [<span data-ttu-id="c809e-150">Java API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="c809e-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* <span data-ttu-id="c809e-151">[Azure SignalR hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="c809e-151">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
