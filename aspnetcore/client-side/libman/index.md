---
title: LibMan ile ASP.NET Core istemci tarafı kitaplık alma
author: scottaddie
description: Kitaplık Yöneticisi 'Ni (LibMan) kullanarak bir ASP.NET Core projesindeki istemci tarafı kitaplık varlıklarını yüklemeyi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/index
ms.openlocfilehash: a9b0f389303709ebf27e6c300ac2489d4e5a86a9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403087"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="7a36a-103">LibMan ile ASP.NET Core istemci tarafı kitaplık alma</span><span class="sxs-lookup"><span data-stu-id="7a36a-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="7a36a-104">[Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="7a36a-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7a36a-105">Kitaplık Yöneticisi (LibMan), basit, istemci tarafı bir kitaplık alma aracıdır.</span><span class="sxs-lookup"><span data-stu-id="7a36a-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="7a36a-106">LibMan, dosya sisteminden veya bir [içerik teslim ağından (CDN)](https://wikipedia.org/wiki/Content_delivery_network)popüler kitaplıkları ve çerçeveleri indirir.</span><span class="sxs-lookup"><span data-stu-id="7a36a-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="7a36a-107">Desteklenen CDNs, [Cdnjs](https://cdnjs.com/), [jsdelivr](https://www.jsdelivr.com/)ve [unpkg](https://unpkg.com/#/)'yi içerir.</span><span class="sxs-lookup"><span data-stu-id="7a36a-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="7a36a-108">Seçilen kitaplık dosyaları getirildi ve ASP.NET Core projesi içindeki uygun konuma yerleştirildi.</span><span class="sxs-lookup"><span data-stu-id="7a36a-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="7a36a-109">LibMan kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="7a36a-109">LibMan use cases</span></span>

<span data-ttu-id="7a36a-110">LibMan aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="7a36a-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="7a36a-111">Yalnızca ihtiyacınız olan kitaplık dosyaları indirilir.</span><span class="sxs-lookup"><span data-stu-id="7a36a-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="7a36a-112">[Node.js](https://nodejs.org), [NPM](https://www.npmjs.com)ve [WebPack](https://webpack.js.org)gibi ek araçlar, bir kitaplıktaki dosyaların bir alt kümesini almak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="7a36a-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="7a36a-113">Dosyalar, derleme görevleri veya el ile dosya kopyalama işlemleri yapılmadan belirli bir konuma yerleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="7a36a-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="7a36a-114">LibMan 'ın avantajları hakkında daha fazla bilgi için [Visual Studio 2017: Libman segmentinde modern ön uç Web geliştirmesini](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)izleyin.</span><span class="sxs-lookup"><span data-stu-id="7a36a-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="7a36a-115">LibMan bir paket yönetim sistemi değildir.</span><span class="sxs-lookup"><span data-stu-id="7a36a-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="7a36a-116">Zaten NPM veya [Yarn](https://yarnpkg.com)gibi bir paket Yöneticisi kullanıyorsanız, bunu yapmaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="7a36a-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="7a36a-117">LibMan, bu araçların yerini alacak şekilde geliştirilmedi.</span><span class="sxs-lookup"><span data-stu-id="7a36a-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a36a-118">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7a36a-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="7a36a-119">LibMan GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="7a36a-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
