---
title: ASP.NET Core için Razor söz dizimi başvurusu
author: rick-anderson
description: Web sayfalarının sunucu tabanlı kod katıştırma Razor biçimlendirme söz dizimi hakkında bilgi edinin.
manager: wpickett
ms.author: riande
ms.date: 10/18/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/views/razor
ms.openlocfilehash: 224c855b355b8ecde36377bba6966edec251af6a
ms.sourcegitcommit: 74be78285ea88772e7dad112f80146b6ed00e53e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/10/2018
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="5ade2-103">ASP.NET Core için Razor söz dizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="5ade2-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="5ade2-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), ve [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="5ade2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="5ade2-105">Razor kod sunucu tabanlı Web sayfalarının katıştırma için işaretleme sözdizimi aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="5ade2-106">Razor sözdizimi Razor biçimlendirme, C# ve HTML oluşur.</span><span class="sxs-lookup"><span data-stu-id="5ade2-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="5ade2-107">Razor genellikle içeren dosyalarınız bir *.cshtml* dosya uzantısı.</span><span class="sxs-lookup"><span data-stu-id="5ade2-107">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="5ade2-108">HTML işleme</span><span class="sxs-lookup"><span data-stu-id="5ade2-108">Rendering HTML</span></span>

<span data-ttu-id="5ade2-109">Varsayılan Razor HTML dilidir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-109">The default Razor language is HTML.</span></span> <span data-ttu-id="5ade2-110">Razor biçimlendirme işleme HTML bir HTML dosyası HTML işleme daha farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-110">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="5ade2-111">HTML biçimlendirmede *.cshtml* Razor dosyaları değiştirmeden sunucu tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-111">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="5ade2-112">Razor sözdizimi</span><span class="sxs-lookup"><span data-stu-id="5ade2-112">Razor syntax</span></span>

<span data-ttu-id="5ade2-113">Razor C# destekler ve kullandığı `@` C# HTML geçiş simgesini.</span><span class="sxs-lookup"><span data-stu-id="5ade2-113">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="5ade2-114">Razor C# ifadeleri değerlendirir ve HTML çıkışında işler.</span><span class="sxs-lookup"><span data-stu-id="5ade2-114">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="5ade2-115">Zaman bir `@` simgesi tarafından izlenen bir [Razor ayrılmış anahtar sözcüğü](#razor-reserved-keywords), Razor özgü biçimlendirme geçiş.</span><span class="sxs-lookup"><span data-stu-id="5ade2-115">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="5ade2-116">Aksi takdirde, düz C# diline geçiş.</span><span class="sxs-lookup"><span data-stu-id="5ade2-116">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="5ade2-117">Çıkış için bir `@` sembol Razor biçimlendirme içinde ikinci bir `@` simgesi:</span><span class="sxs-lookup"><span data-stu-id="5ade2-117">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="5ade2-118">Kod tek bir HTML olarak işlenen `@` simgesi:</span><span class="sxs-lookup"><span data-stu-id="5ade2-118">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="5ade2-119">HTML öznitelikleri ve e-posta adreslerini içeren içerik yok işler `@` geçiş karakter olarak simge.</span><span class="sxs-lookup"><span data-stu-id="5ade2-119">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="5ade2-120">Aşağıdaki örnekte e-posta adresleri tarafından Razor ayrıştırma aynı şekilde:</span><span class="sxs-lookup"><span data-stu-id="5ade2-120">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="5ade2-121">Örtük Razor ifadeleri</span><span class="sxs-lookup"><span data-stu-id="5ade2-121">Implicit Razor expressions</span></span>

<span data-ttu-id="5ade2-122">Örtük Razor ifadeleri başlayarak `@` C# kodu tarafından izlenen:</span><span class="sxs-lookup"><span data-stu-id="5ade2-122">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="5ade2-123">C# dışında `await` anahtar sözcüğü, örtük ifadeleri boşluk içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-123">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="5ade2-124">C# deyimi Temizle bitiş varsa, alanları birbirine:</span><span class="sxs-lookup"><span data-stu-id="5ade2-124">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="5ade2-125">Örtük ifadeleri **olamaz** köşeli ayraçlar içindeki karakterlerden olarak C# genel türleri, içeren (`<>`) bir HTML etiketi olarak yorumlanır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-125">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="5ade2-126">Aşağıdaki kodu **değil** geçerli:</span><span class="sxs-lookup"><span data-stu-id="5ade2-126">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="5ade2-127">Önceki kod, aşağıdakilerden birini benzer bir derleyici hatası oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5ade2-127">The preceding code generates a compiler error similar to one of the following:</span></span>

 * <span data-ttu-id="5ade2-128">"İnt" öğesi kapalı değildi.</span><span class="sxs-lookup"><span data-stu-id="5ade2-128">The "int" element wasn't closed.</span></span> <span data-ttu-id="5ade2-129">Tüm öğeleri ya da olmalıdır otomatik olarak kapatma veya eşleşen bir bitiş etiketi vardır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-129">All elements must be either self-closing or have a matching end tag.</span></span>
 *  <span data-ttu-id="5ade2-130">Yöntem Grup 'object' türü temsilci için GenericMethod' dönüştürülemiyor.</span><span class="sxs-lookup"><span data-stu-id="5ade2-130">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="5ade2-131">Yöntemini çağırmak istiyordunuz?'</span><span class="sxs-lookup"><span data-stu-id="5ade2-131">Did you intend to invoke the method?\`</span></span> 
 
<span data-ttu-id="5ade2-132">Genel yöntem çağrılarını Sarmalanan, içinde bir [açık Razor ifade](#explicit-razor-expressions) veya [Razor kod bloğunu](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="5ade2-132">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="5ade2-133">Açık Razor ifadeleri</span><span class="sxs-lookup"><span data-stu-id="5ade2-133">Explicit Razor expressions</span></span>

<span data-ttu-id="5ade2-134">Açık Razor ifadeleri oluşur bir `@` dengeli parantez simgesiyle.</span><span class="sxs-lookup"><span data-stu-id="5ade2-134">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="5ade2-135">Geçen haftaki zaman işlemek için aşağıdaki Razor biçimlendirme kullanılır:</span><span class="sxs-lookup"><span data-stu-id="5ade2-135">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="5ade2-136">İçinde herhangi bir içerik `@()` parantez değerlendirilir ve çıktıyı çizilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-136">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="5ade2-137">Örtük ifadeleri, önceki bölümde açıklanan genellikle boşluk içeremez.</span><span class="sxs-lookup"><span data-stu-id="5ade2-137">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="5ade2-138">Aşağıdaki kodda bir hafta geçerli saatten çıkarılır değil:</span><span class="sxs-lookup"><span data-stu-id="5ade2-138">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="5ade2-139">Aşağıdaki HTML kod işler:</span><span class="sxs-lookup"><span data-stu-id="5ade2-139">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="5ade2-140">Açık ifadeler bir ifade sonucu metinle birleştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-140">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="5ade2-141">Açık ifade olmadan `<p>Age@joe.Age</p>` bir e-posta adresi olarak kabul edilir ve `<p>Age@joe.Age</p>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-141">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="5ade2-142">Açık bir ifade olarak yazılırken `<p>Age33</p>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-142">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="5ade2-143">Açık ifadeleri, genel yöntemleri çıktısını işlemek için kullanılabilir *.cshtml* dosyaları.</span><span class="sxs-lookup"><span data-stu-id="5ade2-143">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="5ade2-144">Aşağıdaki biçimlendirmede gösterilen hatayı düzeltmek gösterilmiştir daha önce bir C# genel köşeli parantez neden oldu.</span><span class="sxs-lookup"><span data-stu-id="5ade2-144">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="5ade2-145">Kod açık bir ifade olarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="5ade2-145">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="5ade2-146">İfade kodlama</span><span class="sxs-lookup"><span data-stu-id="5ade2-146">Expression encoding</span></span>

<span data-ttu-id="5ade2-147">Bir dizeyi değerlendirmek C# HTML kodlu ifadelerini.</span><span class="sxs-lookup"><span data-stu-id="5ade2-147">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="5ade2-148">Değerlendirmek için C# ifadeleri `IHtmlContent` doğrudan ile işlenir `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="5ade2-148">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="5ade2-149">Değerlendirme yok C# ifadeleri `IHtmlContent` tarafından bir dizeye dönüştürülür `ToString` ve işlenen önce kodlanır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-149">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="5ade2-150">Aşağıdaki HTML kod işler:</span><span class="sxs-lookup"><span data-stu-id="5ade2-150">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="5ade2-151">HTML tarayıcıda gösterilir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-151">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="5ade2-152">`HtmlHelper.Raw` Çıktı kodlanmış değildir, ancak geri HTML biçimlendirmesi olarak çizilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-152">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="5ade2-153">Kullanarak `HtmlHelper.Raw` unsanitized kullanıcı girişi bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-153">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="5ade2-154">Kullanıcı girişi kötü amaçlı JavaScript veya diğer açıkları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-154">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="5ade2-155">Kullanıcı girişi temizleme zordur.</span><span class="sxs-lookup"><span data-stu-id="5ade2-155">Sanitizing user input is difficult.</span></span> <span data-ttu-id="5ade2-156">Kullanmaktan kaçının `HtmlHelper.Raw` kullanıcı girişi ile.</span><span class="sxs-lookup"><span data-stu-id="5ade2-156">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="5ade2-157">Aşağıdaki HTML kod işler:</span><span class="sxs-lookup"><span data-stu-id="5ade2-157">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="5ade2-158">Razor kod blokları</span><span class="sxs-lookup"><span data-stu-id="5ade2-158">Razor code blocks</span></span>

<span data-ttu-id="5ade2-159">Razor kod blokları başlayarak `@` ve tarafından içine `{}`.</span><span class="sxs-lookup"><span data-stu-id="5ade2-159">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="5ade2-160">İfadeler C# kodu kod blokları içinde çizilir değil.</span><span class="sxs-lookup"><span data-stu-id="5ade2-160">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="5ade2-161">Kod blokları bir görünüm ifadelerinde aynı kapsamı paylaşabilir ve sıraya göre tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="5ade2-161">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="5ade2-162">Aşağıdaki HTML kod işler:</span><span class="sxs-lookup"><span data-stu-id="5ade2-162">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a><span data-ttu-id="5ade2-163">Örtük geçişleri</span><span class="sxs-lookup"><span data-stu-id="5ade2-163">Implicit transitions</span></span>

<span data-ttu-id="5ade2-164">Kod bloğu varsayılan dil C# olduğu, ancak Razor sayfasını HTML olarak geçiş yapabilir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-164">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="5ade2-165">Açık sınırlandırılmış geçiş</span><span class="sxs-lookup"><span data-stu-id="5ade2-165">Explicit delimited transition</span></span>

<span data-ttu-id="5ade2-166">HTML oluşturması gerektiğini bir kod bloğunun alt tanımlamak için Razor ile işleme için karakterleri arasında  **\<metin >** etiketi:</span><span class="sxs-lookup"><span data-stu-id="5ade2-166">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor **\<text>** tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="5ade2-167">Bir HTML etiketi tarafından çevrelenen olmayan HTML oluşturmak için bu yaklaşımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5ade2-167">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="5ade2-168">Bir HTML veya Razor etiketi bir Razor çalışma zamanı hatası oluşur.</span><span class="sxs-lookup"><span data-stu-id="5ade2-168">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="5ade2-169">**\<Metin >** etiketi, içeriği işlenirken boşluk denetlemek yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="5ade2-169">The **\<text>** tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="5ade2-170">Yalnızca arasında içerik  **\<metin >** etiketi işlenir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-170">Only the content between the **\<text>** tag is rendered.</span></span> 
* <span data-ttu-id="5ade2-171">Hiçbir boşluk önce veya sonra  **\<metin >** etiketi HTML çıkışında görünür.</span><span class="sxs-lookup"><span data-stu-id="5ade2-171">No whitespace before or after the **\<text>** tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="5ade2-172">Açık satır geçişle @:</span><span class="sxs-lookup"><span data-stu-id="5ade2-172">Explicit Line Transition with @:</span></span>

<span data-ttu-id="5ade2-173">Tam bir satırın geri kalanı bir kod bloğunun içine HTML olarak işlemek için kullanmak `@:` sözdizimi:</span><span class="sxs-lookup"><span data-stu-id="5ade2-173">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="5ade2-174">Olmadan `@:` kodda bir Razor çalışma zamanı hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5ade2-174">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="5ade2-175">Uyarı: Ek `@` Razor dosyasının karakter bloğu içinde deyimleri derleyici hataları neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-175">Warning: Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="5ade2-176">Bu derleyici hataları gerçek hata önce bildirilen hata oluştuğundan anlaşılması zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-176">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="5ade2-177">Bu hata, bir tek bir kod bloğu birden çok dolaylı/açık ifadelere birleştirme sonra yaygındır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-177">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="5ade2-178">Denetim yapıları</span><span class="sxs-lookup"><span data-stu-id="5ade2-178">Control structures</span></span>

<span data-ttu-id="5ade2-179">Denetim yapıları kod blokları bir uzantı var.</span><span class="sxs-lookup"><span data-stu-id="5ade2-179">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="5ade2-180">Ayrıca kod blokları (geçiş biçimlendirmesi, satır içi C#) tüm yönlerini aşağıdaki yapıları için geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-180">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="5ade2-181">Koşulları @if, başka ise, başka ve @switch</span><span class="sxs-lookup"><span data-stu-id="5ade2-181">Conditionals @if, else if, else, and @switch</span></span>

<span data-ttu-id="5ade2-182">`@if` kod çalıştığında denetimleri:</span><span class="sxs-lookup"><span data-stu-id="5ade2-182">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="5ade2-183">`else` ve `else if` gerektirmeyen `@` simgesi:</span><span class="sxs-lookup"><span data-stu-id="5ade2-183">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="5ade2-184">Aşağıdaki biçimlendirmede switch deyimi kullanmayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-184">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="5ade2-185">Döngü @for, @foreach, @while, ve @do sırada</span><span class="sxs-lookup"><span data-stu-id="5ade2-185">Looping @for, @foreach, @while, and @do while</span></span>

<span data-ttu-id="5ade2-186">Şablonlu HTML denetim ifadeleri döngü ile oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-186">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="5ade2-187">Kişilerin listesini oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="5ade2-187">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="5ade2-188">Aşağıdaki döngü ifadeleri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-188">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="5ade2-189">Bileşik @using</span><span class="sxs-lookup"><span data-stu-id="5ade2-189">Compound @using</span></span>

<span data-ttu-id="5ade2-190">C# ' ta, bir `using` deyimi, bir nesne kapatılır emin olmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-190">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="5ade2-191">Razor aynı mekanizmayı ek içeriklere sahip bir HTML Yardımcıları oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-191">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="5ade2-192">Aşağıdaki kodda bir form etiketi HTML Yardımcıları işlemek `@using` deyimi:</span><span class="sxs-lookup"><span data-stu-id="5ade2-192">In the following code, HTML Helpers render a form tag with the `@using` statement:</span></span>


```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

<span data-ttu-id="5ade2-193">Kapsam düzeyinde Eylemler ile yapılabilir [etiket Yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="5ade2-193">Scope-level actions can be performed with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="5ade2-194">@try, son olarak, yakalama</span><span class="sxs-lookup"><span data-stu-id="5ade2-194">@try, catch, finally</span></span>

<span data-ttu-id="5ade2-195">Özel durum işleme, C# benzer:</span><span class="sxs-lookup"><span data-stu-id="5ade2-195">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

<span data-ttu-id="5ade2-196">Razor kritik bölümler kilit deyimleri ile koruma özelliği vardır:</span><span class="sxs-lookup"><span data-stu-id="5ade2-196">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="5ade2-197">Açıklamalar</span><span class="sxs-lookup"><span data-stu-id="5ade2-197">Comments</span></span>

<span data-ttu-id="5ade2-198">Razor C# ve HTML açıklamaları destekler:</span><span class="sxs-lookup"><span data-stu-id="5ade2-198">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="5ade2-199">Aşağıdaki HTML kod işler:</span><span class="sxs-lookup"><span data-stu-id="5ade2-199">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="5ade2-200">Web sayfası işlenmeden önce razor açıklama sunucu tarafından kaldırıldı.</span><span class="sxs-lookup"><span data-stu-id="5ade2-200">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="5ade2-201">Razor kullanan `@*  *@` açıklamaları sınırlandırmak için.</span><span class="sxs-lookup"><span data-stu-id="5ade2-201">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="5ade2-202">Sunucu tüm biçimlendirmesi oluşturmak olmayan şekilde aşağıdaki kod, kılınmıştır:</span><span class="sxs-lookup"><span data-stu-id="5ade2-202">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="5ade2-203">Yönergeler</span><span class="sxs-lookup"><span data-stu-id="5ade2-203">Directives</span></span>

<span data-ttu-id="5ade2-204">Ayrılmış anahtar sözcükler aşağıdaki örtük ifadelerle ile temsil Razor yönergeleri `@` simgesi.</span><span class="sxs-lookup"><span data-stu-id="5ade2-204">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="5ade2-205">Bir yönerge genellikle bir görünüm ayrıştırılır veya farklı işlevler sağlayan şeklini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-205">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="5ade2-206">Razor kod bir görünümün nasıl oluşturur anlama yönergeleri nasıl çalıştığını anlamak kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-206">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-html[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="5ade2-207">Kod aşağıdakine benzer bir sınıf oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5ade2-207">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="5ade2-208">Bu makalede, bölümün sonraki [bir görünümü için oluşturulan Razor C# sınıfı görüntüleme](#viewing-the-razor-c-class-generated-for-a-view) bu oluşturulan sınıf görüntülemek açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-208">Later in this article, the section [Viewing the Razor C# class generated for a view](#viewing-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

<a name="using"></a>
### <a name="using"></a>@using

<span data-ttu-id="5ade2-209">`@using` Yönergesi ekler C# `using` yönerge oluşturulan görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="5ade2-209">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

<span data-ttu-id="5ade2-210">`@model` Yönergesi bir görünüme iletilen model türünü belirtir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-210">The `@model` directive specifies the type of the model passed to a view:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="5ade2-211">Bireysel kullanıcı hesapları ile oluşturulmuş bir ASP.NET Core MVC uygulamasında *Views/Account/Login.cshtml* görünümü aşağıdaki model bildirimi içerir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-211">In an ASP.NET Core MVC app created with individual user accounts, the *Views/Account/Login.cshtml* view contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="5ade2-212">Oluşturulan sınıf devraldığı `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="5ade2-212">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="5ade2-213">Razor kullanıma sunan bir `Model` model erişmek için özelliği geçirilen görünümü:</span><span class="sxs-lookup"><span data-stu-id="5ade2-213">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="5ade2-214">`@model` Yönergesi bu özelliğin türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-214">The `@model` directive specifies the type of this property.</span></span> <span data-ttu-id="5ade2-215">Yönergeyi belirtir `T` içinde `RazorPage<T>` türeyen görünümü oluşturulan, sınıfın.</span><span class="sxs-lookup"><span data-stu-id="5ade2-215">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="5ade2-216">Varsa `@model` yönergesi değil belirtilen, `Model` özelliği türüdür `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="5ade2-216">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="5ade2-217">Model değeri denetleyicisinden görünümüne geçirilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-217">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="5ade2-218">Daha fazla bilgi için bkz: [modelleri'kesin türü belirtilmiş ve &commat;model anahtar sözcük](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="5ade2-218">For more information, see [Strongly typed models and the &commat;model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="inherits"></a>@inherits

<span data-ttu-id="5ade2-219">`@inherits` Yönergesi görünümü devralır sınıfının tam denetim sağlar:</span><span class="sxs-lookup"><span data-stu-id="5ade2-219">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="5ade2-220">Aşağıdaki kod bir özel Razor sayfa türüdür:</span><span class="sxs-lookup"><span data-stu-id="5ade2-220">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="5ade2-221">`CustomText` Bir görünümde görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-221">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="5ade2-222">Aşağıdaki HTML kod işler:</span><span class="sxs-lookup"><span data-stu-id="5ade2-222">The code renders the following HTML:</span></span>

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 <span data-ttu-id="5ade2-223">`@model` ve `@inherits` aynı görünümünde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-223">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="5ade2-224">`@inherits` kullanılabilir bir *_viewımports.cshtml* görünümü alır dosyası:</span><span class="sxs-lookup"><span data-stu-id="5ade2-224">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="5ade2-225">Aşağıdaki kod, kesin türü belirtilmiş görünüm örneğidir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-225">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="5ade2-226">Varsa "rick@contoso.com" geçirilen modeldeki görünümünü aşağıdaki HTML biçimlendirme oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5ade2-226">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject


<span data-ttu-id="5ade2-227">`@inject` Yönergesi sağlayan bir hizmetinden eklemesine Razor sayfasını [hizmet kapsayıcısı](xref:fundamentals/dependency-injection) görünüm.</span><span class="sxs-lookup"><span data-stu-id="5ade2-227">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="5ade2-228">Daha fazla bilgi için bkz: [bağımlılık ekleme görünümleri içine](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5ade2-228">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

### <a name="functions"></a>@functions

<span data-ttu-id="5ade2-229">`@functions` Yönergesi C# kod bloğu bir görünüme eklemek bir Razor sayfasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="5ade2-229">The `@functions` directive enables a Razor Page to add a C# code block to a view:</span></span>

```cshtml
@functions { // C# Code }
```

<span data-ttu-id="5ade2-230">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5ade2-230">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="5ade2-231">Kod aşağıdaki HTML biçimlendirmeleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="5ade2-231">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="5ade2-232">Aşağıdaki kodu oluşturulan Razor C# sınıf verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-232">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

<span data-ttu-id="5ade2-233">`@section` Yönergesi ile birlikte kullanılan [düzeni](xref:mvc/views/layout) içeriği HTML sayfası farklı bölümlerini işlemek görünümleri etkinleştirmek için.</span><span class="sxs-lookup"><span data-stu-id="5ade2-233">The `@section` directive is used in conjunction with the [layout](xref:mvc/views/layout) to enable views to render content in different parts of the HTML page.</span></span> <span data-ttu-id="5ade2-234">Daha fazla bilgi için bkz: [bölümleri](xref:mvc/views/layout#layout-sections-label).</span><span class="sxs-lookup"><span data-stu-id="5ade2-234">For more information, see [Sections](xref:mvc/views/layout#layout-sections-label).</span></span>

## <a name="tag-helpers"></a><span data-ttu-id="5ade2-235">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="5ade2-235">Tag Helpers</span></span>

<span data-ttu-id="5ade2-236">İlgilidir üç yönergeleri vardır [etiket Yardımcıları](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="5ade2-236">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="5ade2-237">Yönergesi</span><span class="sxs-lookup"><span data-stu-id="5ade2-237">Directive</span></span> | <span data-ttu-id="5ade2-238">İşlev</span><span class="sxs-lookup"><span data-stu-id="5ade2-238">Function</span></span> |
| --------- | -------- |
| [<span data-ttu-id="5ade2-239">&commat;addTagHelper</span><span class="sxs-lookup"><span data-stu-id="5ade2-239">&commat;addTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="5ade2-240">Etiket Yardımcıları bir görünüm için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-240">Makes Tag Helpers available to a view.</span></span> |
| [<span data-ttu-id="5ade2-241">&commat;removeTagHelper</span><span class="sxs-lookup"><span data-stu-id="5ade2-241">&commat;removeTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="5ade2-242">Etiket Yardımcıları daha önce eklediğiniz bir görünümden kaldırır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-242">Removes Tag Helpers previously added from a view.</span></span> |
| [<span data-ttu-id="5ade2-243">&commat;tagHelperPrefix</span><span class="sxs-lookup"><span data-stu-id="5ade2-243">&commat;tagHelperPrefix</span></span>](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="5ade2-244">Etiket Yardımcısı desteğinin etkinleştirmek ve etiket Yardımcısı kullanım açık yapmak için etiket öneki belirtir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-244">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="5ade2-245">Razor ayrılmış anahtar sözcükler</span><span class="sxs-lookup"><span data-stu-id="5ade2-245">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="5ade2-246">Razor anahtar sözcükler</span><span class="sxs-lookup"><span data-stu-id="5ade2-246">Razor keywords</span></span>

* <span data-ttu-id="5ade2-247">Sayfa (ASP.NET Core 2.0 ve üzeri gerektirir)</span><span class="sxs-lookup"><span data-stu-id="5ade2-247">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="5ade2-248">ad alanı</span><span class="sxs-lookup"><span data-stu-id="5ade2-248">namespace</span></span>
* <span data-ttu-id="5ade2-249">işlevleri</span><span class="sxs-lookup"><span data-stu-id="5ade2-249">functions</span></span>
* <span data-ttu-id="5ade2-250">devralır</span><span class="sxs-lookup"><span data-stu-id="5ade2-250">inherits</span></span>
* <span data-ttu-id="5ade2-251">model</span><span class="sxs-lookup"><span data-stu-id="5ade2-251">model</span></span>
* <span data-ttu-id="5ade2-252">section</span><span class="sxs-lookup"><span data-stu-id="5ade2-252">section</span></span>
* <span data-ttu-id="5ade2-253">yardımcı (ASP.NET Core tarafından şu anda desteklenmiyor)</span><span class="sxs-lookup"><span data-stu-id="5ade2-253">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="5ade2-254">Razor anahtar sözcükleri ile kaçışlı `@(Razor Keyword)` (örneğin, `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="5ade2-254">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="5ade2-255">C# Razor anahtar sözcükler</span><span class="sxs-lookup"><span data-stu-id="5ade2-255">C# Razor keywords</span></span>

* <span data-ttu-id="5ade2-256">büyük/küçük harf</span><span class="sxs-lookup"><span data-stu-id="5ade2-256">case</span></span>
* <span data-ttu-id="5ade2-257">do</span><span class="sxs-lookup"><span data-stu-id="5ade2-257">do</span></span>
* <span data-ttu-id="5ade2-258">default</span><span class="sxs-lookup"><span data-stu-id="5ade2-258">default</span></span>
* <span data-ttu-id="5ade2-259">for</span><span class="sxs-lookup"><span data-stu-id="5ade2-259">for</span></span>
* <span data-ttu-id="5ade2-260">foreach</span><span class="sxs-lookup"><span data-stu-id="5ade2-260">foreach</span></span>
* <span data-ttu-id="5ade2-261">if</span><span class="sxs-lookup"><span data-stu-id="5ade2-261">if</span></span>
* <span data-ttu-id="5ade2-262">else</span><span class="sxs-lookup"><span data-stu-id="5ade2-262">else</span></span>
* <span data-ttu-id="5ade2-263">lock</span><span class="sxs-lookup"><span data-stu-id="5ade2-263">lock</span></span>
* <span data-ttu-id="5ade2-264">anahtarı</span><span class="sxs-lookup"><span data-stu-id="5ade2-264">switch</span></span>
* <span data-ttu-id="5ade2-265">Deneyin</span><span class="sxs-lookup"><span data-stu-id="5ade2-265">try</span></span>
* <span data-ttu-id="5ade2-266">Yakalama</span><span class="sxs-lookup"><span data-stu-id="5ade2-266">catch</span></span>
* <span data-ttu-id="5ade2-267">finally</span><span class="sxs-lookup"><span data-stu-id="5ade2-267">finally</span></span>
* <span data-ttu-id="5ade2-268">kullanma</span><span class="sxs-lookup"><span data-stu-id="5ade2-268">using</span></span>
* <span data-ttu-id="5ade2-269">while</span><span class="sxs-lookup"><span data-stu-id="5ade2-269">while</span></span>

<span data-ttu-id="5ade2-270">C# Razor anahtar sözcükler, çift kaçışlı ile olmalıdır `@(@C# Razor Keyword)` (örneğin, `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="5ade2-270">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="5ade2-271">İlk `@` Razor ayrıştırıcısı çıkışları.</span><span class="sxs-lookup"><span data-stu-id="5ade2-271">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="5ade2-272">İkinci `@` C# ayrıştırıcı çıkışları.</span><span class="sxs-lookup"><span data-stu-id="5ade2-272">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="5ade2-273">Razor tarafından kullanılmayan ayrılmış anahtar sözcükler</span><span class="sxs-lookup"><span data-stu-id="5ade2-273">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="5ade2-274">sınıf</span><span class="sxs-lookup"><span data-stu-id="5ade2-274">class</span></span>

## <a name="viewing-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="5ade2-275">Bir görünümü için oluşturulan Razor C# sınıfı görüntüleme</span><span class="sxs-lookup"><span data-stu-id="5ade2-275">Viewing the Razor C# class generated for a view</span></span>

<span data-ttu-id="5ade2-276">Aşağıdaki sınıf ASP.NET Core MVC projenize ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5ade2-276">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="5ade2-277">Geçersiz kılma `RazorTemplateEngine` MVC tarafından eklenen `CustomTemplateEngine` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="5ade2-277">Override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="5ade2-278">Bir kesme noktası ayarlayın `return csharpDocument` deyiminin `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="5ade2-278">Set a break point on the `return csharpDocument` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="5ade2-279">Program yürütme kesme noktasına durduğunda değerini görüntülemek `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="5ade2-279">When program execution stops at the break point, view the value of `generatedCode`.</span></span>

![GeneratedCode metin Görselleştirici görünümü](razor/_static/tvr.png)

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="5ade2-281">Görünüm aramaları ve büyük/küçük harfe duyarlılık</span><span class="sxs-lookup"><span data-stu-id="5ade2-281">View lookups and case sensitivity</span></span>

<span data-ttu-id="5ade2-282">Razor görüntüleme altyapısı için görünümleri büyük küçük harfe duyarlı arama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-282">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="5ade2-283">Ancak, gerçek arama temel alınan dosya sistemi tarafından belirlenir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-283">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="5ade2-284">Dosya tabanlı kaynağı:</span><span class="sxs-lookup"><span data-stu-id="5ade2-284">File based source:</span></span> 
  * <span data-ttu-id="5ade2-285">Büyük küçük harfe duyarlı dosya sistemleri (örneğin, Windows) ile işletim sistemlerinde, fiziksel dosya sağlayıcısı aramaları büyük küçük harfe duyarlı.</span><span class="sxs-lookup"><span data-stu-id="5ade2-285">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="5ade2-286">Örneğin, `return View("Test")` için eşleşen sonuçlanıyor */Views/Home/Test.cshtml*, */Views/home/test.cshtml*ve diğer büyük/küçük harf değişken.</span><span class="sxs-lookup"><span data-stu-id="5ade2-286">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="5ade2-287">Büyük küçük harfe duyarlı dosya sistemlerindeki (örneğin, Linux, OSX ile `EmbeddedFileProvider`), arama duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-287">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="5ade2-288">Örneğin, `return View("Test")` özellikle eşleşmeleri */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5ade2-288">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="5ade2-289">Görünümleri önceden derlenmiş: önceden derlenmiş görünümleri arayan ASP.NET Core ile 2.0 ve üzeri, servis talebi tüm işletim sistemlerinde harflere duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-289">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="5ade2-290">Davranış Windows fiziksel dosya sağlayıcının davranışı aynıdır.</span><span class="sxs-lookup"><span data-stu-id="5ade2-290">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="5ade2-291">İki önceden derlenmiş görünüm yalnızca durumda farklıysa, arama sonucu belirleyici değildir.</span><span class="sxs-lookup"><span data-stu-id="5ade2-291">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="5ade2-292">Geliştiriciler, büyük/küçük harf, büyük küçük harf dosya ve dizin adlarını eşleştirmek için önerilir:</span><span class="sxs-lookup"><span data-stu-id="5ade2-292">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

    * <span data-ttu-id="5ade2-293">Alan, denetleyici ve eylem adları.</span><span class="sxs-lookup"><span data-stu-id="5ade2-293">Area, controller, and action names.</span></span> 
    * <span data-ttu-id="5ade2-294">Razor sayfalarının.</span><span class="sxs-lookup"><span data-stu-id="5ade2-294">Razor Pages.</span></span>
    
<span data-ttu-id="5ade2-295">Servis talebi eşleşen dağıtımları temeldeki dosya sistemi bağımsız olarak kendi görünümler Bul sağlar.</span><span class="sxs-lookup"><span data-stu-id="5ade2-295">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
