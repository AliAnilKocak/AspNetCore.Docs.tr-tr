---
title: 'Öğretici: EF Core devralma-ASP.NET MVC uygulama'
description: Bu öğretici, bir ASP.NET Core uygulamasındaki Entity Framework Core kullanarak veri modelinde devralmayı nasıl uygulayacağınızı gösterir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: dab3d2b057162f6d986db10e74e3681acc0ada3b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657243"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Öğretici: EF Core devralma-ASP.NET MVC uygulama

Önceki öğreticide eşzamanlılık özel durumlarını ele alırsınız. Bu öğretici, veri modelinde devralmayı nasıl uygulayacağınızı gösterir.

Nesne odaklı programlamada, kod yeniden kullanımını kolaylaştırmak için devralmayı kullanabilirsiniz. Bu öğreticide, `Instructor` ve `Student` sınıflarını, hem Eğitmenler hem de öğrenciler için ortak olan `LastName` gibi özellikleri içeren bir `Person` taban sınıftan türetireceğiz olacak şekilde değiştireceksiniz. Herhangi bir Web sayfası eklemez veya değiştirmezsiniz, ancak koddan bazılarını değiştireceksiniz ve bu değişiklikler otomatik olarak veritabanına yansıtılacaktır.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Devralmayı veritabanına eşle
> * Kişi sınıfını oluşturma
> * Eğitmeni ve öğrenci 'yi güncelleştirme
> * Modele kişi ekleme
> * Geçişleri oluşturma ve güncelleştirme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

* [Eşzamanlılık işle](concurrency.md)

## <a name="map-inheritance-to-database"></a>Devralmayı veritabanına eşle

Okul veri modelindeki `Instructor` ve `Student` sınıfları özdeş olan birkaç özelliğe sahiptir:

![Öğrenci ve eğitmen sınıfları](inheritance/_static/no-inheritance.png)

`Instructor` ve `Student` varlıkları tarafından paylaşılan özellikler için gereksiz kodu ortadan kaldırmak istediğinizi varsayalım. Ya da adın bir eğitmenden veya bir öğrenciye ait olup olmadığına bakılmaksızın adları biçimlendirmeden bir hizmet yazmak isteyebilirsiniz. Yalnızca bu paylaşılan özellikleri içeren bir `Person` temel sınıf oluşturabilirsiniz, sonra `Instructor` ve `Student` sınıfları aşağıdaki çizimde gösterildiği gibi bu temel sınıftan devralınır.

![Kişi sınıfından türetilen öğrenci ve eğitmen sınıfları](inheritance/_static/inheritance.png)

Bu devralma yapısının veritabanında temsil edilebilmesi için birkaç yol vardır. Tek bir tabloda hem öğrenciler hem de eğitmenler hakkında bilgi içeren bir kişi tablonuz olabilir. Bazı sütunlar yalnızca Eğitmenler (HireDate), bazıları yalnızca öğrencilerle (kayıttarihi), bazıları ise (soyadı, adı) için geçerlidir. Genellikle, her bir satırın temsil ettiği türü belirtmek için bir Ayrıştırıcı sütunu vardır. Örneğin, ayrıştırıcı sütununda, Eğitmenler için "eğitmen" ve öğrenciler için "öğrenci" bulunabilir.

![Hiyerarşi başına tablo örneği](inheritance/_static/tph.png)

Tek bir veritabanı tablosundan bir varlık devralma yapısı oluşturmanın bu düzeni, hiyerarşi başına tablo (TPH) devralma olarak adlandırılır.

Alternatif olarak, veritabanının devralma yapısına benzer bir şekilde görünmesini sağlayabilirsiniz. Örneğin, kişi tablosunda yalnızca ad alanları olabilir ve Tarih alanlarıyla ayrı eğitmen ve öğrenci tabloları vardır.

![Tablo türü devralma](inheritance/_static/tpt.png)

Her varlık sınıfı için bir veritabanı tablosu yapmanın bu düzeni, tür başına tablo (TPT) devralma olarak adlandırılır.

Başka bir seçenek de Özet olmayan tüm türleri tek tek tablolarla eşlemenize olanak sağlar. Devralınan özellikler de dahil olmak üzere bir sınıfın tüm özellikleri karşılık gelen tablonun sütunlarına eşlenir. Bu düzene, tablo başına somut sınıf (TPC) devralma adı verilir. Daha önce gösterildiği gibi, kişi, öğrenci ve eğitmen sınıfları için TPC devralmayı uyguladıysanız, öğrenci ve eğitmen tabloları, devralındıktan sonra, devralma uygulandıktan sonra farklı şekilde görünür.

TPC ve TPH devralma desenleri genellikle TPT devralma desenlerinden daha iyi performans sağlar, çünkü TPT desenleri karmaşık JOIN sorgularına yol açabilir.

Bu öğreticide, TPH devralmanın nasıl uygulanacağı gösterilmektedir. TPH Entity Framework Core desteklediği tek devralma modelidir.  Ne yapacaklarınız `Person` bir sınıf oluşturur, `Instructor` ve `Student` sınıfları `Person`türetmeye değiştirin, yeni sınıfı `DbContext`ekleyin ve bir geçiş oluşturun.

> [!TIP]
> Aşağıdaki değişiklikleri yapmadan önce projenin bir kopyasını kaydetmeyi göz önünde bulundurun.  Daha sonra sorunlarla karşılaşırsanız ve baştan başlamak gerekirse, bu öğretici için yapılan adımları tersine çevirme veya tüm serinin başlangıcına geri dönme yerine kaydedilen projeden başlamak daha kolay olacaktır.

## <a name="create-the-person-class"></a>Kişi sınıfını oluşturma

Modeller klasöründe Person.cs oluşturun ve şablon kodunu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Eğitmeni ve öğrenci 'yi güncelleştirme

*Instructor.cs*' de, kişi sınıfından eğitmen sınıfını türetirsiniz ve anahtar ve ad alanlarını kaldırın. Kod aşağıdaki örneğe benzer şekilde görünür:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

*Student.cs*' de aynı değişiklikleri yapın.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Modele kişi ekleme

Kişi varlık türünü *SchoolContext.cs*öğesine ekleyin. Yeni satırlar vurgulanır.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

Bu, Entity Framework hiyerarşinin devralma devralınmasını yapılandırmak için gereklidir. Gördüğünüz gibi, veritabanı güncelleştirildiği sırada öğrenci ve eğitmen tablolarının yerine bir kişi tablosu olacaktır.

## <a name="create-and-update-migrations"></a>Geçişleri oluşturma ve güncelleştirme

Değişikliklerinizi kaydedin ve projeyi derleyin. Ardından proje klasöründe komut penceresini açın ve aşağıdaki komutu girin:

```dotnetcli
dotnet ef migrations add Inheritance
```

`database update` komutunu henüz çalıştırmayın. Bu komut, eğitmen tablosunu bırakacak ve öğrenci tablosunu kişi olarak yeniden adlandırdığı için kayıp veri oluşmasına neden olur. Varolan verileri korumak için özel kod sağlamanız gerekir.

*_Inheritance. cs > geçişleri/\<zaman damgasını* açın ve `Up` yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Bu kod aşağıdaki veritabanı güncelleştirme görevlerini gerçekleştirir:

* Yabancı anahtar kısıtlamalarını ve öğrenci tablosuna işaret eden dizinleri kaldırır.

* Eğitmen tablosunu kişi olarak yeniden adlandırır ve öğrenci verilerini depolamak için gerekli değişiklikleri yapar:

* Öğrenciler için Nullable kayıt tarihi ekler.

* Bir satırın bir öğrenci mi yoksa bir eğitmen mi olduğunu göstermek için ayrıştırıcı sütunu ekler.

* Öğrenci satırlarında işe alma tarihleri olmadığından, HireDate null yapılabilir hale gelir.

* Öğrencilere işaret eden yabancı anahtarları güncelleştirmek için kullanılacak geçici bir alan ekler. Öğrencileri kişi tablosuna kopyaladığınızda, yeni birincil anahtar değerleri alırlar.

* Öğrenci tablosundaki verileri kişi tablosuna kopyalar. Bu, öğrencilerden yeni birincil anahtar değerleri atanmasını sağlar.

* Öğrencilere işaret eden yabancı anahtar değerlerini düzeltir.

* Yabancı anahtar kısıtlamalarını ve dizinleri yeniden oluşturur, şimdi bunları kişi tablosuna işaret eder.

(Birincil anahtar türü olarak tamsayı yerine GUID kullandıysanız, öğrenci birincil anahtar değerlerinin değiştirilmesi gerekmez ve bu adımların bazıları atlanamaz.)

`database update` komutunu çalıştırın:

```dotnetcli
dotnet ef database update
```

(Bir üretim sisteminde, önceki veritabanı sürümüne geri dönmek için bunu kullanmanız durumunda `Down` yönteminde ilgili değişiklikler yaparsınız. Bu öğreticide `Down` yöntemi kullanmayacaktır.)

> [!NOTE]
> Varolan verileri içeren bir veritabanında şema değişiklikleri yaparken başka hatalar almak mümkündür. Çözemiyoruz geçiş hataları alırsanız, bağlantı dizesindeki veritabanı adını değiştirebilir veya veritabanını silebilirsiniz. Yeni bir veritabanı ile geçirilecek veri yoktur ve Update-Database komutunun hatasız tamamlanabilmesi daha olasıdır. Veritabanını silmek için, SSOX kullanın veya `database drop` CLı komutunu çalıştırın.

## <a name="test-the-implementation"></a>Uygulamayı test etme

Uygulamayı çalıştırın ve çeşitli sayfaları deneyin. Her şey, daha önce olduğu gibi çalışmaktadır.

**SQL Server Nesne Gezgini**, **veri bağlantıları/SchoolContext** ve ardından **Tablolar**' ı genişletin ve öğrenci ve eğitmen tablolarının bir kişi tablosu ile değiştirildiğini görürsünüz. Kişi tablosu tasarımcısını açın ve öğrencinin ve eğitmen tablolarında kullanılan tüm sütunları olduğunu görürsünüz.

![SSOX 'teki kişi tablosu](inheritance/_static/ssox-person-table.png)

Kişi tablosuna sağ tıklayın ve ardından **tablo verilerini göster** ' e tıklayarak ayrıştırıcı sütununu görüntüleyin.

![SSOX tablo verilerinde kişi tablosu](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ek kaynaklar

Entity Framework Core devralma hakkında daha fazla bilgi için bkz. [Devralma](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Veritabanına devralma eşlenmiş
> * Kişi sınıfı oluşturuldu
> * Güncelleştirilmiş eğitmen ve öğrenci
> * Modele kişi eklendi
> * Geçişleri oluşturma ve güncelleştirme
> * Uygulama test edildi

Çeşitli görece gelişmiş Entity Framework senaryolarını nasıl işleyeceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [İleri: gelişmiş konular](advanced.md)
