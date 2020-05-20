---
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>ASP.NET Core için bağlayıcı yapılandırmaBlazor

[Luke Latham](https://github.com/guardrex) tarafından

BlazorWebAssembly, uygulamanın çıkış derlemelerinden gereksiz Il 'yi kırpmak için bir derleme sırasında [ara dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) bağlamayı gerçekleştirir. Hata ayıklama yapılandırmasında oluşturulurken bağlayıcı devre dışı bırakıldı. Bağlayıcı etkinleştirmek için uygulamaların yayın yapılandırmasında derlenmesi gerekir. Webassembly uygulamalarınızı dağıttığınızda yayında derleme yapmanızı öneririz Blazor . 

Uygulama bağlama boyutu için en iyi duruma getirir, ancak bu etkilere sebep olabilir. Bağlayıcı bu dinamik davranışı öğrenmediği ve çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemediği için yansıma veya ilgili dinamik özellikleri kullanan uygulamalar kırpılmayabilir. Bu tür uygulamaları kırpmak için bağlayıcı, koddaki yansıma tarafından gerek duyulan herhangi bir tür ve uygulamanın bağımlı olduğu paketler veya çerçeveler hakkında bilgilendirmelidir. 

Kırpılan uygulamanın dağıtıldıktan sonra düzgün çalıştığından emin olmak için, geliştirme sırasında uygulamanın yayın derlemelerini test etmek önemlidir.

Uygulamalar için bağlama Blazor , bu MSBuild özellikleri kullanılarak yapılandırılabilir:

* Bir [MSBuild özelliği](#control-linking-with-an-msbuild-property)ile genel olarak bağlamayı yapılandırın.
* [Yapılandırma dosyası](#control-linking-with-a-configuration-file)ile derleme temelinde bağlama denetimi.

## <a name="control-linking-with-an-msbuild-property"></a>MSBuild özelliği ile bağlamayı denetleme

Bir uygulama yapılandırmada derlenmediğinde bağlama etkinleştirilir `Release` . Bunu değiştirmek için, `BlazorWebAssemblyEnableLinking` Proje dosyasında MSBuild özelliğini yapılandırın:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Yapılandırma dosyası ile bağlamayı denetleme

Bir XML yapılandırma dosyası sağlayarak ve dosyayı proje dosyasında MSBuild öğesi olarak belirterek, derleme başına temelinde bağlamayı denetleyin:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*Linkerconfig. xml*:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Daha fazla bilgi ve örnek için bkz. [veri biçimleri (mono/bağlayıcı GitHub deposu)](https://github.com/mono/linker/blob/master/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme

Bağlayıcıyı belirli bir kitaplık için yapılandırmak için, kitaplığa katıştırılmış kaynak olarak bir XML bağlayıcı yapılandırma dosyası ekleyin. Katıştırılmış kaynak, derlemeyle aynı ada sahip olmalıdır.

Aşağıdaki örnekte, *Linkerconfig. xml* dosyası, kitaplığın derlemesi ile aynı ada sahip gömülü bir kaynak olarak belirtilir:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Bağlayıcıyı uluslararası duruma getirme için yapılandırma

Varsayılan olarak, Blazor Blazor webassembly uygulamaları için bağlayıcı yapılandırması, açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini şeritleri. Bu derlemelerin kaldırılması uygulamanın boyutunu en aza indirir.

Hangi I18N derlemelerinin korunacağını denetlemek için, `<BlazorWebAssemblyI18NAssemblies>` Proje dosyasında MSBuild özelliğini ayarlayın:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Bölge değeri     | Mono bölgesi derlemesi    |
| ---
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-------- | ---title: ' ASP.NET Core için bağlayıcı yapılandırma: Blazor Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcı denetimini nasıl denetleyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------------ | | `all`            | Tüm derlemeler dahil | | `cjk`             |  *I18N. ÇJK. dll* | | `mideast`         |  *I18N. MIDEAST. dll* | | `none`(varsayılan) | Hiçbiri | | `other`           |  *I18N. Diğer. dll* | | `rare`            |  *I18N. Nadir. dll* | | `west`            |  *I18N. Batı. dll*         |

Birden çok değeri ayırmak için virgül kullanın (örneğin, `mideast,west` ).

Daha fazla bilgi için bkz. [I18N: Pnetlib uluslararası duruma getirme çerçeve kitaplığı (Mono/Mono GitHub deposu)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
