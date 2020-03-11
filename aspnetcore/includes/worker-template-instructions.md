# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Yeni bir proje oluşturma.
1. **Çalışan hizmeti**seçin. **İleri**’yi seçin.
1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. **Oluştur**’u seçin.
1. **Yeni çalışan hizmeti oluştur** Iletişim kutusunda **Oluştur**' u seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Yeni bir proje oluşturma.
1. Kenar çubuğunda **.NET Core** altında **uygulama** ' yı seçin.
1. **ASP.NET Core**altında **çalışan** ' ı seçin. **İleri**’yi seçin.
1. **Hedef çerçeve**Için **.NET Core 3,0** veya üzerini seçin. **İleri**’yi seçin.
1. **Proje adı** alanına bir ad girin. **Oluştur**’u seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Bir komut kabuğundan [DotNet New](/dotnet/core/tools/dotnet-new) komutuyla çalışan hizmeti (`worker`) şablonunu kullanın. Aşağıdaki örnekte, `ContosoWorker`adlı bir çalışan hizmeti uygulaması oluşturulur. Komut yürütüldüğünde `ContosoWorker` uygulamasının bir klasörü otomatik olarak oluşturulur.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
