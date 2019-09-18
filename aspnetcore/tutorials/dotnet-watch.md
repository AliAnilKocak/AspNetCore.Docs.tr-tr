---
title: Dosya İzleyicisi kullanarak ASP.NET Core uygulamalar geliştirme
author: rick-anderson
description: Bu öğreticide, .NET Core CLI dosya İzleyicisi (DotNet Watch) aracının bir ASP.NET Core uygulamasında nasıl yükleneceği ve kullanılacağı gösterilmektedir.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: 5462f89a3b5a257ed0a6a8439efb077653fb14f6
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082243"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="b1199-103">Dosya İzleyicisi kullanarak ASP.NET Core uygulamalar geliştirme</span><span class="sxs-lookup"><span data-stu-id="b1199-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="b1199-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="b1199-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="b1199-105">`dotnet watch`, kaynak dosyalar değiştiğinde [.NET Core CLI](/dotnet/core/tools) komutu çalıştıran bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="b1199-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="b1199-106">Örneğin, bir dosya değişikliği derleme, test yürütmesi veya dağıtımı tetikleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b1199-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="b1199-107">Bu öğretici, iki uç nokta ile mevcut bir Web API 'SI kullanır: bir toplamı ve bir ürünü döndüren bir tane döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1199-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="b1199-108">Ürün yönteminde, bu öğreticide düzeltilen bir hata vardır.</span><span class="sxs-lookup"><span data-stu-id="b1199-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="b1199-109">[Örnek uygulamayı](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)indirin.</span><span class="sxs-lookup"><span data-stu-id="b1199-109">Download the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="b1199-110">İki projeden oluşur: *WebApp* (bir ASP.NET Core Web API 'SI) ve *Webapptests* (Web API 'si için birim testleri).</span><span class="sxs-lookup"><span data-stu-id="b1199-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="b1199-111">Bir komut kabuğu 'nda *WebApp* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="b1199-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="b1199-112">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b1199-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="b1199-113">Çalıştırmak için bir proje belirtmek üzere'ikullanabilirsiniz.`dotnet run --project <PROJECT>`</span><span class="sxs-lookup"><span data-stu-id="b1199-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="b1199-114">Örneğin, örnek uygulamanın `dotnet run --project WebApp` kökünden çalıştırıldığında *WebApp* projesi de çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="b1199-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="b1199-115">Konsol çıktısı aşağıdakine benzer iletileri gösterir (uygulamanın çalıştığını ve istekleri beklediğini gösterir):</span><span class="sxs-lookup"><span data-stu-id="b1199-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="b1199-116">Bir Web tarayıcısında öğesine `http://localhost:<port number>/api/math/sum?a=4&b=5`gidin.</span><span class="sxs-lookup"><span data-stu-id="b1199-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="b1199-117">Sonucunu görmeniz gerekir `9`.</span><span class="sxs-lookup"><span data-stu-id="b1199-117">You should see the result of `9`.</span></span>

<span data-ttu-id="b1199-118">Ürün API 'sine (`http://localhost:<port number>/api/math/product?a=4&b=5`) gidin.</span><span class="sxs-lookup"><span data-stu-id="b1199-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="b1199-119">Beklenmez `9`değil `20` , döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1199-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="b1199-120">Bu sorun öğreticide daha sonra düzeltildi.</span><span class="sxs-lookup"><span data-stu-id="b1199-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="b1199-121">Projeye `dotnet watch` ekleme</span><span class="sxs-lookup"><span data-stu-id="b1199-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="b1199-122">`dotnet watch` Dosya İzleyici Aracı, .NET Core SDK sürüm 2.1.300 eklenir.</span><span class="sxs-lookup"><span data-stu-id="b1199-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="b1199-123">.NET Core SDK önceki bir sürümü kullanılırken aşağıdaki adımlar gereklidir.</span><span class="sxs-lookup"><span data-stu-id="b1199-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="b1199-124">`Microsoft.DotNet.Watcher.Tools` *. Csproj* dosyasına bir paket başvurusu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1199-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="b1199-125">Aşağıdaki komutu çalıştırarak paketi yüklemelisiniz: `Microsoft.DotNet.Watcher.Tools`</span><span class="sxs-lookup"><span data-stu-id="b1199-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="b1199-126">Kullanarak .NET Core CLI komutları çalıştırma`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="b1199-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="b1199-127">Tüm [.NET Core CLI komutları](/dotnet/core/tools#cli-commands) ile `dotnet watch`çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1199-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="b1199-128">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b1199-128">For example:</span></span>

| <span data-ttu-id="b1199-129">Komut</span><span class="sxs-lookup"><span data-stu-id="b1199-129">Command</span></span> | <span data-ttu-id="b1199-130">İzle komutu</span><span class="sxs-lookup"><span data-stu-id="b1199-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="b1199-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="b1199-131">dotnet run</span></span> | <span data-ttu-id="b1199-132">DotNet izleme çalıştırması</span><span class="sxs-lookup"><span data-stu-id="b1199-132">dotnet watch run</span></span> |
| <span data-ttu-id="b1199-133">DotNet Run-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="b1199-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="b1199-134">DotNet Watch çalıştırması-f netcoreapp 2.0</span><span class="sxs-lookup"><span data-stu-id="b1199-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="b1199-135">DotNet Run-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="b1199-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="b1199-136">DotNet Watch-f netcoreapp 2.0----arg1</span><span class="sxs-lookup"><span data-stu-id="b1199-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="b1199-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="b1199-137">dotnet test</span></span> | <span data-ttu-id="b1199-138">DotNet izleme testi</span><span class="sxs-lookup"><span data-stu-id="b1199-138">dotnet watch test</span></span> |

<span data-ttu-id="b1199-139">WEBAPP `dotnet watch run` klasöründe çalıştırın .</span><span class="sxs-lookup"><span data-stu-id="b1199-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="b1199-140">Konsol çıktısı başladığını gösterir `watch` .</span><span class="sxs-lookup"><span data-stu-id="b1199-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="b1199-141">İzlenecek projeyi belirtmek `dotnet watch --project <PROJECT>` için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b1199-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="b1199-142">Örneğin, örnek uygulamanın `dotnet watch --project WebApp run` kökünden çalıştırılması Ayrıca *WebApp* projesini de çalıştırır ve bu uygulamayı izleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b1199-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="b1199-143">İle değişiklik yap`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="b1199-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="b1199-144">Çalıştığından emin `dotnet watch` olun.</span><span class="sxs-lookup"><span data-stu-id="b1199-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="b1199-145">`Product` *MathController.cs* yöntemindeki hatayı düzelttikten sonra, ürünü döndürür ve toplamı değil:</span><span class="sxs-lookup"><span data-stu-id="b1199-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="b1199-146">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b1199-146">Save the file.</span></span> <span data-ttu-id="b1199-147">Konsol çıktısı `dotnet watch` bir dosya değişikliği olduğunu algıladı ve uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="b1199-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="b1199-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` doğru sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1199-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="b1199-149">Kullanarak testleri çalıştırma`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="b1199-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="b1199-150">Toplamı döndürmek için *MathController.cs* yönteminigeri`Product` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b1199-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="b1199-151">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b1199-151">Save the file.</span></span>
1. <span data-ttu-id="b1199-152">Bir komut kabuğunda *Webapptests* klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="b1199-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="b1199-153">[DotNet restore](/dotnet/core/tools/dotnet-restore)çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b1199-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="b1199-154">`dotnet watch test`'i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b1199-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="b1199-155">Çıktısı bir testin başarısız olduğunu ve izleyicinin dosya değişikliklerini beklediğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="b1199-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="b1199-156">Yöntem kodunu `Product` , ürünü geri döndürdüğünden düzeltir.</span><span class="sxs-lookup"><span data-stu-id="b1199-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="b1199-157">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b1199-157">Save the file.</span></span>

<span data-ttu-id="b1199-158">`dotnet watch`dosya değişikliğini algılar ve testleri yeniden çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="b1199-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="b1199-159">Konsol çıktısı, geçirilen testleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="b1199-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="b1199-160">Dosya listesini izlemek için Özelleştir</span><span class="sxs-lookup"><span data-stu-id="b1199-160">Customize files list to watch</span></span>

<span data-ttu-id="b1199-161">Varsayılan olarak, `dotnet-watch` aşağıdaki glob desenleriyle eşleşen tüm dosyaları izler:</span><span class="sxs-lookup"><span data-stu-id="b1199-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="b1199-162">*. Csproj* dosyası düzenlenerek, izleme listesine daha fazla öğe eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="b1199-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="b1199-163">Öğeler tek tek veya glob desenleri kullanılarak belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="b1199-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="b1199-164">İzlenen dosyaları kabul etme</span><span class="sxs-lookup"><span data-stu-id="b1199-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="b1199-165">`dotnet-watch`varsayılan ayarlarını yok sayılabilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1199-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="b1199-166">Belirli dosyaları yoksaymak için `Watch="false"` özniteliği *. csproj* dosyasındaki bir öğenin tanımına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1199-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="b1199-167">Özel izleme projeleri</span><span class="sxs-lookup"><span data-stu-id="b1199-167">Custom watch projects</span></span>

<span data-ttu-id="b1199-168">`dotnet-watch`C# projelerle sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="b1199-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="b1199-169">Özel izleme projeleri, farklı senaryoları işlemek için oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="b1199-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="b1199-170">Aşağıdaki proje mizanpajını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="b1199-170">Consider the following project layout:</span></span>

* <span data-ttu-id="b1199-171">**sınamanız**</span><span class="sxs-lookup"><span data-stu-id="b1199-171">**test/**</span></span>
  * <span data-ttu-id="b1199-172">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="b1199-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="b1199-173">*Integrationtests/ıntegrationtests. csproj*</span><span class="sxs-lookup"><span data-stu-id="b1199-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="b1199-174">Hedef her iki projeyi de seyretmek istiyorsanız, her iki projeyi de izlemek için yapılandırılmış özel bir proje dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="b1199-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="b1199-175">Her iki projede de dosya izlemeye başlamak için, *Test* klasörüne geçin.</span><span class="sxs-lookup"><span data-stu-id="b1199-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="b1199-176">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="b1199-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="b1199-177">VSTest, her iki test projesinde herhangi bir dosya değiştiğinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="b1199-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="b1199-178">`dotnet-watch`GitHub 'da</span><span class="sxs-lookup"><span data-stu-id="b1199-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="b1199-179">`dotnet-watch`GitHub [ASPNET/AspNetCore deposunun](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch)bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="b1199-179">`dotnet-watch` is part of the GitHub [aspnet/AspNetCore repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
