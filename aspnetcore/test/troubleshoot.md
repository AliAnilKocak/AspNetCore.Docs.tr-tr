---
title: ASP.NET Core projelerinde sorun giderme ve hata ayıklama
author: Rick-Anderson
description: ASP.NET Core projelerle uyarıları ve hataları anlayın ve sorun giderin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 73a73fb51571e5f7b706ff4b958217854750c1fb
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354706"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>ASP.NET Core projelerinde sorun giderme ve hata ayıklama

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT)

Aşağıdaki bağlantılar sorun giderme kılavuzunu sağlar:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Konferansı (Londra, 2018): ASP.NET Core uygulamalarda sorunları tanılama](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET blogu: sorun giderme ASP.NET Core performans sorunları](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK uyarılar

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>.NET Core SDK hem 32-bit hem de 64-bit sürümleri yüklenir

**Yeni proje** iletişim kutusunda ASP.NET Core için aşağıdaki uyarıyı görebilirsiniz:

> .NET Core SDK hem 32-bit hem de 64-bit sürümleri yüklenir. Yalnızca ' C:\\Program Files\\DotNet\\SDK\\' konumunda yüklü 64 bitlik sürümlerden alınan şablonlar görüntülenir.

Bu uyarı, [.NET Core SDK](https://www.microsoft.com/net/download/all) hem 32-bit (x86) hem de 64-bit (x64) sürümleri yüklendiğinde görüntülenir. Her iki sürümün de sık yüklenebileceği yaygın nedenler şunlardır:

* İlk olarak 32 bitlik bir makine kullanarak .NET Core SDK yükleyicisini indirdiniz, ancak bu dosyayı bir 64 bit makineye kopyaladınız ve bu makinede yüklediniz.
* 32 bit .NET Core SDK başka bir uygulama tarafından yüklendi.
* Yanlış sürüm indirildi ve yüklendi.

Bu uyarıyı engellemek için 32 bit .NET Core SDK kaldırın. **Programlar ve Özellikler '** den kaldırma > **bir programı kaldırma ve değiştirme** ** > .** Uyarının neden oluştuğunu ve etkilerini anladıysanız, uyarıyı yoksayabilirsiniz.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK birden çok konuma yüklendi

**Yeni proje** iletişim kutusunda ASP.NET Core için aşağıdaki uyarıyı görebilirsiniz:

> .NET Core SDK birden çok konuma yüklenir. Yalnızca ' C:\\Program Files\\DotNet\\SDK\\' konumunda yüklü SDK 'lardan şablonlar görüntülenir.

*C:\\Program Files\\dotnet\\SDK\\* dışında bir dizinde en az bir .NET Core SDK yüklemeniz olduğunda bu iletiyi görürsünüz. Genellikle bu, .NET Core SDK MSI yükleyicisi yerine Kopyala/Yapıştır kullanılarak bir makineye dağıtıldığında meydana gelir.

Bu uyarıyı engellemek için tüm 32-bit .NET Core SDK 'larını ve çalışma zamanlarını kaldırın. **Programlar ve Özellikler '** den kaldırma > **bir programı kaldırma ve değiştirme** ** > .** Uyarının neden oluştuğunu ve etkilerini anladıysanız, uyarıyı yoksayabilirsiniz.

### <a name="no-net-core-sdks-were-detected"></a>.NET Core SDK 'Ları algılanmadı

* ASP.NET Core için Visual Studio **Yeni proje** iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:

  > .NET Core SDK 'Ları algılanmadı, `PATH`ortam değişkenine eklendiğinden emin olun.

* Bir `dotnet` komutu yürütürken, uyarı şöyle görünür:

  > Yüklü olan DotNet SDK 'Ları bulmak mümkün değildi.

Bu uyarılar, ortam değişkeni `PATH`, makinedeki herhangi bir .NET Core SDK 'Sı üzerinde işaret olmadığında görüntülenir. Bu sorunu çözmek için:

* .NET Core SDK 'i yükler. [.Net Indirmelerinde](https://dotnet.microsoft.com/download)en son yükleyiciyi edinin.
* `PATH` ortamı değişkeninin SDK 'nın yüklü olduğu konuma işaret ettiğini doğrulayın (64 bit/x64 için`C:\Program Files\dotnet\` veya 32 bit/x86 için `C:\Program Files (x86)\dotnet\`). SDK yükleyicisi normalde `PATH`ayarlar. Aynı bit genişliği SDK 'larını ve çalışma zamanlarını aynı makineye her zaman yükler.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>.NET Core barındırma paketi yüklendikten sonra SDK eksik

.NET Core [barındırma](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) paketini yüklemek, .net core 'un 32-bit (x86) sürümüne işaret etmek üzere .NET Core çalışma zamanını yüklediğinde `PATH` değiştirir (`C:\Program Files (x86)\dotnet\`). Bu, 32-bit (x86) .NET Core `dotnet` komutu kullanıldığında ([.NET Core SDK 'ları algılanmadığında](#no-net-core-sdks-were-detected)) eksik SDK 'lara yol açabilir. Bu sorunu çözmek için `C:\Program Files\dotnet\` `PATH``C:\Program Files (x86)\dotnet\` önce bir konuma taşıyın.

## <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alın

Bir uygulama isteklere yanıt veriyorsa, ara yazılım kullanarak uygulamadan aşağıdaki verileri alabilirsiniz:

* İstek &ndash; yöntemi, şeması, ana bilgisayar, pathbase, yol, sorgu dizesi, üst bilgiler
* Bağlantı &ndash; uzak IP adresi, uzak bağlantı noktası, yerel IP adresi, yerel bağlantı noktası, istemci sertifikası
* Kimlik &ndash; adı, görünen ad
* Yapılandırma ayarları
* Ortam değişkenleri

`Startup.Configure` yönteminin istek işleme ardışık düzeninin başlangıcında aşağıdaki [Ara yazılım](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) kodunu yerleştirin. Bu ortam, kodun yalnızca geliştirme ortamında yürütülmesini sağlamak için, ara yazılım çalıştırılmadan önce denetlenir.

Ortamı edinmek için aşağıdaki yaklaşımlardan birini kullanın:

* `IHostingEnvironment` `Startup.Configure` yöntemine ekleyin ve ortamı yerel değişkenle kontrol edin. Aşağıdaki örnek kodda bu yaklaşım gösterilmektedir.

* Ortamı `Startup` sınıfındaki bir özelliğe atayın. Özelliğini kullanarak ortamı denetleyin (örneğin, `if (Environment.IsDevelopment())`).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>ASP.NET Core uygulamalarda hata ayıkla

Aşağıdaki bağlantılar ASP.NET Core uygulamalarda hata ayıklama hakkında bilgi sağlar.

* [Linux 'ta ASP Core hata ayıklaması](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [SSH üzerinde UNIX üzerinde .NET Core hata ayıklaması](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Hızlı başlangıç: Visual Studio hata ayıklayıcısı ile ASP.NET hata ayıklayın](/visualstudio/debugger/quickstart-debug-aspnet)
* Daha fazla hata ayıklama bilgisi için [Bu GitHub sorununa](https://github.com/aspnet/AspNetCore.Docs/issues/2960) bakın.
