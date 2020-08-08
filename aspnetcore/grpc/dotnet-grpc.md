---
title: dotnet-grpc ile Protobuf başvurularını yönetme
author: juntaoluo
description: DotNet-GRPC küresel aracıyla prototip başvuruları ekleme, güncelleştirme, kaldırma ve listeleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: d41958d586f54d5944af187933f2b0248f763171
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016135"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="b1c2e-103">dotnet-grpc ile Protobuf başvurularını yönetme</span><span class="sxs-lookup"><span data-stu-id="b1c2e-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="b1c2e-104">[John Luo](https://github.com/juntaoluo) tarafından</span><span class="sxs-lookup"><span data-stu-id="b1c2e-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b1c2e-105">`dotnet-grpc`, bir .NET gRPC projesindeki [prototip (*. proto*)](xref:grpc/basics#proto-file) başvurularını yönetmeye yönelik .NET Core küresel bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="b1c2e-106">Araç, prototipleme başvurularını eklemek, yenilemek, kaldırmak ve listelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="b1c2e-107">Yükleme</span><span class="sxs-lookup"><span data-stu-id="b1c2e-107">Installation</span></span>

<span data-ttu-id="b1c2e-108">`dotnet-grpc` [.NET Core küresel aracını](/dotnet/core/tools/global-tools)yüklemek için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b1c2e-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="b1c2e-109">Başvuru Ekle</span><span class="sxs-lookup"><span data-stu-id="b1c2e-109">Add references</span></span>

<span data-ttu-id="b1c2e-110">`dotnet-grpc`, `<Protobuf />` *. csproj* dosyasına öğe olarak prototip başvuruları eklemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="b1c2e-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="b1c2e-111">Prototip başvuruları, C# istemcisi ve/veya sunucu varlıklarını oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="b1c2e-112">`dotnet-grpc`Araç şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="b1c2e-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="b1c2e-113">Diskteki yerel dosyalardan Prototipsiz başvuru oluştur.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="b1c2e-114">Bir URL ile belirtilen uzak dosyadan Protobir başvuru oluştur.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="b1c2e-115">Projeye doğru gRPC paket bağımlılıklarının eklendiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="b1c2e-116">Örneğin, `Grpc.AspNetCore` paket bir Web uygulamasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="b1c2e-117">`Grpc.AspNetCore`gRPC sunucusu ve istemci kitaplıklarını ve araç desteğini içerir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="b1c2e-118">Alternatif olarak, `Grpc.Net.Client` `Grpc.Tools` ve `Google.Protobuf` yalnızca GRPC istemci kitaplıklarını ve araç desteğini içeren paketleri konsol uygulamasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="b1c2e-119">Dosya ekle</span><span class="sxs-lookup"><span data-stu-id="b1c2e-119">Add file</span></span>

<span data-ttu-id="b1c2e-120">`add-file`Komut, disk üzerindeki yerel dosyaları prototip başvuruları olarak eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="b1c2e-121">Belirtilen dosya yolları:</span><span class="sxs-lookup"><span data-stu-id="b1c2e-121">The file paths provided:</span></span>

* <span data-ttu-id="b1c2e-122">Geçerli dizin veya mutlak yollarla göreli olabilir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="b1c2e-123">, [Glob](https://wikipedia.org/wiki/Glob_(programming))model tabanlı dosya için joker karakterler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="b1c2e-124">Herhangi bir dosya proje dizininin dışındaysa, `Link` dosyayı `Protos` Visual Studio 'daki klasörü altında göstermek için bir öğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="b1c2e-125">Kullanım</span><span class="sxs-lookup"><span data-stu-id="b1c2e-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="b1c2e-126">Arguments</span><span class="sxs-lookup"><span data-stu-id="b1c2e-126">Arguments</span></span>

| <span data-ttu-id="b1c2e-127">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="b1c2e-127">Argument</span></span> | <span data-ttu-id="b1c2e-128">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-128">Description</span></span> |
|-|-|
| <span data-ttu-id="b1c2e-129">files</span><span class="sxs-lookup"><span data-stu-id="b1c2e-129">files</span></span> | <span data-ttu-id="b1c2e-130">Prototip dosyası başvuruları.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-130">The protobuf file references.</span></span> <span data-ttu-id="b1c2e-131">Bunlar yerel prototipli dosyalar için glob 'nin bir yolu olabilir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="b1c2e-132">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b1c2e-132">Options</span></span>

| <span data-ttu-id="b1c2e-133">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-133">Short option</span></span> | <span data-ttu-id="b1c2e-134">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-134">Long option</span></span> | <span data-ttu-id="b1c2e-135">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="b1c2e-136">-p</span><span class="sxs-lookup"><span data-stu-id="b1c2e-136">-p</span></span> | <span data-ttu-id="b1c2e-137">--Proje</span><span class="sxs-lookup"><span data-stu-id="b1c2e-137">--project</span></span> | <span data-ttu-id="b1c2e-138">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-138">The path to the project file to operate on.</span></span> <span data-ttu-id="b1c2e-139">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="b1c2e-140">-s</span><span class="sxs-lookup"><span data-stu-id="b1c2e-140">-s</span></span> | <span data-ttu-id="b1c2e-141">--Hizmetler</span><span class="sxs-lookup"><span data-stu-id="b1c2e-141">--services</span></span> | <span data-ttu-id="b1c2e-142">Oluşturulması gereken gRPC Hizmetleri türü.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="b1c2e-143">`Default`Belirtilmişse, `Both` Web projeleri için kullanılır ve `Client` Web dışı projeler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="b1c2e-144">Kabul edilen değerler,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="b1c2e-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="b1c2e-145">-i</span><span class="sxs-lookup"><span data-stu-id="b1c2e-145">-i</span></span> | <span data-ttu-id="b1c2e-146">--ek-içeri aktarma-Dizin</span><span class="sxs-lookup"><span data-stu-id="b1c2e-146">--additional-import-dirs</span></span> | <span data-ttu-id="b1c2e-147">Prototip dosyaları için içeri aktarmalar çözümlenirken kullanılacak ek dizinler.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="b1c2e-148">Bu, yolların noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="b1c2e-149">--erişim</span><span class="sxs-lookup"><span data-stu-id="b1c2e-149">--access</span></span> | <span data-ttu-id="b1c2e-150">Oluşturulan C# sınıfları için kullanılacak erişim değiştiricisi.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="b1c2e-151">Varsayılan değer: `Public`.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-151">The default value is `Public`.</span></span> <span data-ttu-id="b1c2e-152">Kabul edilen değerler `Internal` şunlardır `Public` .</span><span class="sxs-lookup"><span data-stu-id="b1c2e-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="b1c2e-153">URL Ekle</span><span class="sxs-lookup"><span data-stu-id="b1c2e-153">Add URL</span></span>

<span data-ttu-id="b1c2e-154">`add-url`Komut, kaynak URL tarafından belirtilen bir uzak dosyayı prototipde başvuru olarak eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="b1c2e-155">Uzak dosyanın nereye indirileceği belirtmek için bir dosya yolu belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="b1c2e-156">Dosya yolu, geçerli dizin veya mutlak bir yol ile ilişkili olabilir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="b1c2e-157">Dosya yolu proje dizininin dışındaysa, `Link` dosyayı `Protos` Visual Studio 'daki sanal klasör altında göstermek için bir öğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="b1c2e-158">Kullanım</span><span class="sxs-lookup"><span data-stu-id="b1c2e-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="b1c2e-159">Arguments</span><span class="sxs-lookup"><span data-stu-id="b1c2e-159">Arguments</span></span>

| <span data-ttu-id="b1c2e-160">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="b1c2e-160">Argument</span></span> | <span data-ttu-id="b1c2e-161">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-161">Description</span></span> |
|-|-|
| <span data-ttu-id="b1c2e-162">url</span><span class="sxs-lookup"><span data-stu-id="b1c2e-162">url</span></span> | <span data-ttu-id="b1c2e-163">Uzak protoarabellek dosyasının URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="b1c2e-164">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b1c2e-164">Options</span></span>

| <span data-ttu-id="b1c2e-165">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-165">Short option</span></span> | <span data-ttu-id="b1c2e-166">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-166">Long option</span></span> | <span data-ttu-id="b1c2e-167">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="b1c2e-168">-o</span><span class="sxs-lookup"><span data-stu-id="b1c2e-168">-o</span></span> | <span data-ttu-id="b1c2e-169">--output</span><span class="sxs-lookup"><span data-stu-id="b1c2e-169">--output</span></span> | <span data-ttu-id="b1c2e-170">Uzak protoarabellek dosyası için indirme yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="b1c2e-171">Bu gerekli bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-171">This is a required option.</span></span>
| <span data-ttu-id="b1c2e-172">-p</span><span class="sxs-lookup"><span data-stu-id="b1c2e-172">-p</span></span> | <span data-ttu-id="b1c2e-173">--Proje</span><span class="sxs-lookup"><span data-stu-id="b1c2e-173">--project</span></span> | <span data-ttu-id="b1c2e-174">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-174">The path to the project file to operate on.</span></span> <span data-ttu-id="b1c2e-175">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="b1c2e-176">-s</span><span class="sxs-lookup"><span data-stu-id="b1c2e-176">-s</span></span> | <span data-ttu-id="b1c2e-177">--Hizmetler</span><span class="sxs-lookup"><span data-stu-id="b1c2e-177">--services</span></span> | <span data-ttu-id="b1c2e-178">Oluşturulması gereken gRPC Hizmetleri türü.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="b1c2e-179">`Default`Belirtilmişse, `Both` Web projeleri için kullanılır ve `Client` Web dışı projeler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="b1c2e-180">Kabul edilen değerler,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="b1c2e-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="b1c2e-181">-i</span><span class="sxs-lookup"><span data-stu-id="b1c2e-181">-i</span></span> | <span data-ttu-id="b1c2e-182">--ek-içeri aktarma-Dizin</span><span class="sxs-lookup"><span data-stu-id="b1c2e-182">--additional-import-dirs</span></span> | <span data-ttu-id="b1c2e-183">Prototip dosyaları için içeri aktarmalar çözümlenirken kullanılacak ek dizinler.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="b1c2e-184">Bu, yolların noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="b1c2e-185">--erişim</span><span class="sxs-lookup"><span data-stu-id="b1c2e-185">--access</span></span> | <span data-ttu-id="b1c2e-186">Oluşturulan C# sınıfları için kullanılacak erişim değiştiricisi.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="b1c2e-187">Varsayılan değer `Public` .</span><span class="sxs-lookup"><span data-stu-id="b1c2e-187">Default value is `Public`.</span></span> <span data-ttu-id="b1c2e-188">Kabul edilen değerler `Internal` şunlardır `Public` .</span><span class="sxs-lookup"><span data-stu-id="b1c2e-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="b1c2e-189">Kaldır</span><span class="sxs-lookup"><span data-stu-id="b1c2e-189">Remove</span></span>

<span data-ttu-id="b1c2e-190">`remove`Komut, *. csproj* dosyasından prototipsiz başvuruları kaldırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="b1c2e-191">Komut bağımsız değişken olarak yol bağımsız değişkenlerini ve kaynak URL 'Lerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="b1c2e-192">Araç:</span><span class="sxs-lookup"><span data-stu-id="b1c2e-192">The tool:</span></span>

* <span data-ttu-id="b1c2e-193">Yalnızca Prototipsiz başvuruyu kaldırır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="b1c2e-194">, İlk olarak uzak bir URL 'den indirilse bile, *. proto* dosyasını silmez.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="b1c2e-195">Kullanım</span><span class="sxs-lookup"><span data-stu-id="b1c2e-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="b1c2e-196">Arguments</span><span class="sxs-lookup"><span data-stu-id="b1c2e-196">Arguments</span></span>

| <span data-ttu-id="b1c2e-197">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="b1c2e-197">Argument</span></span> | <span data-ttu-id="b1c2e-198">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-198">Description</span></span> |
|-|-|
| <span data-ttu-id="b1c2e-199">başvurular</span><span class="sxs-lookup"><span data-stu-id="b1c2e-199">references</span></span> | <span data-ttu-id="b1c2e-200">Kaldırılacak prototip başvurularının URL 'Leri veya dosya yolları.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="b1c2e-201">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b1c2e-201">Options</span></span>

| <span data-ttu-id="b1c2e-202">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-202">Short option</span></span> | <span data-ttu-id="b1c2e-203">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-203">Long option</span></span> | <span data-ttu-id="b1c2e-204">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="b1c2e-205">-p</span><span class="sxs-lookup"><span data-stu-id="b1c2e-205">-p</span></span> | <span data-ttu-id="b1c2e-206">--Proje</span><span class="sxs-lookup"><span data-stu-id="b1c2e-206">--project</span></span> | <span data-ttu-id="b1c2e-207">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-207">The path to the project file to operate on.</span></span> <span data-ttu-id="b1c2e-208">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="b1c2e-209">Yenile</span><span class="sxs-lookup"><span data-stu-id="b1c2e-209">Refresh</span></span>

<span data-ttu-id="b1c2e-210">`refresh`Komut, kaynak URL 'den en son içerikle uzak bir başvuruyu güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="b1c2e-211">Yalnızca indirme dosyası yolu ve kaynak URL 'SI, güncellenmek üzere olan başvuruyu belirtmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="b1c2e-212">Not:</span><span class="sxs-lookup"><span data-stu-id="b1c2e-212">Note:</span></span>

* <span data-ttu-id="b1c2e-213">Dosya içeriğinin karmaları yerel dosyanın güncelleştirilip güncelleştirilmediğini belirleme ile karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="b1c2e-214">Zaman damgası bilgisi karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-214">No timestamp information is compared.</span></span>

<span data-ttu-id="b1c2e-215">Bir güncelleştirme gerekiyorsa araç her zaman yerel dosyayı uzak dosya ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="b1c2e-216">Kullanım</span><span class="sxs-lookup"><span data-stu-id="b1c2e-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="b1c2e-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="b1c2e-217">Arguments</span></span>

| <span data-ttu-id="b1c2e-218">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="b1c2e-218">Argument</span></span> | <span data-ttu-id="b1c2e-219">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-219">Description</span></span> |
|-|-|
| <span data-ttu-id="b1c2e-220">başvurular</span><span class="sxs-lookup"><span data-stu-id="b1c2e-220">references</span></span> | <span data-ttu-id="b1c2e-221">Güncellenmesi gereken uzak prototip başvurularına yönelik URL veya dosya yolları.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="b1c2e-222">Tüm Uzak başvuruları yenilemek için bu bağımsız değişkeni boş bırakın.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="b1c2e-223">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b1c2e-223">Options</span></span>

| <span data-ttu-id="b1c2e-224">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-224">Short option</span></span> | <span data-ttu-id="b1c2e-225">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-225">Long option</span></span> | <span data-ttu-id="b1c2e-226">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="b1c2e-227">-p</span><span class="sxs-lookup"><span data-stu-id="b1c2e-227">-p</span></span> | <span data-ttu-id="b1c2e-228">--Proje</span><span class="sxs-lookup"><span data-stu-id="b1c2e-228">--project</span></span> | <span data-ttu-id="b1c2e-229">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-229">The path to the project file to operate on.</span></span> <span data-ttu-id="b1c2e-230">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="b1c2e-231">--Kuru-Çalıştır</span><span class="sxs-lookup"><span data-stu-id="b1c2e-231">--dry-run</span></span> | <span data-ttu-id="b1c2e-232">Herhangi bir yeni içerik indirilmeden güncelleştirilenecek dosyaların listesini verir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="b1c2e-233">Liste</span><span class="sxs-lookup"><span data-stu-id="b1c2e-233">List</span></span>

<span data-ttu-id="b1c2e-234">`list`Komut, proje dosyasındaki tüm prototipsiz başvuruları göstermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="b1c2e-235">Bir sütunun tüm değerleri varsayılan değerler ise, sütun atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="b1c2e-236">Kullanım</span><span class="sxs-lookup"><span data-stu-id="b1c2e-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="b1c2e-237">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b1c2e-237">Options</span></span>

| <span data-ttu-id="b1c2e-238">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-238">Short option</span></span> | <span data-ttu-id="b1c2e-239">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="b1c2e-239">Long option</span></span> | <span data-ttu-id="b1c2e-240">Açıklama</span><span class="sxs-lookup"><span data-stu-id="b1c2e-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="b1c2e-241">-p</span><span class="sxs-lookup"><span data-stu-id="b1c2e-241">-p</span></span> | <span data-ttu-id="b1c2e-242">--Proje</span><span class="sxs-lookup"><span data-stu-id="b1c2e-242">--project</span></span> | <span data-ttu-id="b1c2e-243">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-243">The path to the project file to operate on.</span></span> <span data-ttu-id="b1c2e-244">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="b1c2e-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1c2e-245">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b1c2e-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
