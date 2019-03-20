---
ms.openlocfilehash: 1e848a6779a0c68754215ead35d27df5b371f8ed
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265381"
---
<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="a8766-101">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="a8766-101">Test the app</span></span>

* <span data-ttu-id="a8766-102">Uygulamayı çalıştırın ve ekleme `/Movies` tarayıcıda URL'sine (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="a8766-102">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="a8766-103">Test **Oluştur** bağlantı.</span><span class="sxs-lookup"><span data-stu-id="a8766-103">Test the **Create** link.</span></span>

  ![sayfası oluşturma](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="a8766-105">Test **Düzenle**, **ayrıntıları**, ve **Sil** bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="a8766-105">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="a8766-106">Şu hatayı alırsanız, geçişler çalıştırma ve veritabanına güncelleştirilmiş doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="a8766-106">If you get the following error, verify you have run migrations and updated the database:</span></span>

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)
```
