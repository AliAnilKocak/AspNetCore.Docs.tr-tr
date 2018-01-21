<a name="scaffold"></a>
### <a name="scaffold-the-movie-model"></a><span data-ttu-id="94fc9-101">İskele film modeli</span><span class="sxs-lookup"><span data-stu-id="94fc9-101">Scaffold the Movie model</span></span>

* <span data-ttu-id="94fc9-102">Aşağıdaki komut satırından çalıştırma (içeren proje dizininde *Program.cs*, *haline*, ve *.csproj* dosyaları):</span><span class="sxs-lookup"><span data-stu-id="94fc9-102">Run the following from the command line (in the project directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files):</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<span data-ttu-id="94fc9-103">Hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="94fc9-103">If you get the error:</span></span>
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

<span data-ttu-id="94fc9-104">Proje dizini için bir komut kabuğunu açın (içeren dizine *Program.cs*, *haline*, ve *.csproj* dosyaları).</span><span class="sxs-lookup"><span data-stu-id="94fc9-104">Open a command shell to the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
