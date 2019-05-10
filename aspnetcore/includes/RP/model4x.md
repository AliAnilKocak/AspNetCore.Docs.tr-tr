<a name="scaffold"></a>

### <a name="scaffold-the-movie-model"></a>Film modeli iskelesini

* Komut satırından aşağıdaki komutu çalıştırın (içeren proje dizininde *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları):

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

Hatası alırsanız:

  ```
  No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Proje dizini için bir komut kabuğunu açın (içeren dizine *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları).
