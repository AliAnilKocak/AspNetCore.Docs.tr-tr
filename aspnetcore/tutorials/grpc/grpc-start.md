---
title: ASP.NET Core .NET Core gRPC istemcisi ve sunucusu oluşturma
author: juntaoluo
description: Bu öğreticide, ASP.NET Core bir gRPC hizmeti ve gRPC istemcisinin nasıl oluşturulacağı gösterilmektedir. GRPC hizmeti projesi oluşturmayı, Proto dosyasını düzenlemeyi ve çift yönlü akış araması eklemeyi öğrenin.
ms.author: johluo
ms.date: 12/05/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 0cedeb021427455c3f60a8a8cc36b52794a055bc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665825"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Öğretici: ASP.NET Core bir gRPC istemcisi ve sunucusu oluşturma

[John Luo](https://github.com/juntaoluo) tarafından

Bu öğreticide, bir .NET Core [GRPC](https://grpc.io/docs/guides/) istemcisinin ve bir ASP.NET Core GRPC sunucusunun nasıl oluşturulacağı gösterilmektedir.

Sonda, gRPC Greeter hizmeti ile iletişim kuran bir gRPC istemcisine sahip olacaksınız.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * GRPC sunucusu oluşturun.
> * GRPC istemcisi oluşturun.
> * GRPC istemci hizmetini gRPC Greeter hizmeti ile test edin.

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>GRPC hizmeti oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin. Alternatif olarak, Visual Studio **Dosya** menüsünden **Yeni** > **projesi**' ni seçin.
* **Yeni proje oluştur** Iletişim kutusunda **GRPC hizmeti** ' ni seçin ve **İleri**' yi seçin:

  ![Yeni proje iletişim kutusu oluştur](~/tutorials/grpc/grpc-start/static/cnp.png)

* Projeyi **Grpcgreeter**olarak adlandırın. Kodu kopyalayıp yapıştırdığınızda ad alanlarının eşleşmesi için, proje *Grpcgreeter* adında bir ad vermek önemlidir.
* **Oluştur**’u seçin.
* **Yeni bir gRPC hizmeti oluştur** iletişim kutusunda:
  * **GRPC hizmeti** şablonu seçilidir.
  * **Oluştur**’u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri (`cd`), projeyi içerecek bir klasöre değiştirin.
* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new` komutu, *Grpcgreeter* klasöründe yeni bir GRPC hizmeti oluşturur.
  * `code` komutu *Grpcgreeter* klasörünü yeni bir Visual Studio Code örneğinde açar.

  **Gerekli varlıkların derlenmesi ve hata ayıklaması için ' GrpcGreeter ' içinde eksik bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**
* **Evet**’i seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Terminalden aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Yukarıdaki komutlar, gRPC hizmeti oluşturmak için [.NET Core CLI](/dotnet/core/tools/dotnet) kullanır.

### <a name="open-the-project"></a>Projeyi açın

Visual Studio 'da **dosya** > **Aç**' ı seçin ve ardından *grpcgreeter. csproj* dosyasını seçin.

---

### <a name="run-the-service"></a>Hizmeti çalıştırma

  [!INCLUDE[](~/includes/run-the-app.md)]

Günlükler, `https://localhost:5001`üzerinde dinleme hizmetini gösterir.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> GRPC şablonu, [Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)kullanmak üzere yapılandırılmıştır. gRPC istemcilerinin, sunucuyu çağırmak için HTTPS kullanması gerekir.
>
> macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez. MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir. Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

*Grpcgreeter* proje dosyaları:

* *Greet. proto* &ndash; *prototips/Greet. proto* dosyası `Greeter` GRPC 'Yi tanımlar ve GRPC sunucu varlıklarını oluşturmak için kullanılır. Daha fazla bilgi için bkz. [gRPC 'ye giriş](xref:grpc/index).
* *Hizmetler* klasörü: `Greeter` hizmetinin uygulamasını içerir.
* *appSettings. json* &ndash;, Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.
* *Program.cs* &ndash;, GRPC hizmetinin giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.
* *Startup.cs* &ndash; uygulama davranışını yapılandıran kodu içerir. Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Bir .NET konsol uygulamasında gRPC istemcisini oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'nun ikinci bir örneğini açın ve **Yeni proje oluştur**' u seçin.
* **Yeni proje oluştur** Iletişim kutusunda **konsol uygulaması (.NET Core)** öğesini seçin ve **İleri**' yi seçin.
* **Ad** metin kutusuna **Grpcgreeterclient** girin ve **Oluştur**' u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri (`cd`), projeyi içerecek bir klasöre değiştirin.
* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Grpcgreeterclient*adlı bir konsol uygulaması oluşturmak için [Mac için Visual Studio kullanarak MacOS 'ta kapsamlı bir .NET Core çözümü oluşturma](/dotnet/core/tutorials/using-on-mac-vs-full-solution) konusundaki yönergeleri izleyin.

---

### <a name="add-required-packages"></a>Gerekli paketleri Ekle

GRPC istemci projesi aşağıdaki paketleri gerektirir:

* .NET Core istemcisini içeren [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client).
* İçin C#prototipsiz Ileti API 'Leri içeren [Google. protoarabellek](https://www.nuget.org/packages/Google.Protobuf/).
* Prototipleme dosyaları için araç desteğini C# Içeren [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/). Araç, çalışma zamanında gerekli değildir, bu nedenle bağımlılık `PrivateAssets="All"`olarak işaretlenir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet ' i kullanarak paketleri yükler.

#### <a name="pmc-option-to-install-packages"></a>Paket yüklemek için PMC seçeneği

* Visual Studio 'da **araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.
* **Paket Yöneticisi konsol** penceresinde, *Grpcgreeterclient. csproj* dosyalarını içeren klasöre Dizin değiştirmek için `cd GrpcGreeterClient` çalıştırın.
* Aşağıdaki komutları çalıştırın:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Paket yüklemek için NuGet Paketlerini Yönet seçeneği

* **NuGet paketlerini yönetmek** > **Çözüm Gezgini** ' de projeye sağ tıklayın
* **Gözat** sekmesini seçin.
* Arama kutusuna **GRPC .net. Client** girin.
* **Araştır** sekmesinden **GRPC .net. Client** paketini seçin ve ardından **Install**' ı seçin.
* `Google.Protobuf` ve `Grpc.Tools`için yineleyin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Tümleşik terminalden**aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Çözüm Bölmesi** > paket **Ekle** ' de **paketler** klasörüne sağ tıklayın
* Arama kutusuna **GRPC .net. Client** girin.
* Sonuçlar bölmesinden **GRPC .net. Client** paketini seçin ve **paket Ekle** ' yi seçin.
* `Google.Protobuf` ve `Grpc.Tools`için yineleyin.

---

### <a name="add-greetproto"></a>Greet. proto Ekle

* GRPC istemci projesinde bir *Prototips* klasörü oluşturun.
* *Protos\bilgisem. proto* dosyasını GRPC Greeter hizmetinden GRPC istemci projesine kopyalayın.
* *Grpcgreeterclient. csproj* proje dosyasını düzenleyin:

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Projeye sağ tıklayın ve **Proje dosyasını Düzenle**' yi seçin.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  *Grpcgreeterclient. csproj* dosyasını seçin.

  # <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  Projeye sağ tıklayın ve **dosyayı düzenle** > **Araçlar** ' ı seçin.

  ---

* *Greet. proto* dosyasına başvuran `<Protobuf>` öğesi olan bir öğe grubu ekleyin:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Greeter istemcisini oluşturma

`GrpcGreeter` ad alanındaki türleri oluşturmak için projeyi derleyin. `GrpcGreeter` türleri yapı işlemi tarafından otomatik olarak oluşturulur.

GRPC Client *program.cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* , GRPC istemcisinin giriş noktasını ve mantığını içerir.

Greeter istemcisi şu şekilde oluşturulur:

* GRPC hizmetine bağlantı oluşturma bilgilerini içeren bir `GrpcChannel` örnekleniyor.
* Greeter istemcisini oluşturmak için `GrpcChannel` kullanma:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Greeter istemcisi zaman uyumsuz `SayHello` yöntemini çağırır. `SayHello` çağrısının sonucu görüntülenir:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>GRPC istemcisini gRPC Greeter hizmeti ile test etme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Greeter hizmetinde, hata ayıklayıcı olmadan sunucuyu başlatmak için `Ctrl+F5` ' a basın.
* `GrpcGreeterClient` projede, hata ayıklayıcı olmadan istemcisini başlatmak için `Ctrl+F5` ' a basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Greeter hizmetini başlatın.
* İstemcisini başlatın.


# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Greeter hizmetini başlatın.
* İstemcisini başlatın.

---

İstemci, adı *Greeterclient*olan bir iletiyle hizmete bir tebrik gönderir. Hizmet, "Hello GreeterClient" iletisini yanıt olarak gönderir. Komut isteminde "Hello GreeterClient" yanıtı görüntülenir:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

GRPC hizmeti, komut istemine yazılan günlüklerde başarılı çağrının ayrıntılarını kaydeder:

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> Bu makaledeki kod, gRPC hizmetini güvenli hale getirmek için ASP.NET Core HTTPS geliştirme sertifikası gerektirir. İstemci `The remote certificate is invalid according to the validation procedure.`ileti ile başarısız olursa, geliştirme sertifikası güvenilir değildir. Bu sorunu gidermeye yönelik yönergeler için bkz. [Windows ve macOS 'ta ASP.NET Core https geliştirme sertifikasına güvenin](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Sonraki adımlar

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
