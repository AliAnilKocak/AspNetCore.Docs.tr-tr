---
title: ASP.NET Core yerleşik etiket Yardımcıları
author: pkellner
description: ASP.NET Core yerleşik etiket Yardımcıları üretkenliğinizi artırın nasıl çıkışı bulun.
ms.author: riande
ms.date: 09/13/2017
uid: mvc/views/tag-helpers/builtin-th/Index
ms.openlocfilehash: 4f2ebf1600f42847db1c1f9517787b020d2e86c9
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36279174"
---
# <a name="aspnet-core-built-in-tag-helpers"></a><span data-ttu-id="ed383-103">ASP.NET Core yerleşik etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ed383-103">ASP.NET Core built-in Tag Helpers</span></span>

<span data-ttu-id="ed383-104">Tarafından [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="ed383-104">By [Peter Kellner](http://peterkellner.net)</span></span>

<span data-ttu-id="ed383-105">ASP.NET Core birçok yerleşik etiketi verimliliğinizi artırmak için Yardımcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="ed383-105">ASP.NET Core includes many built-in Tag Helpers to boost your productivity.</span></span> <span data-ttu-id="ed383-106">Bu bölümde, yerleşik etiket Yardımcıları genel bir bakış sağlar.</span><span class="sxs-lookup"><span data-stu-id="ed383-106">This section provides an overview of the built-in Tag Helpers.</span></span>

> [!NOTE]
> <span data-ttu-id="ed383-107">Tarafından dahili olarak kullanılan bu yana tartışılan, olmayan yerleşik etiket Yardımcıları vardır [Razor](xref:mvc/views/razor) görünüm altyapısı.</span><span class="sxs-lookup"><span data-stu-id="ed383-107">There are built-in Tag Helpers which aren't discussed, since they're used internally by the [Razor](xref:mvc/views/razor) view engine.</span></span> <span data-ttu-id="ed383-108">Bu etiket Yardımcısı için içerir ~ Web sitesinin kök yolu genişletir karakter.</span><span class="sxs-lookup"><span data-stu-id="ed383-108">This includes a Tag Helper for the ~ character, which expands to the root path of the website.</span></span>

## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="ed383-109">Yerleşik ASP.NET Core etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ed383-109">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="ed383-110">**[Yer işareti etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-110">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span></span>

<span data-ttu-id="ed383-111">**[Önbellek etiket Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-111">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span></span>

<span data-ttu-id="ed383-112">**[Dağıtılmış önbellek etiket Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-112">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span></span>

<span data-ttu-id="ed383-113">**[Ortam etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-113">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span></span>

[comment]: **[FormActionTagHelper](xref:mvc/views/tag-helpers/builtin-th/form-action-tag-helper)**

<span data-ttu-id="ed383-114">**[Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-114">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="ed383-115">**[Görüntü etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-115">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span></span>

<span data-ttu-id="ed383-116">**[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-116">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="ed383-117">**[Etiket etiket Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-117">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

[comment]: **[LinkTagHelper](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**

[comment]: **[OptionTagHelper](xref:mvc/views/tag-helpers/builtin-th/option-tag-helper)**

[comment]: **[ScriptTagHelper](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**

<span data-ttu-id="ed383-118">**[Kısmi etiket Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-118">**[Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span></span>

<span data-ttu-id="ed383-119">**[Etiket Yardımcısı seçin](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-119">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="ed383-120">**[TextArea etiket Yardımcısı](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-120">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="ed383-121">**[Doğrulama ileti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-121">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="ed383-122">**[Doğrulama Özeti etiket Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="ed383-122">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed383-123">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ed383-123">Additional resources</span></span>

* [<span data-ttu-id="ed383-124">İstemci tarafı geliştirme</span><span class="sxs-lookup"><span data-stu-id="ed383-124">Client-side development</span></span>](xref:client-side/index)
* [<span data-ttu-id="ed383-125">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="ed383-125">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
