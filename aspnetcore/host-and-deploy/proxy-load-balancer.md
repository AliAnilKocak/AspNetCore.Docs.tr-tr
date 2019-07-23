---
title: Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma
author: guardrex
description: Genellikle önemli istek bilgilerini gizleyen ara sunucu ve yük dengeleyiciler arkasında barındırılan uygulamalar için yapılandırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/12/2019
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 4f04e6cae120ee88734855252542e2bfc2f194a0
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "67856166"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="c4c84-103">Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c4c84-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="c4c84-104">[Luke Latham](https://github.com/guardrex) ve [Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="c4c84-104">By [Luke Latham](https://github.com/guardrex) and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="c4c84-105">ASP.NET Core için Önerilen yapılandırmada, uygulama IIS/ASP. NET Core modülü, NGINX veya Apache kullanılarak barındırılır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="c4c84-106">Proxy sunucuları, yük dengeleyiciler ve diğer ağ gereçleri, genellikle istek hakkında uygulamaya ulaşmadan önce bu bilgileri gizlemediğinde:</span><span class="sxs-lookup"><span data-stu-id="c4c84-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="c4c84-107">HTTPS istekleri HTTP üzerinden proxy olduğunda, özgün düzen (HTTPS) kaybolur ve bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="c4c84-108">Bir uygulama, Internet veya şirket ağında gerçek kaynağını değil, ara sunucudan bir istek aldığından, kaynak istemci IP adresinin de bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="c4c84-109">Bu bilgiler, istek işlemede, örneğin yeniden yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konum gibi önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="c4c84-110">İletilen üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="c4c84-110">Forwarded headers</span></span>

<span data-ttu-id="c4c84-111">Kurala göre, proxy 'leri HTTP başlıklarında iletme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="c4c84-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="c4c84-112">Üstbilgi</span><span class="sxs-lookup"><span data-stu-id="c4c84-112">Header</span></span> | <span data-ttu-id="c4c84-113">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c4c84-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="c4c84-114">X-Iletilmiş-Için</span><span class="sxs-lookup"><span data-stu-id="c4c84-114">X-Forwarded-For</span></span> | <span data-ttu-id="c4c84-115">, İsteği başlatan istemciyle ilgili bilgileri ve bir proxy zincirinde sonraki proxy 'leri barındırır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="c4c84-116">Bu parametre IP adresleri (ve isteğe bağlı olarak bağlantı noktası numaraları) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="c4c84-117">Bir ara sunucu zincirinde, ilk parametre isteğin ilk yaptığı istemciyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="c4c84-118">Sonraki proxy tanımlayıcıları izler.</span><span class="sxs-lookup"><span data-stu-id="c4c84-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="c4c84-119">Zincirdeki son proxy, Parametreler listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="c4c84-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="c4c84-120">Son proxy 'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanında uzak IP adresi olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="c4c84-121">X-Iletilen-proto</span><span class="sxs-lookup"><span data-stu-id="c4c84-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="c4c84-122">Kaynak düzenin değeri (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c4c84-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="c4c84-123">Bu değer, istek birden çok proxy 'ye geçen bir düzen listesi de olabilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="c4c84-124">X-Iletilen-konak</span><span class="sxs-lookup"><span data-stu-id="c4c84-124">X-Forwarded-Host</span></span> | <span data-ttu-id="c4c84-125">Ana bilgisayar üst bilgisi alanının özgün değeri.</span><span class="sxs-lookup"><span data-stu-id="c4c84-125">The original value of the Host header field.</span></span> <span data-ttu-id="c4c84-126">Genellikle, proxy 'ler ana bilgisayar üst bilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="c4c84-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="c4c84-127">Proxy 'nin konak üstbilgilerini doğrulamadığı veya kısıtlayamayacağı sistemleri etkileyen ayrıcalık yükselmesi güvenlik açığı hakkında bilgi için bkz. [Microsoft Güvenlik Danışmanlığı CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) .</span><span class="sxs-lookup"><span data-stu-id="c4c84-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="c4c84-128">[Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki Iletilen üstbilgiler ara yazılımı, bu üst bilgileri okur ve ilgili alanları üzerinde <xref:Microsoft.AspNetCore.Http.HttpContext>doldurur.</span><span class="sxs-lookup"><span data-stu-id="c4c84-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="c4c84-129">Ara yazılım güncelleştirmeleri:</span><span class="sxs-lookup"><span data-stu-id="c4c84-129">The middleware updates:</span></span>

* <span data-ttu-id="c4c84-130">`X-Forwarded-For` Üst bilgi değerini kullanarak [HttpContext. Connection. remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; kümesi.</span><span class="sxs-lookup"><span data-stu-id="c4c84-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="c4c84-131">Ek ayarlar, ara yazılımın `RemoteIpAddress`nasıl ayarlanacağını etkiler.</span><span class="sxs-lookup"><span data-stu-id="c4c84-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="c4c84-132">Ayrıntılar için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="c4c84-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="c4c84-133">`X-Forwarded-Proto` Üst bilgi değerini kullanan [HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; kümesi.</span><span class="sxs-lookup"><span data-stu-id="c4c84-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="c4c84-134">`X-Forwarded-Host` Üst bilgi değerini kullanan [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; kümesi.</span><span class="sxs-lookup"><span data-stu-id="c4c84-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="c4c84-135">İletilen üstbilgiler ara yazılımı [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="c4c84-136">Varsayılan ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c4c84-136">The default settings are:</span></span>

* <span data-ttu-id="c4c84-137">Uygulama ve isteklerin kaynağı arasında yalnızca *bir ara sunucu* vardır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="c4c84-138">Bilinen proxy 'ler ve bilinen ağlar için yalnızca geri döngü adresleri yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="c4c84-139">İletilen üstbilgiler ve `X-Forwarded-Proto`olarak adlandırılır `X-Forwarded-For` .</span><span class="sxs-lookup"><span data-stu-id="c4c84-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="c4c84-140">Tüm ağ gereçleri ek yapılandırma olmadan `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini eklemez.</span><span class="sxs-lookup"><span data-stu-id="c4c84-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="c4c84-141">Proxy istekler uygulamaya ulaştığında bu üst bilgileri içermiyorsa, Gereç üreticinizin kılavuzuna başvurun.</span><span class="sxs-lookup"><span data-stu-id="c4c84-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="c4c84-142">Gereç `X-Forwarded-For` , ve `X-Forwarded-Proto`dışındaki farklı üstbilgi adlarını kullanıyorsa, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini gereç tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="c4c84-143">Daha fazla bilgi için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) ve [farklı üstbilgi adları kullanan bir proxy için yapılandırma](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="c4c84-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="c4c84-144">IIS/IIS Express ve ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="c4c84-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="c4c84-145">İletilen üstbilgiler ara yazılımı, uygulama IIS 'nin arkasında ve ASP.NET Core modülünden sonra [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) barındırılıyorsa [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="c4c84-146">İletilen üstbilgiler ara yazılımı, iletilen üst bilgilerle (örneğin, [IP aldatmacası](https://www.iplocation.net/ip-spoofing)) güven sorunları nedeniyle ASP.NET Core modülüne özgü kısıtlanmış bir yapılandırmaya sahip olan ara yazılım ardışık düzeninde ilk olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="c4c84-147">Ara yazılım, `X-Forwarded-For` ve `X-Forwarded-Proto` üst bilgilerini iletmek üzere yapılandırılmıştır ve tek bir localhost proxy ile kısıtlanır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="c4c84-148">Ek yapılandırma gerekliyse, [Iletilen üstbilgiler ara yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c4c84-149">Diğer proxy sunucu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="c4c84-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c4c84-150">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında [IIS tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 'nin kullanılması dışında, iletilen üstbilgiler ara yazılımı varsayılan olarak etkinleştirilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="c4c84-151">İletilen üstbilgileri ile <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>işlemek için bir uygulamanın yönlendirilmiş üst bilgi ara yazılımı etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="c4c84-152">Ara yazılım için Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse, ara yazılımı etkinleştirdikten sonra varsayılan [forwardedheadersoptions. forwardedheaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) , forwardedheaders [. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="c4c84-153">İle <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> ara yazılımı, içindeki `X-Forwarded-For` `X-Forwarded-Proto` ve`Startup.ConfigureServices`üst bilgilerini iletecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c4c84-154">Diğer ara yazılım çağrılmadan `Startup.Configure` önce metodunuçağırın:<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*></span><span class="sxs-lookup"><span data-stu-id="c4c84-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="c4c84-155">Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `Startup.ConfigureServices` veya doğrudan genişletme yöntemine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>belirtilmemişse, iletmek için varsayılan üstbilgiler [forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="c4c84-156"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Özelliğin, iletmek için üst bilgilerle yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="c4c84-157">NGINX yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c4c84-157">Nginx configuration</span></span>

<span data-ttu-id="c4c84-158">`X-Forwarded-For` <xref:host-and-deploy/linux-nginx#configure-nginx>Ve üstbilgileriniiletmekiçinbkz.`X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="c4c84-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="c4c84-159">Daha fazla bilgi için bkz [. NGINX: Iletilen üstbilgi](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="c4c84-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="c4c84-160">Apache yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c4c84-160">Apache configuration</span></span>

<span data-ttu-id="c4c84-161">`X-Forwarded-For`otomatik olarak eklenir (bkz [. Apache Module mod_proxy: Ters proxy Isteği üstbilgileri](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="c4c84-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="c4c84-162">`X-Forwarded-Proto` Üstbilgiyi iletme hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="c4c84-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="c4c84-163">İletilen üstbilgiler ara yazılım seçenekleri</span><span class="sxs-lookup"><span data-stu-id="c4c84-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="c4c84-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen üstbilgiler ara yazılımı davranışını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="c4c84-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="c4c84-165">Aşağıdaki örnek varsayılan değerleri değiştirir:</span><span class="sxs-lookup"><span data-stu-id="c4c84-165">The following example changes the default values:</span></span>

* <span data-ttu-id="c4c84-166">İletilen üst `2`bilgilerdeki giriş sayısını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="c4c84-167">Bilinen bir proxy adresi `127.0.10.1`ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4c84-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="c4c84-168">İletilen üst bilgi adını varsayılan `X-Forwarded-For` değerinden olarak `X-Forwarded-For-My-Custom-Header-Name`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c4c84-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="c4c84-169">Seçenek</span><span class="sxs-lookup"><span data-stu-id="c4c84-169">Option</span></span> | <span data-ttu-id="c4c84-170">Açıklama</span><span class="sxs-lookup"><span data-stu-id="c4c84-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="c4c84-171">Ana bilgisayarları `X-Forwarded-Host` üst bilgiyle belirtilen değerlerle sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="c4c84-172">Değerler, Ordinal-Ignore-Case kullanılarak karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="c4c84-173">Bağlantı noktası numaraları dışlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="c4c84-174">Liste boşsa, tüm konaklara izin verilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="c4c84-175">Üst düzey joker karakter `*` boş olmayan tüm konaklara izin verir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="c4c84-176">Alt etki alanı Joker karakterlere izin verilir ancak kök etki alanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="c4c84-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="c4c84-177">Örneğin, `*.contoso.com` kök etki alanını değil `foo.contoso.com` , alt etki alanıyla `contoso.com`eşleşir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="c4c84-178">Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [puni koduna](https://tools.ietf.org/html/rfc3492) dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="c4c84-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="c4c84-179">[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı ayraçları içermeli ve [geleneksel biçimde](https://tools.ietf.org/html/rfc4291#section-2.2) olmalıdır (örneğin, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="c4c84-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="c4c84-180">IPv6 adresleri, farklı biçimler arasında mantıksal eşitlik denetimi için özel değildir ve hiçbir kurallı kullanım yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="c4c84-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="c4c84-181">İzin verilen konaklar kısıtlanamaması, bir saldırganın hizmet tarafından oluşturulan bağlantıları sızdırmasına izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="c4c84-182">Varsayılan değer boş `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="c4c84-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="c4c84-183">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="c4c84-184">Bu seçenek, proxy/iletici `X-Forwarded-For` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c4c84-185">Varsayılan, `X-Forwarded-For` değeridir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="c4c84-186">Hangi ileticilerin işleneceğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="c4c84-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="c4c84-187">Uygulanan alanların listesi için [Forwardedheaders numaralandırması](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="c4c84-188">Bu özelliğe atanan tipik değerler şunlardır `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="c4c84-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="c4c84-189">Varsayılan değer [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="c4c84-190">[Forwardedheadersdefaults varsayılan. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="c4c84-191">Bu seçenek, proxy/iletici `X-Forwarded-Host` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c4c84-192">Varsayılan, `X-Forwarded-Host` değeridir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="c4c84-193">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="c4c84-194">Bu seçenek, proxy/iletici `X-Forwarded-Proto` üstbilgiyi kullanmıyorsa ve bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="c4c84-195">Varsayılan, `X-Forwarded-Proto` değeridir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="c4c84-196">İşlenen üst bilgilerdeki giriş sayısını sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="c4c84-197">Sınırı devre `null` dışı bırakmak için olarak ayarlayın, ancak bu yalnızca `KnownProxies` veya `KnownNetworks` yapılandırılmışsa yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="c4c84-198">`null` Değer olmayan bir önlem, hatalı yapılandırılmış proxy 'lerde ve ağdaki yan kanallardan gelen kötü amaçlı isteklere karşı koruma sağlamak için bir önlem (garanti değil) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="c4c84-199">İletilen üstbilgiler ara yazılımı, üst bilgileri sağdan sola doğru sırada işler.</span><span class="sxs-lookup"><span data-stu-id="c4c84-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="c4c84-200">Varsayılan değer (`1`) kullanılırsa, `ForwardLimit` değeri artmadığı müddetçe yalnızca üst bilgilerden en sağdaki değer işlenir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="c4c84-201">Varsayılan, `1` değeridir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="c4c84-202">İletilen üstbilgileri kabul etmek için bilinen ağların adres aralıkları.</span><span class="sxs-lookup"><span data-stu-id="c4c84-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="c4c84-203">Sınıfsız Etki alanları arası yönlendirme (CıDR) gösterimini kullanarak IP aralıkları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="c4c84-204">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak `::ffff:10.0.0.1`temsil edilen IPv4 içinde).</span><span class="sxs-lookup"><span data-stu-id="c4c84-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="c4c84-205">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c4c84-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c4c84-206">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="c4c84-207">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="c4c84-208">Varsayılan, için `IList` <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> \< tek`IPAddress.Loopback`bir giriş içeren bir >.</span><span class="sxs-lookup"><span data-stu-id="c4c84-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="c4c84-209">İletilen üstbilgileri kabul etmek için bilinen proxy 'lerin adresleri.</span><span class="sxs-lookup"><span data-stu-id="c4c84-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="c4c84-210">Tam `KnownProxies` IP adresi eşleşmelerini belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="c4c84-211">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak `::ffff:10.0.0.1`temsil edilen IPv4 içinde).</span><span class="sxs-lookup"><span data-stu-id="c4c84-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="c4c84-212">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c4c84-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c4c84-213">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="c4c84-214">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="c4c84-215">Varsayılan, için `IList` <xref:System.Net.IPAddress> \< tek`IPAddress.IPv6Loopback`bir giriş içeren bir >.</span><span class="sxs-lookup"><span data-stu-id="c4c84-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="c4c84-216">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="c4c84-217">Varsayılan, `X-Original-For` değeridir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="c4c84-218">[Forwardedheadersdefaults varsayılan. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="c4c84-219">Varsayılan, `X-Original-Host` değeridir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="c4c84-220">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="c4c84-221">Varsayılan, `X-Original-Proto` değeridir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="c4c84-222">Bilgi işlem sayısının, işlenmekte olan [Forwardedheadersoptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitlenmiş olmasını gerektir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="c4c84-223">ASP.NET Core 1. x `true`içindeki varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="c4c84-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="c4c84-224">ASP.NET Core 2,0 veya sonraki sürümlerde `false`varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="c4c84-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="c4c84-225">Senaryolar ve kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="c4c84-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="c4c84-226">İletilen üstbilgiler eklemek mümkün olmadığında ve tüm istekler güvenlidir</span><span class="sxs-lookup"><span data-stu-id="c4c84-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="c4c84-227">Bazı durumlarda, iletilen üstbilgileri uygulamaya yönelik isteklere eklemek mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="c4c84-228">Proxy tüm genel dış isteklerin https olduğunu zortacaktır, düzen herhangi bir tür ara yazılım kullanılmadan önce içinde `Startup.Configure` el ile ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="c4c84-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="c4c84-229">Bu kod bir geliştirme veya hazırlama ortamındaki ortam değişkeniyle veya diğer yapılandırma ayarıyla devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="c4c84-230">Yol tabanı ve istek yolunu değiştiren proxy 'ler ile uğraşın</span><span class="sxs-lookup"><span data-stu-id="c4c84-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="c4c84-231">Bazı proxy 'ler yolu bozulmadan, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama temel yolu ile geçer.</span><span class="sxs-lookup"><span data-stu-id="c4c84-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="c4c84-232">[Usepathbaseextensions. usepathbase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ara yazılımı, yolu HttpRequest. [Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve uygulama temeli yoluna, [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine böler.</span><span class="sxs-lookup"><span data-stu-id="c4c84-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="c4c84-233">`Request.PathBase` `/foo` `Request.Path` `/api/1` , Olarak `/foo/api/1`geçirilen bir ara sunucu yolu için uygulama temel yolu ise, ara yazılım aşağıdaki komutla ve olarak ayarlanır: `/foo`</span><span class="sxs-lookup"><span data-stu-id="c4c84-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="c4c84-234">Özgün yol ve yol tabanı, ara yazılım ters ' de çağrıldığında yeniden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="c4c84-235">Ara yazılım siparişi işleme hakkında daha fazla bilgi için <xref:fundamentals/middleware/index>bkz.</span><span class="sxs-lookup"><span data-stu-id="c4c84-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="c4c84-236">Proxy, yolu kırpar (örneğin, ' ye `/foo/api/1` `/api/1`iletme), isteğin [pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yeniden yönlendirmeleri ve bağlantıları düzeltir:</span><span class="sxs-lookup"><span data-stu-id="c4c84-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="c4c84-237">Proxy, yol verileri ekliyor ise, kullanarak yeniden yönlendirmeleri ve bağlantıları onarmak üzere yolun bir bölümünü atın ve <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> özelliğini kullanarak yeniden yönlendirmeyi ve bağlantıları onarın:</span><span class="sxs-lookup"><span data-stu-id="c4c84-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="c4c84-238">Farklı üstbilgi adları kullanan bir ara sunucu için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c4c84-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="c4c84-239">Proxy, adlı `X-Forwarded-For` üst bilgileri kullanmıyorsa ve `X-Forwarded-Proto` proxy adresini/bağlantı noktasını ve kaynak <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> düzen bilgilerini iletmek için, ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c4c84-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="c4c84-240">IPv6 adresi olarak temsil edilen IPv4 adresinin yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c4c84-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="c4c84-241">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 'da veya `::ffff:a00:1`olarak `::ffff:10.0.0.1` temsil edilen IPv4).</span><span class="sxs-lookup"><span data-stu-id="c4c84-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="c4c84-242">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="c4c84-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="c4c84-243">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="c4c84-244">Aşağıdaki örnekte, iletilen üstbilgiler sağlayan bir ağ adresi `KnownNetworks` listeye IPv6 biçiminde eklenir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="c4c84-245">IPv4 adresi:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="c4c84-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="c4c84-246">Dönüştürülen IPv6 adresi:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="c4c84-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="c4c84-247">Dönüştürülen önek uzunluğu: 104</span><span class="sxs-lookup"><span data-stu-id="c4c84-247">Converted prefix length: 104</span></span>

<span data-ttu-id="c4c84-248">Adresi onaltılık biçimde (`10.11.12.1` IPv6 olarak `::ffff:0a0b:0c01`temsil edilir) de sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c4c84-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="c4c84-249">Bir IPv4 adresini IPv6 'ya dönüştürürken, ek`8` `::ffff:` IPv6 öneki (8 + 96 = 104) için (örnekteki) CIDR önek uzunluğuna 96 ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4c84-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="c4c84-250">Linux ve IIS olmayan ters proxy 'ler için Düzen iletme</span><span class="sxs-lookup"><span data-stu-id="c4c84-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="c4c84-251">Bir Azure Linux <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> App Service <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> , Azure Linux sanal makinesi (VM) veya IIS 'nin yanı sıra diğer tüm ters proxy 'ler için dağıtıldığında bir siteyi çağıran ve sonsuz döngüye alan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="c4c84-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="c4c84-252">TLS, ters proxy tarafından sonlandırılır ve Kestrel doğru istek düzeninden haberdar değildir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="c4c84-253">OAuth ve OıDC yanlış yeniden yönlendirmeler oluşturduğundan bu yapılandırmada de başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="c4c84-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="c4c84-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS 'nin arkasında çalışırken Iletilen üstbilgiler ara yazılımı ekler ve yapılandırır, ancak Linux için eşleşen otomatik yapılandırma (Apache veya NGINX tümleştirmesi) yoktur.</span><span class="sxs-lookup"><span data-stu-id="c4c84-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="c4c84-255">IIS olmayan senaryolarda düzeni proxy 'den iletmek için, Iletilen üstbilgiler ara yazılımını ekleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="c4c84-256">İçinde `Startup.ConfigureServices`, aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="c4c84-256">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="c4c84-257">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="c4c84-257">Troubleshoot</span></span>

<span data-ttu-id="c4c84-258">Üstbilgiler beklendiği gibi iletilemediği zaman [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c4c84-258">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="c4c84-259">Günlükler sorunu gidermek için yeterli bilgi sağlamıyorsa, sunucu tarafından alınan istek üstbilgilerini numaralandırın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-259">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="c4c84-260">Bir uygulama yanıtına istek üst bilgileri yazmak veya üst bilgileri günlüğe kaydetmek için satır içi ara yazılım kullanın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-260">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="c4c84-261">Üst bilgileri uygulamanın yanıtına yazmak için, aşağıdaki Terminal satır içi ara yazılımını, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> içindeki `Startup.Configure`çağrısından hemen sonra yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="c4c84-261">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="c4c84-262">Yanıt gövdesi yerine günlüklere yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c4c84-262">You can write to logs instead of the response body.</span></span> <span data-ttu-id="c4c84-263">Günlüklere yazmak, sitenin hata ayıklanırken normal şekilde çalışmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-263">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="c4c84-264">Yanıt gövdesi yerine günlükleri yazmak için:</span><span class="sxs-lookup"><span data-stu-id="c4c84-264">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="c4c84-265">[Başlangıçta günlükleri oluşturma](xref:fundamentals/logging/index#create-logs-in-startup)bölümünde açıklandığı gibi `Startup` sınıfına ekleyin.`ILogger<Startup>`</span><span class="sxs-lookup"><span data-stu-id="c4c84-265">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="c4c84-266"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> İçindeki`Startup.Configure`çağrısından hemen sonra aşağıdaki satır içi ara yazılımı yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="c4c84-266">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {METHOD}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {SCHEME}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {PATH}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {KEY}: {VALUE}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {REMOTE_IP_ADDRESS}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="c4c84-267">İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler öğesine `X-Original-{For|Proto|Host}`taşınır.</span><span class="sxs-lookup"><span data-stu-id="c4c84-267">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="c4c84-268">Belirli bir üst bilgide birden çok değer varsa, Iletilen üstbilgiler ara yazılımı sağdan sola doğru sırada üst bilgileri işler.</span><span class="sxs-lookup"><span data-stu-id="c4c84-268">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="c4c84-269">Varsayılan `ForwardLimit` değer ( `1` bir) ise, değeri arttırılmadığı müddetçe yalnızca en `ForwardLimit` sağdaki üst bilgilerden en sağdaki değer işlenir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-269">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="c4c84-270">İletilen üstbilgiler işlenmeden önce isteğin özgün uzak IP 'si `KnownProxies` veya `KnownNetworks` listelerindeki bir girdiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-270">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="c4c84-271">Bu, güvenilmeyen proxy 'lerden ileticileri kabul etmediği için üst bilgi yanıltmasını kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="c4c84-271">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="c4c84-272">Bilinmeyen bir proxy algılandığında günlüğe kaydetme, proxy 'nin adresini gösterir:</span><span class="sxs-lookup"><span data-stu-id="c4c84-272">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="c4c84-273">Yukarıdaki örnekte, alana 10.0.0.100 bir proxy sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="c4c84-273">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="c4c84-274">Sunucu, güvenilir bir ara sunucu ise, sunucusunun IP adresini ' `KnownProxies` `Startup.ConfigureServices`e (veya güvenilen bir ağı öğesine `KnownNetworks`ekleyin) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c4c84-274">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c4c84-275">Daha fazla bilgi için [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-275">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="c4c84-276">Yalnızca güvenilen proxy 'lerin ve ağların üstbilgileri iletmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-276">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="c4c84-277">Aksi takdirde, [IP sahtekarlığı](https://www.iplocation.net/ip-spoofing) saldırıları mümkündür.</span><span class="sxs-lookup"><span data-stu-id="c4c84-277">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="certificate-forwarding"></a><span data-ttu-id="c4c84-278">Sertifika iletme</span><span class="sxs-lookup"><span data-stu-id="c4c84-278">Certificate forwarding</span></span> 

### <a name="on-azure"></a><span data-ttu-id="c4c84-279">Azure 'da</span><span class="sxs-lookup"><span data-stu-id="c4c84-279">On Azure</span></span>

<span data-ttu-id="c4c84-280">Azure Web Apps 'yi yapılandırmak için [Azure belgelerine](/azure/app-service/app-service-web-configure-tls-mutual-auth) bakın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-280">See the [Azure documentation](/azure/app-service/app-service-web-configure-tls-mutual-auth) to configure Azure Web Apps.</span></span> <span data-ttu-id="c4c84-281">Uygulamanızın `Startup.Configure` yönteminde, `app.UseAuthentication();`çağrısının önüne aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4c84-281">In your app's `Startup.Configure` method, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="c4c84-282">Ayrıca, Azure 'un kullandığı üst bilgi adını belirtmek için sertifika Iletme ara yazılımını yapılandırmanız gerekecektir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-282">You'll also need to configure the Certificate Forwarding middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="c4c84-283">Uygulamanızın `Startup.ConfigureServices` yönteminde, ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4c84-283">In your app's `Startup.ConfigureServices` method, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="with-other-web-proxies"></a><span data-ttu-id="c4c84-284">Diğer Web proxy 'leriyle</span><span class="sxs-lookup"><span data-stu-id="c4c84-284">With other web proxies</span></span>

<span data-ttu-id="c4c84-285">IIS veya Azure 'un uygulama Isteği yönlendirme Web Apps olmayan bir ara sunucu kullanıyorsanız, proxy 'nizi bir HTTP üst bilgisinde aldığı sertifikayı iletecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-285">If you're using a proxy that isn't IIS or Azure's Web Apps Application Request Routing, configure your proxy to forward the certificate it received in an HTTP header.</span></span> <span data-ttu-id="c4c84-286">Uygulamanızın `Startup.Configure` yönteminde, `app.UseAuthentication();`çağrısının önüne aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4c84-286">In your app's `Startup.Configure` method, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="c4c84-287">Ayrıca, üst bilgi adını belirtmek için sertifika Iletme ara yazılımını yapılandırmanız gerekecektir.</span><span class="sxs-lookup"><span data-stu-id="c4c84-287">You'll also need to configure the Certificate Forwarding middleware to specify the header name.</span></span> <span data-ttu-id="c4c84-288">Uygulamanızın `Startup.ConfigureServices` yönteminde, ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c4c84-288">In your app's `Startup.ConfigureServices` method, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="c4c84-289">Son olarak, proxy (NGINX ile olduğu gibi) Base64 kodlaması dışında bir şeyi alıyorsa, `HeaderConverter` seçeneğini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4c84-289">Finally, if the proxy is doing something other than base64 encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="c4c84-290">İçinde `Startup.ConfigureServices`aşağıdaki örneği göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="c4c84-290">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="additional-resources"></a><span data-ttu-id="c4c84-291">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c4c84-291">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="c4c84-292">Microsoft Güvenlik Danışma belgesi CVE-2018-0787: ASP.NET Core ayrıcalık yükselmesi güvenlik açığı</span><span class="sxs-lookup"><span data-stu-id="c4c84-292">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)
