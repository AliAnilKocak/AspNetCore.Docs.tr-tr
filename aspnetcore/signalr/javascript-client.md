---
title: ASP.NET Core SignalR JavaScript istemcisi
author: bradygaster
description: ASP.NET Core SignalR JavaScript istemcisi genel bakış.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/28/2019
uid: signalr/javascript-client
ms.openlocfilehash: f314e1fe0ef0ea73a28b332404a09f2956524132
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412387"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript istemcisi

Tarafından [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript istemci kitaplığı, geliştiricilerin, sunucu taraflı hub kodu çağırmak sağlar.

[Görüntüleme veya indirme örnek kodu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([nasıl indirileceğini](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>SignalR istemci paketini yükle

SignalR JavaScript istemci kitaplığı olarak teslim edilen bir [npm](https://www.npmjs.com/) paket. Visual Studio kullanıyorsanız, çalıştırma `npm install` gelen **Paket Yöneticisi Konsolu** while kök klasöründe. Visual Studio Code için komutu çalıştırın **tümleşik Terminalini**.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

npm paket içeriğini yükler *node_modules\\@microsoft\signalr\dist\browser* klasör. Adlı yeni bir klasör oluşturun *signalr* altında *wwwroot\\LIB* klasör. Kopyalama *signalr.js* dosyasını *wwwroot\lib\signalr* klasör.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm paket içeriğini yükler *node_modules\\@aspnet\signalr\dist\browser* klasör. Adlı yeni bir klasör oluşturun *signalr* altında *wwwroot\\LIB* klasör. Kopyalama *signalr.js* dosyasını *wwwroot\lib\signalr* klasör.

::: moniker-end

## <a name="use-the-signalr-javascript-client"></a>SignalR JavaScript istemcisi kullanma

SignalR JavaScript istemci başvuru `<script>` öğesi.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Bir hub'ına bağlama

Aşağıdaki kod oluşturur ve bir bağlantı başlatır. Hub'ının adı büyük/küçük harfe duyarlıdır.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Çıkış noktaları arası bağlantılar

Genellikle, tarayıcılar istenen sayfa aynı etki alanında bağlantıları yükleyin. Ancak, başka bir etki alanı bağlantı gerekli olduğunda durumlar vardır.

Kötü amaçlı bir siteyi başka bir siteden hassas verileri okumasını önlemek için [çıkış noktaları arası bağlantıları](xref:security/cors) varsayılan olarak devre dışıdır. Çıkış noktaları arası istek izin vermek için bunu etkinleştirin `Startup` sınıfı.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>İstemciden hub yöntemlerini çağırma

JavaScript istemcilerinin şirket hub'ları genel yöntemleri çağırmak [çağırma](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). `invoke` Yöntemi iki bağımsız değişkeni kabul eder:

* Hub yönteminin adı. Aşağıdaki örnekte, yöntem hub'ında addır `SendMessage`.
* Hub yönteminin içinde tanımlanan tüm bağımsız değişkenler. Aşağıdaki örnekte, bağımsız değişkeni addır `message`. Örnek kod, Internet Explorer hariç tüm büyük tarayıcıların güncel sürümlerinde desteklenen ok işlev sözdizimini kullanır.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Azure SignalR hizmetini *sunucusuz modda*kullanıyorsanız, bir istemciden hub yöntemleri çağrılamaz. Daha fazla bilgi için bkz. [SignalR hizmeti belgeleri](/azure/azure-signalr/signalr-concept-serverless-development-config).

Yöntemi bir JavaScript Promise döndürür. [](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) `invoke` , `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir. Sunucu üzerindeki yöntem bir hata `Promise` oluşturursa, hata iletisiyle reddedilir. Bu durumları ( `catch` veya `Promise` `then` söz`await` dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın.

Yöntemi bir JavaScript `Promise`döndürür. `send` `Promise` İleti sunucuya gönderildiğinde çözümlenir. İleti `Promise` gönderilirken bir hata oluşursa, hata iletisiyle reddedilir. Bu durumları ( `catch` veya `Promise` `then` söz`await` dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın.

> [!NOTE]
> Kullanmak `send` sunucu iletiyi almaa kadar beklemez. Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.

## <a name="call-client-methods-from-hub"></a>İstemci hub'ından yöntemleri çağırma

Hub'ından iletiler almak için bir yöntemi kullanarak tanımlarsınız [üzerinde](/javascript/api/%40aspnet/signalr/hubconnection#on) yöntemi `HubConnection`.

* JavaScript istemci yöntemin adı. Aşağıdaki örnekte, yöntem adı olan `ReceiveMessage`.
* Bağımsız değişkenleri hub yöntemine geçirir. Aşağıdaki örnekte, bağımsız değişken değeri olan `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Önceki kodda `connection.on` sunucu tarafı kod kullanarak çağırdığında çalıştıran [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) yöntemi.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR yöntem adını eşleştirerek çağırmak için hangi istemci yöntemini belirler ve bağımsız değişkenler tanımlanan `SendAsync` ve `connection.on`.

> [!NOTE]
> En iyi uygulama, çağrı [Başlat](/javascript/api/%40aspnet/signalr/hubconnection#start) metodunda `HubConnection` sonra `on`. İletileri almadan önce İşleyicileriniz kayıtlı sağlar.

## <a name="error-handling-and-logging"></a>Hata işleme ve günlüğe kaydetme

Zincir bir `catch` sonuna yöntemi `start` istemci tarafı hataları işlemek için yöntemi. Kullanım `console.error` tarayıcının konsola çıktı hataları.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Bağlantı kurulduğunda oturum için bir Günlükçü ve olay türünü geçirerek istemci tarafı günlük izleme ayarlayın. Belirtilen günlük düzeyi ve üzeri, iletileri günlüğe kaydedilir. Kullanılabilir günlük düzeyleri aşağıdaki gibidir:

* `signalR.LogLevel.Error` &ndash; Hata iletileri. Günlükleri `Error` yalnızca iletileri.
* `signalR.LogLevel.Warning` &ndash; Olası hatalar hakkında uyarı iletileri. Günlükleri `Warning`, ve `Error` iletileri.
* `signalR.LogLevel.Information` &ndash; Hatasız durum iletileri. Günlükleri `Information`, `Warning`, ve `Error` iletileri.
* `signalR.LogLevel.Trace` &ndash; İzleme iletileri. Hub ve istemci arasında taşınan veriler dahil her şeyi günlüğe kaydeder.

Kullanım [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) metodunda [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) günlük düzeyi yapılandırmak için. İletileri tarayıcı konsoluna yazılır.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>İstemcileri yeniden

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Otomatik olarak yeniden bağlan

SignalR için JavaScript istemcisi, `withAutomaticReconnect` [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)'daki yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir. Varsayılan olarak otomatik olarak yeniden bağlanmaz.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Herhangi bir parametre olmadan `withAutomaticReconnect()` , her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.

Yeniden bağlanma `HubConnection` girişimlerini başlatmadan önce, duruma geçiş `HubConnectionState.Reconnecting` yapılır ve geri çağırmaları bir `onreconnecting` `Disconnected` `onclose` `HubConnection`durumageçmekyerinegeriçağırmalarıtetikleyerekotomatik yeniden bağlanma olmadan yapılandırılır. Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

İstemci ilk dört deneme `HubConnection` süresi içinde başarıyla yeniden `Connected` bağlanırsa, durum durumuna geçer ve `onreconnected` geri çağırmaları harekete geçirebilir. Bu, kullanıcılara bağlantı yeniden kurulmadığını bildirmek için bir fırsat sağlar.

Bağlantı sunucuya tamamen yeni göründüğünden `connectionId` `onreconnected` geri aramaya yeni bir verilecek.

> [!WARNING]
> Geri aramanın parametresi, [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa tanımsız olur. `HubConnection` `connectionId` `onreconnected`

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`, `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

İstemci ilk dört denemeden `HubConnection` sonra başarıyla yeniden bağlanmazsa, `Disconnected` durumuna geçer ve [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramalarını harekete geçirebilir. Bu, kullanıcılara bağlantının kalıcı olarak kaybedilmediğini bildirme ve sayfayı yenilemeyi önerme olanağı sağlar:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, `withAutomaticReconnect` her bir yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır. Bu, varsayılan yapılandırma için de geçerlidir.

İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.

İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.

Özel davranış daha sonra varsayılan şekilde yeniden bağlantı kurmayı denemek yerine, üçüncü yeniden bağlantı girişimi başarısızlığından sonra varsayılan davranıştan sonra yeniden bir kez daha fazla yeniden bağlantı kurmaya çalışır.

Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `withAutomaticReconnect` adlı `nextRetryDelayInMilliseconds`tek bir yöntemine sahip olan `IRetryPolicy` arabirimini uygulayan nesneyi kabul eder.

`nextRetryDelayInMilliseconds`türünde `RetryContext`tek bir bağımsız değişken alır. `previousRetryCount` ,,`retryReason` `RetryContext` `elapsedMilliseconds` Ve sırasıyla bir`number`olan üç özelliğe sahiptir:.`number` `Error` İlk yeniden bağlanma denemesinden önce, `previousRetryCount` ve `elapsedMilliseconds` sıfır olur ve `retryReason` bağlantının kaybolmasına neden olan hata olacaktır. Her başarısız yeniden deneme denemesinden `previousRetryCount` sonra `elapsedMilliseconds` bir artırılır. Bu, şimdiye kadar geçen sürenin süresini yansıtacak şekilde güncelleştirilir ve `retryReason` en son yeniden bağlanma denemesine neden olan hata olur Neden.

`nextRetryDelayInMilliseconds`sonraki yeniden bağlanma girişiminden önce beklenecek milisaniye sayısını temsil eden bir sayı döndürmelidir veya bunun `null` `HubConnection` yeniden bağlanması durdurulmalıdır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
          if (retryContext.elapsedMilliseconds < 60000) {
            // If we've been reconnecting for less than 60 seconds so far,
            // wait between 0 and 10 seconds before the next reconnect attempt.
            return Math.random() * 10000;
          } else {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
          }
        })
    .build();
```

Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.

::: moniker-end

### <a name="manually-reconnect"></a>El ile yeniden bağlan

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3,0 ' den önce, SignalR için JavaScript istemcisi otomatik olarak yeniden bağlanmaz. İstemcinizi el ile yeniden kod yazmanız gerekir.

::: moniker-end

Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:

1. Bir işlev (Bu durumda, `start` işlevi) bağlantıyı başlatmak için oluşturulur.
1. Çağrı `start` bağlantının işlevinde `onclose` olay işleyicisi.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Gerçek uygulama bir üstel geri alma kullanın veya ancak bir belirtilen sayıda vazgeçmeden önce yeniden deneyin.

## <a name="additional-resources"></a>Ek kaynaklar

* [JavaScript API'si başvurusu](/javascript/api/?view=signalr-js-latest)
* [JavaScript Öğreticisi](xref:tutorials/signalr)
* [Web ve TypeScript Öğreticisi](xref:tutorials/signalr-typescript-webpack)
* [Merkezler](xref:signalr/hubs)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [Azure'a Yayımlama](xref:signalr/publish-to-azure-web-app)
* [Çıkış noktaları arası istekleri (CORS)](xref:security/cors)
* [Azure SignalR hizmeti sunucusuz belgeleri](/azure/azure-signalr/signalr-concept-serverless-development-config)
