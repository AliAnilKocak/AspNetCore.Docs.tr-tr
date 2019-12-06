---
title: ASP.NET Core Grönyı kullanma
author: rick-anderson
description: ASP.NET Core Grönyı kullanma
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879785"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="6afcb-103">ASP.NET Core Grönyı kullanma</span><span class="sxs-lookup"><span data-stu-id="6afcb-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="6afcb-104">Grrete, komut dosyası küçültmeye, TypeScript derlemesini, kod kalitesi "Lint" araçlarını, CSS ön işlemcilerini ve yalnızca istemci geliştirmeyi desteklemek için gereken tüm yinelenen chore 'yi otomatikleştiren bir JavaScript görev Çalıştırıcısı.</span><span class="sxs-lookup"><span data-stu-id="6afcb-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="6afcb-105">Gryeniden bağlama Visual Studio 'da tam olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="6afcb-106">Bu örnek, başlangıç noktası olarak boş bir ASP.NET Core projesi kullanarak, istemci derleme işleminin sıfırdan nasıl otomatikleştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="6afcb-107">Tamamlanmış örnek, hedef dağıtım dizinini temizler, JavaScript dosyalarını birleştirir, kod kalitesini denetler, JavaScript dosya içeriğini daraltabilir ve Web uygulamanızın köküne dağıtır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="6afcb-108">Aşağıdaki paketleri kullanacağız:</span><span class="sxs-lookup"><span data-stu-id="6afcb-108">We will use the following packages:</span></span>

* <span data-ttu-id="6afcb-109">**gryeniden bağlama**: gryeniden görev Çalıştırıcısı paketi.</span><span class="sxs-lookup"><span data-stu-id="6afcb-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="6afcb-110">**gryeniden bağlama-contrib-Clean**: dosya veya dizinleri kaldıran bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="6afcb-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="6afcb-111">**grkıt-contrib-jshınt**: JavaScript kod kalitesini inceleyen bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="6afcb-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="6afcb-112">**gryeniden bağlama-contrib-Concat**: dosyaları tek bir dosyaya birleştiren bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="6afcb-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="6afcb-113">**gryeniden bağlama-contrib-utimy**: boyutu azaltmak Için JavaScript 'i mini görüntüleyen bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="6afcb-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="6afcb-114">**gryeniden bağlama-contrib-Watch**: dosya etkinliğini izleyen bir eklenti.</span><span class="sxs-lookup"><span data-stu-id="6afcb-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="6afcb-115">Uygulama hazırlanıyor</span><span class="sxs-lookup"><span data-stu-id="6afcb-115">Preparing the application</span></span>

<span data-ttu-id="6afcb-116">Başlamak için yeni bir boş Web uygulaması ayarlayın ve TypeScript örnek dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="6afcb-117">TypeScript dosyaları varsayılan Visual Studio ayarları kullanılarak JavaScript 'e otomatik olarak derlenir ve Grönyı kullanarak işlemek için ham malzemelerimiz olacaktır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="6afcb-118">Visual Studio 'da yeni bir `ASP.NET Web Application`oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6afcb-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="6afcb-119">**Yeni ASP.NET projesi** Iletişim kutusunda **boş** şablon ASP.NET Core seçin ve Tamam düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="6afcb-120">Çözüm Gezgini, proje yapısını gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="6afcb-121">`\src` klasör boş `wwwroot` ve `Dependencies` düğümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![boş Web çözümü](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="6afcb-123">Proje dizininize `TypeScript` adlı yeni bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="6afcb-124">Herhangi bir dosya eklemeden önce, Visual Studio 'Nun TypeScript dosyaları için ' kaydetme sırasında derle ' seçeneğinin işaretli olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="6afcb-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="6afcb-125">**Araçlar** > **Seçenekler** > **metin Düzenleyicisi** > **TypeScript** > **projesi**' ne gidin:</span><span class="sxs-lookup"><span data-stu-id="6afcb-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![TypeScript dosyalarının otomatik derlemesini ayarlama seçenekleri](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="6afcb-127">`TypeScript` dizinine sağ tıklayın ve bağlam menüsünden **> yeni öğe Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="6afcb-128">**JavaScript dosya** öğesini seçin ve dosyayı *tastes. TS* olarak adlandırın (\*. TS uzantısını aklınızda edin).</span><span class="sxs-lookup"><span data-stu-id="6afcb-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="6afcb-129">Aşağıdaki TypeScript kodu satırını dosyaya kopyalayın (kaydettiğinizde, JavaScript kaynağıyla yeni bir *tastes. js* dosyası görünür).</span><span class="sxs-lookup"><span data-stu-id="6afcb-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="6afcb-130">**TypeScript** dizinine ikinci bir dosya ekleyin ve `Food.ts`adlandırın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="6afcb-131">Aşağıdaki kodu dosyasına kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="6afcb-132">NPM 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6afcb-132">Configuring NPM</span></span>

<span data-ttu-id="6afcb-133">Daha sonra, NPM 'yi, grer ve grsıt-görevler 'i indirmek için yapılandırın</span><span class="sxs-lookup"><span data-stu-id="6afcb-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="6afcb-134">Çözüm Gezgini, projeye sağ tıklayın ve bağlam menüsünden **> yeni öğe Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="6afcb-135">**NPM yapılandırma dosyası** öğesini seçin, varsayılan adı *Package. JSON*olarak bırakın ve **Ekle** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="6afcb-136">*Package. JSON* dosyasında, `devDependencies` nesne ayraçları içinde "grönbağlama" yazın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="6afcb-137">IntelliSense listesinden `grunt` ' yi seçin ve ENTER tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="6afcb-138">Visual Studio gryeniden paket adını teklif eder ve iki nokta üst üste ekler.</span><span class="sxs-lookup"><span data-stu-id="6afcb-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="6afcb-139">İki nokta üst üste, IntelliSense listesinin en üstünden paketin en son kararlı sürümünü seçin (IntelliSense görünmüyorsa `Ctrl-Space` ' a basın).</span><span class="sxs-lookup"><span data-stu-id="6afcb-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![gryeniden bağlama IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="6afcb-141">NPM, bağımlılıkları düzenlemek için [anlamsal sürüm oluşturmayı](https://semver.org/) kullanır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="6afcb-142">SemVer olarak da bilinen anlamsal sürüm oluşturma, büyük > \<numaralandırma düzenine sahip paketleri tanımlar.\<küçük >.\<Patch >.</span><span class="sxs-lookup"><span data-stu-id="6afcb-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="6afcb-143">IntelliSense yalnızca birkaç ortak seçeneği göstererek anlamsal sürüm oluşturmayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="6afcb-144">IntelliSense listesindeki üst öğe (Yukarıdaki örnekte 0.4.5), paketin en son kararlı sürümü olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="6afcb-145">Şapka (^) simgesi en son ana sürümle eşleşir ve tilde (~) en son ikincil sürümle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="6afcb-146">SemVer 'in sağladığı tam ifade çekimi için bir kılavuz olarak [NPM semver sürüm ayrıştırıcısı başvurusuna](https://www.npmjs.com/package/semver) bakın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="6afcb-147">Aşağıdaki örnekte gösterildiği gibi, *Clean*, *jshınt*, *Concat*, *uıshowmy*ve *Watch* için grkıt-contrib-\* paketlerine yük daha fazla bağımlılık ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="6afcb-148">Sürümlerin örnekle eşleşmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="6afcb-148">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="6afcb-149">*Package. JSON* dosyasını kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-149">Save the *package.json* file.</span></span>

<span data-ttu-id="6afcb-150">Her bir `devDependencies` öğesi için paketler, her paketin gerektirdiği tüm dosyalarla birlikte indirilir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="6afcb-151">Paket dosyalarını, **Çözüm Gezgini** **tüm dosyaları göster** düğmesini etkinleştirerek *node_modules* dizininde bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6afcb-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![gryeniden bağlama node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="6afcb-153">Gerekirse, `Dependencies\NPM` ' a sağ tıklayıp **paketleri geri yükle** menü seçeneğini belirleyerek **Çözüm Gezgini** bağımlılıkları el ile geri yükleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6afcb-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![paketleri geri yükle](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="6afcb-155">Grönbağlama yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6afcb-155">Configuring Grunt</span></span>

<span data-ttu-id="6afcb-156">Gror, el ile çalıştırılabilecek veya Visual Studio 'daki olaylara göre otomatik olarak çalışacak şekilde yapılandırılmış görevleri tanımlayan, yükleyen ve kaydeden *Gruntfile. js* adlı bir bildirim kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="6afcb-157">Projeye sağ tıklayın ve > **Yeni öğe** **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="6afcb-158">**JavaScript dosya** öğesi şablonunu seçin, adı *Gruntfile. js*olarak değiştirin ve **Ekle** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="6afcb-159">*Gruntfile. js*' ye aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="6afcb-160">`initConfig` işlevi her bir paket için seçenekleri ayarlar ve modülün geri kalanı görevleri yükler ve kaydeder.</span><span class="sxs-lookup"><span data-stu-id="6afcb-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="6afcb-161">`initConfig` işlevi içinde, aşağıdaki örnek *Gruntfile. js* ' de gösterildiği gibi `clean` görevi için seçenekler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="6afcb-162">`clean` görevi Dizin dizeleri dizisini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="6afcb-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="6afcb-163">Bu görev, dosyaları *Wwwroot/lib* 'den kaldırır ve tüm */Temp klasörüne yazılır* dizinini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="6afcb-164">`initConfig` işlevinin altında, `grunt.loadNpmTasks`için bir çağrı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="6afcb-165">Bu işlem, görevin Visual Studio 'dan bir şekilde bir şekilde bir şekilde bir şekilde</span><span class="sxs-lookup"><span data-stu-id="6afcb-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="6afcb-166">*Gruntfile. js dosyasını*kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="6afcb-167">Dosya aşağıdaki ekran görüntüsüne benzer şekilde görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-167">The file should look something like the screenshot below.</span></span>

    ![ilk gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="6afcb-169">*Gruntfile. js* ' ye sağ tıklayın ve bağlam menüsünden **görev Çalıştırıcısı Gezgini** ' ni seçin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="6afcb-170">**Görev çalıştırıcı Gezgini** penceresi açılır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-170">The **Task Runner Explorer** window will open.</span></span>

    ![görev Çalıştırıcısı Gezgin menüsü](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="6afcb-172">`clean` **görev Çalıştırıcısı Gezgininde** **Görevler** altında görüntülendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![görev çalıştırıcı Gezgini görev listesi](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="6afcb-174">Temizleme görevine sağ tıklayın ve bağlam menüsünden **Çalıştır** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="6afcb-175">Bir komut penceresi, görevin ilerlemesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6afcb-175">A command window displays progress of the task.</span></span>

    ![görev çalıştırıcı Gezgini temiz görevi çalıştır](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="6afcb-177">Henüz temizleyene dosya veya dizin yok.</span><span class="sxs-lookup"><span data-stu-id="6afcb-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="6afcb-178">İsterseniz, bunları Çözüm Gezgini el ile oluşturabilir ve sonra temiz görevi bir test olarak çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6afcb-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="6afcb-179">`initConfig` işlevinde, aşağıdaki kodu kullanarak `concat` için bir giriş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="6afcb-180">`src` Property dizisi birleştirilecek dosyaları, birleştirilmeleri gereken sırayla listeler.</span><span class="sxs-lookup"><span data-stu-id="6afcb-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="6afcb-181">`dest` özelliği, üretilen Birleşik dosyanın yolunu atar.</span><span class="sxs-lookup"><span data-stu-id="6afcb-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="6afcb-182">Yukarıdaki koddaki `all` özelliği bir hedefin adıdır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="6afcb-183">Hedefler, bazı Grsi görevlerinde birden çok derleme ortamına izin vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="6afcb-184">IntelliSense kullanarak yerleşik hedefleri görüntüleyebilir veya kendi kendinize atayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6afcb-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="6afcb-185">Aşağıdaki kodu kullanarak `jshint` görevini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="6afcb-186">Jshınt `code-quality` yardımcı programı, *Temp* dizininde bulunan her JavaScript dosyasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="6afcb-187">"-W069" seçeneği, JavaScript, nokta gösterimi yerine bir özellik atamak için köşeli ayraç sözdizimi kullandığında jshınt tarafından oluşturulan bir hatadır, örneğin `Tastes.Sweet`yerine `Tastes["Sweet"]`.</span><span class="sxs-lookup"><span data-stu-id="6afcb-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="6afcb-188">Seçeneği, işlemin geri kalanının devam etmesine izin vermek için uyarıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="6afcb-189">Aşağıdaki kodu kullanarak `uglify` görevini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="6afcb-190">Görev, Temp dizininde bulunan *birleştirilmiş. js* dosyasını mini olarak oluşturur ve standart adlandırma kuralı *\<dosya adı\>. min. js*' den sonra sonuç dosyasını Wwwroot/lib içinde oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6afcb-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="6afcb-191">`grunt-contrib-clean`yükleyen `grunt.loadNpmTasks` çağrısı altında, aşağıdaki kodu kullanarak jshınt, Concat ve UART My için aynı çağrıyı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="6afcb-192">*Gruntfile. js dosyasını*kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="6afcb-193">Dosya aşağıdaki örnekteki gibi görünmelidir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-193">The file should look something like the example below.</span></span>

    ![Tüm gryeniden dosya örneğini doldurun](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="6afcb-195">**Görev çalıştırıcı Gezgini** görev listesi `clean`, `concat`, `jshint` ve `uglify` görevleri içerdiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="6afcb-196">Her görevi sırayla çalıştırın ve **Çözüm Gezgini**sonuçları gözlemleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="6afcb-197">Her görev hatasız çalışmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-197">Each task should run without errors.</span></span>

    ![görev çalıştırıcı Gezgini her görevi çalıştır](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="6afcb-199">Concat görevi yeni bir *birleştirilmiş. js* dosyası oluşturur ve bunu Temp dizinine koyar.</span><span class="sxs-lookup"><span data-stu-id="6afcb-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="6afcb-200">`jshint` görevi yalnızca çalışır ve çıkış üretmez.</span><span class="sxs-lookup"><span data-stu-id="6afcb-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="6afcb-201">`uglify` görevi yeni bir *birleştirilmiş. min. js* dosyası oluşturur ve bunu *Wwwroot/lib*'e koyar.</span><span class="sxs-lookup"><span data-stu-id="6afcb-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="6afcb-202">Tamamlandığında, çözüm aşağıdaki ekran görüntüsüne benzer şekilde görünmelidir:</span><span class="sxs-lookup"><span data-stu-id="6afcb-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![tüm görevlerden sonra Çözüm Gezgini](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="6afcb-204">Her pakete ilişkin seçenekler hakkında daha fazla bilgi için [https://www.npmjs.com/](https://www.npmjs.com/) ziyaret edin ve ana sayfadaki arama kutusunda paket adını arayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="6afcb-205">Örneğin, tüm parametrelerini açıklayan bir belge bağlantısı almak için grcönt-contrib-Clean paketini arayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6afcb-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="6afcb-206">Şimdi hepsi bir arada</span><span class="sxs-lookup"><span data-stu-id="6afcb-206">All together now</span></span>

<span data-ttu-id="6afcb-207">Belirli bir dizideki bir dizi görevi çalıştırmak için Gryeniden `registerTask()` yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="6afcb-208">Örneğin, yukarıdaki örnek adımları sırayla çalıştırmak için > Concat-> jshınt-> uıshowmy ' a gidin ve aşağıdaki kodu modüle ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="6afcb-209">Kod, InitConfig dışında loadNpmTasks () çağrılarıyla aynı düzeye eklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="6afcb-210">Yeni görev, görev Çalıştırıcısı Gezgini 'nde diğer ad görevleri altında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="6afcb-211">Bunu, diğer görevleri yaptığınız gibi sağ tıklayıp çalıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6afcb-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="6afcb-212">`all` görevi sırasıyla `clean`, `concat`, `jshint` ve `uglify`çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![diğer ad grer görevleri](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="6afcb-214">Değişiklik izleme</span><span class="sxs-lookup"><span data-stu-id="6afcb-214">Watching for changes</span></span>

<span data-ttu-id="6afcb-215">`watch` bir görev, dosyaları ve dizinleri göz önünde bulundurur.</span><span class="sxs-lookup"><span data-stu-id="6afcb-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="6afcb-216">İzleme, değişiklikleri algılarsa görevleri otomatik olarak tetikler.</span><span class="sxs-lookup"><span data-stu-id="6afcb-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="6afcb-217">TypeScript dizinindeki \*. js dosyalarında yapılan değişiklikleri izlemek için aşağıdaki kodu InitConfig dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="6afcb-218">Bir JavaScript dosyası değiştirilirse `watch` `all` görevini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="6afcb-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="6afcb-219">Görev çalıştırıcı Gezgini 'nde `watch` görevi göstermek için `loadNpmTasks()` bir çağrı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="6afcb-220">Görev çalıştırıcı Gezgini ' nde gözcü görevine sağ tıklayın ve bağlam menüsünden Çalıştır ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="6afcb-221">Çalışan izleme görevini gösteren komut penceresinde "bekleniyor..." görüntülenir İleti.</span><span class="sxs-lookup"><span data-stu-id="6afcb-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="6afcb-222">TypeScript dosyalarından birini açın, bir boşluk ekleyin ve dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="6afcb-223">Bu işlem, izleme görevini tetikler ve diğer görevleri sırayla çalışacak şekilde tetikler.</span><span class="sxs-lookup"><span data-stu-id="6afcb-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="6afcb-224">Aşağıdaki ekran görüntüsünde örnek bir çalıştırma gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="6afcb-224">The screenshot below shows a sample run.</span></span>

![çalışan görevler çıkışı](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="6afcb-226">Visual Studio olaylarına bağlama</span><span class="sxs-lookup"><span data-stu-id="6afcb-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="6afcb-227">Visual Studio 'da her çalıştığınızda görevlerinizi el ile başlatmak istemediğiniz müddetçe, derleme, **Temizleme**ve **proje açık** olayları **sonrasında** **derlemeden önce**görevleri bağlayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-227">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="6afcb-228">`watch`, Visual Studio her açıldığında çalışacak şekilde bağlayın.</span><span class="sxs-lookup"><span data-stu-id="6afcb-228">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="6afcb-229">Görev çalıştırıcı Gezgini ' nde, Gözcü görevine sağ tıklayın ve bağlam menüsünden **bağlamalar** > **Proje Aç** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-229">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![projeyi açmak için bir görev bağlama](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="6afcb-231">Projeyi kaldırın ve yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="6afcb-231">Unload and reload the project.</span></span> <span data-ttu-id="6afcb-232">Proje yeniden yüklendiğinde, izleme görevi otomatik olarak çalışmaya başlar.</span><span class="sxs-lookup"><span data-stu-id="6afcb-232">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="6afcb-233">Özet</span><span class="sxs-lookup"><span data-stu-id="6afcb-233">Summary</span></span>

<span data-ttu-id="6afcb-234">Gryeniden oluşturma, çoğu istemci derleme görevini otomatikleştirmek için kullanılabilen güçlü bir görev Çalıştırıcısı.</span><span class="sxs-lookup"><span data-stu-id="6afcb-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="6afcb-235">Grsıt, paketlerini teslim etmek için NPM 'den yararlanır ve Visual Studio ile tümleştirme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="6afcb-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="6afcb-236">Visual Studio 'nun görev Çalıştırıcısı Gezgini yapılandırma dosyalarındaki değişiklikleri algılar ve görevleri çalıştırmak, çalışan görevleri görüntülemek ve görevleri Visual Studio olaylarına bağlamak için uygun bir arabirim sağlar.</span><span class="sxs-lookup"><span data-stu-id="6afcb-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
