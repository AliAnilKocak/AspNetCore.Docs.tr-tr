---
Başlık: ' ASP.NET Core için bir Içerik Güvenlik Ilkesi Uygula Blazor ' Yazar: Açıklama: ' Blazor siteler arası komut dosyası (XSS) saldırılarına karşı korumaya yardımcı olmak için ASP.NET Core uygulamalarla bir Içerik güvenlik IlkesI (CSP) kullanmayı öğrenin. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>ASP.NET Core için bir Içerik Güvenlik Ilkesi zorlaBlazor

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

[Siteler arası betik oluşturma (XSS)](xref:security/cross-site-scripting) , bir saldırganın bir veya daha fazla kötü amaçlı istemci tarafı komut dosyasını uygulamanın işlenmiş içeriğine yerleştirdiği bir güvenlik açığıdır. Bir Içerik Güvenlik Ilkesi (CSP), geçerli bir tarayıcıya bildirerek XSS saldırılarına karşı korunmaya yardımcı olur:

* Betikler, stil sayfaları ve görüntüler dahil olmak üzere yüklenen içerik kaynakları.
* Formun izin verilen URL hedeflerini belirten bir sayfa tarafından gerçekleştirilen eylemler.
* Yüklenebilen eklentiler.

Bir CSP 'yi bir uygulamaya uygulamak için, geliştirici bir veya daha fazla *directives* `Content-Security-Policy` üst bilgi veya ETIKET içinde çeşitli CSP içerik güvenliği yönergeleri belirler `<meta>` .

İlkeler, bir sayfa yüklenirken tarayıcı tarafından değerlendirilir. Tarayıcı, sayfanın kaynaklarını inceler ve içerik güvenliği yönergelerinin gereksinimlerini karşılayıp karşılamadığını belirler. Bir kaynak için ilke yönergeleri karşılanmazsa, tarayıcı kaynağı yüklemez. Örneğin, üçüncü taraf betiklerine izin veren bir ilkeyi göz önünde bulundurun. Bir sayfa, `<script>` özniteliğinde üçüncü taraf kaynağına sahip bir etiket içerdiğinde `src` , tarayıcı betiğin yüklenmesini engeller.

CSP, Chrome, Edge, Firefox, Opera ve Safari dahil olmak üzere çoğu modern masaüstü ve mobil tarayıcılarda desteklenir. CSP, uygulamalar için önerilir Blazor .

## <a name="policy-directives"></a>İlke yönergeleri

En düşük düzeyde, uygulamalar için aşağıdaki yönergeleri ve kaynakları belirtin Blazor . Gerektiğinde ek yönergeler ve kaynaklar ekleyin. Aşağıdaki yönergeler, bu makalenin [Ilkeyi Uygula](#apply-the-policy) bölümünde, burada Blazor webassembly ve Server için güvenlik ilkelerinin sağlandığı durumlarda kullanılır Blazor :

* [taban URI 'si](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Bir sayfa etiketinin URL 'Lerini kısıtlar `<base>` . `self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
* [Engelle-tümünü-karışık-içerik](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Karışık HTTP ve HTTPS içeriğini yüklemeyi engeller.
* [varsayılan-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; İlke tarafından açıkça belirtilmeyen kaynak yönergeleri için bir geri dönüş gösterir. `self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Görüntüler için geçerli kaynakları gösterir.
  * `data:`URL 'lerden görüntü yüklemeye izin vermek için belirtin `data:` .
  * `https:`Https uç noktalarından görüntülerin yüklenmesine izin vermek için belirtin.
* [nesne-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; `<object>`, Ve etiketleri için geçerli kaynakları gösterir `<embed>` `<applet>` . `none`Tüm URL kaynaklarını engellemek için belirtin.
* [betik-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Betikler için geçerli kaynakları gösterir.
  * `https://stackpath.bootstrapcdn.com/`Önyükleme betikleri için konak kaynağını belirtin.
  * `self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
  * BlazorWebassembly uygulamasında:
    * Gerekli Blazor webassembly satır içi betiklerinin yüklenmesine izin vermek için aşağıdaki karmaları belirtin:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * `unsafe-eval`' In kullanılacağını `eval()` ve dizelerden kod oluşturma yöntemlerini belirtin.
  * Bir Blazor sunucu uygulamasında, `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` stil sayfaları için geri dönüş algılamayı gerçekleştiren satır içi betiğin karmasını belirtin.
* [Stil-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Stil sayfaları için geçerli kaynakları gösterir.
  * `https://stackpath.bootstrapcdn.com/`Önyükleme stil sayfaları için konak kaynağını belirtin.
  * `self`Uygulamanın kaynağının, düzen ve bağlantı noktası numarası dahil olmak üzere geçerli bir kaynak olduğunu belirtmek için belirtin.
  * `unsafe-inline`Satır içi stillerin kullanılmasına izin vermek için belirtin. Blazorİstemci ve sunucunun ilk istekten sonra yeniden bağlanması için sunucu uygulamalarındaki Kullanıcı arabirimi için satır içi bildirimi gerekir. Gelecekteki bir sürümde, artık gerekli olmaması için satır içi stillendirme kaldırılmış olabilir `unsafe-inline` .
* [yükseltme-güvenli olmayan istekler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Güvenli olmayan (HTTP) kaynaklardaki içerik URL 'Lerinin HTTPS üzerinden güvenli bir şekilde alınması gerektiğini gösterir.

Yukarıdaki yönergeler, Microsoft Internet Explorer hariç tüm tarayıcılar tarafından desteklenir.

Ek satır içi betikler için SHA karmaları almak için:

* [Ilkeyi Uygula](#apply-the-policy) bölümünde gösterilen CSP 'yi uygulayın.
* Uygulamayı yerel olarak çalıştırırken tarayıcının geliştirici araçları konsoluna erişin. Tarayıcı, bir CSP üstbilgisi veya etiketi mevcut olduğunda engellenen betikler için karmaları hesaplar ve görüntüler `meta` .
* Tarayıcı tarafından sunulan karmaları `script-src` kaynaklara kopyalayın. Her karmaya ait tek tırnakları kullanın.

Içerik Güvenlik Ilkesi düzey 2 tarayıcı desteği matrisi için bkz. [Içerik Güvenlik Ilkesi düzeyi 2 ' yi kullanabilir miyim](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>İlkeyi Uygula

`<meta>`İlkeyi uygulamak için bir etiket kullanın:

* `http-equiv`Özniteliğinin değerini olarak ayarlayın `Content-Security-Policy` .
* Yönergeleri `content` öznitelik değerine yerleştirin. Yönergeleri noktalı virgül () ile ayırın `;` .
* Etiketi her zaman `meta` `<head>` içeriğe yerleştirin.

Aşağıdaki bölümlerde Blazor webassembly ve Server için örnek ilkeler gösterilmektedir Blazor . Bu örnekler, uygulamasının her sürümü için bu makalede sürümü oluşturulur Blazor . Sürümünüze uygun bir sürümü kullanmak için bu Web sayfasında **Sürüm** açılan Seçicisi seçiciyle birlikte belge sürümü ' nü seçin.

### <a name="blazor-webassembly"></a>BlazorWebAssembly

`<head>` *Wwwroot/index.html* ana bilgisayarı içeriğinde, [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a>BlazorServer

`<head>` *Pages/_Host. cshtml* ana bilgisayar sayfasındaki Içerik sayfasında, [ilke yönergeleri](#policy-directives) bölümünde açıklanan yönergeleri uygulayın:

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Meta etiketi sınırlamaları

Bir `<meta>` Etiket ilkesi aşağıdaki yönergeleri desteklemez:

* [çerçeve-üst öğeleri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [raporla](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [rapor-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [alanda](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Önceki yönergeleri desteklemek için adlı bir üst bilgi kullanın `Content-Security-Policy` . Yönerge dizesi üst bilginin değeridir.

## <a name="test-a-policy-and-receive-violation-reports"></a>İlkeyi test etme ve ihlal raporları alma

Test, ilk ilke oluşturulurken üçüncü taraf betiklerin yanlışlıkla engellenmediğinden emin olmaya yardımcı olur.

İlke yönergelerini uygulamadan bir süre boyunca bir ilkeyi test etmek için, `<meta>` `http-equiv` üst bilgi tabanlı bir ilkenin etiketinin özniteliğini veya üstbilgi adını olarak ayarlayın `Content-Security-Policy-Report-Only` . Hata raporları, belirtilen bir URL 'ye JSON belgeleri olarak gönderilir. Daha fazla bilgi için bkz. [MDN Web belgeleri: içerik-güvenlik-ilke-yalnızca rapor](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Bir ilke etkinken ihlal hakkında raporlamak için aşağıdaki makalelere bakın:

* [raporla](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [rapor-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

`report-uri`Artık kullanım için önerilmese de, `report-to` ana tarayıcıların tümü tarafından desteklenene kadar her iki yönergeler de kullanılmalıdır. `report-uri`İçin destek `report-uri` , tarayıcıların *herhangi bir saatinde* ayrılmasına tabi olduğundan özel olarak kullanmayın. `report-uri` `report-to` , Tam olarak desteklendikleri zaman ilkelerinizde desteğini kaldırın. Benimseme durumunu izlemek için `report-to` , bkz [: Raporlama-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Uygulamanın ilkesini her sürümde test edin ve güncelleştirin.

## <a name="troubleshoot"></a>Sorun giderme

* Hatalar tarayıcının geliştirici araçları konsolunda görünür. Tarayıcılar hakkında bilgi sağlar:
  * İlkeyle uyumlu olmayan öğeler.
  * İlkeyi engellenen bir öğe için izin verecek şekilde değiştirme.
* Bir ilke, istemci tarayıcısı tüm dahil edilen yönergeleri destekliyorsa tamamen etkilidir. Geçerli bir tarayıcı desteği matrisi için, bkz [: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Ek kaynaklar

* [MDN Web belgeleri: Içerik-güvenlik-Ilke](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [İçerik Güvenlik Ilkesi düzeyi 2](https://www.w3.org/TR/CSP2/)
* [Google CSP değerlendiricisi](https://csp-evaluator.withgoogle.com/)
