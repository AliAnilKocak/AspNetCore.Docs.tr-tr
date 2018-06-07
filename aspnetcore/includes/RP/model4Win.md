<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="6b593-101">İskele film modeli</span><span class="sxs-lookup"><span data-stu-id="6b593-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="6b593-102">Aşağıdaki komut satırından çalıştırma (içeren proje dizininde *Program.cs*, *haline*, ve *.csproj* dosyaları):</span><span class="sxs-lookup"><span data-stu-id="6b593-102">Run the following from the command line (in the project directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files):</span></span>

  ```console
  dotnet restore
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

<span data-ttu-id="6b593-103">Hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="6b593-103">If you get the error:</span></span>
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

<span data-ttu-id="6b593-104">Yanlış dizinindedir önce gelen hata olur.</span><span class="sxs-lookup"><span data-stu-id="6b593-104">The preceeding error happens when you are in the wrong directory.</span></span> <span data-ttu-id="6b593-105">Proje dizini için bir komut kabuğunu açın (içeren dizine *Program.cs*, *haline*, ve *.csproj* dosyaları), ve ardından önceki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6b593-105">Open a command shell to the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files), and then run the preceeding command.</span></span>

<span data-ttu-id="6b593-106">Hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="6b593-106">If you get the error:</span></span>
  ```
  The process cannot access the file 
 'RazorPagesMovie/bin/Debug/netcoreapp2.0/RazorPagesMovie.dll' 
  because it is being used by another process.
  ```

<span data-ttu-id="6b593-107">Visual Studio çıkın ve komutu yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6b593-107">Exit Visual Studio and run the command again.</span></span>
