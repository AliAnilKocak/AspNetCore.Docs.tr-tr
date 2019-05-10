<a name="scaffold"></a>

### <a name="scaffold-the-movie-model"></a><span data-ttu-id="6d439-101">Film modeli iskelesini</span><span class="sxs-lookup"><span data-stu-id="6d439-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="6d439-102">Komut satırından aşağıdaki komutu çalıştırın (içeren proje dizininde *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları):</span><span class="sxs-lookup"><span data-stu-id="6d439-102">Run the following from the command line (in the project directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files):</span></span>

  ```console
  dotnet restore
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

<span data-ttu-id="6d439-103">Hatası alırsanız:</span><span class="sxs-lookup"><span data-stu-id="6d439-103">If you get the error:</span></span>

  ```
  No executable found matching command "dotnet-aspnet-codegenerator"
  ```

<span data-ttu-id="6d439-104">Önceki hatayı yanlış dizine olduğunda gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="6d439-104">The preceding error happens when you are in the wrong directory.</span></span> <span data-ttu-id="6d439-105">Proje dizini için bir komut kabuğunu açın (içeren dizine *Program.cs*, *Startup.cs*, ve *.csproj* dosyaları), ve ardından yukarıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6d439-105">Open a command shell to the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files), and then run the preceding command.</span></span>

<span data-ttu-id="6d439-106">Hatası alırsanız:</span><span class="sxs-lookup"><span data-stu-id="6d439-106">If you get the error:</span></span>

  ```
  The process cannot access the file
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll'
  because it is being used by another process.
  ```

<span data-ttu-id="6d439-107">Visual Studio çıkın ve komutu yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6d439-107">Exit Visual Studio and run the command again.</span></span>
