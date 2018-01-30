---
title: "Komut satırı araçlarını kullanarak Azure ASP.NET Core uygulama yayımlama"
author: camsoper
description: "Azure App Service'e Git komut satırı İstemcisi'ni kullanarak bir ASP.NET Core uygulamayı yayımlamak öğrenin."
manager: wpickett
ms.author: casoper
ms.custom: mvc
ms.date: 11/03/2017
ms.devlang: dotnet
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
services: multiple
uid: tutorials/publish-to-azure-webapp-using-cli
ms.openlocfilehash: 0418a2695d3afb6dc2c55b8f694a97d62239835f
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/30/2018
---
# <a name="deploy-an-aspnet-core-application-to-azure-app-service-from-the-command-line"></a>Azure uygulama hizmeti komut satırından bir ASP.NET Core uygulamayı dağıtma

Tarafından [Cam Soper](https://twitter.com/camsoper)

Bu öğretici komut satırı araçlarını kullanarak oluşturmak ve Microsoft Azure App Service ASP.NET Core uygulama dağıtmak nasıl yapacağınızı gösterir.  Tamamlandığında, ASP.NET MVC Azure App Service Web uygulaması barındırılan çekirdek yerleşik bir web uygulamasına sahip olacaksınız.  Bu öğretici Windows komut satırı araçları kullanılarak yazılmış ancak macOS hem de Linux ortamlarında uygulanabilir.  

Bu öğreticide, bilgi nasıl yapılır:

> [!div class="checklist"]
> * Azure CLI kullanarak Azure App Service Web sitesi oluşturma
> * Azure App Service Git komut satırı aracını kullanarak bir ASP.NET Core uygulamayı dağıtma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için ihtiyacınız vardır:

* A [Microsoft Azure aboneliği](https://azure.microsoft.com/free/)
* [.NET Core](https://www.microsoft.com/net/download/core)
* [Git](https://www.git-scm.com/) komut satırı istemcisi

## <a name="create-a-web-application"></a>Bir web uygulaması oluşturma

Web uygulaması için yeni bir dizin oluşturma, yeni bir ASP.NET Core MVC uygulaması oluşturma ve Web sitesi yerel olarak çalıştırın.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
```cmd
REM Create a new ASP.NET Core MVC application
dotnet new razor -o MyApplication

REM Change to the new directory that was just created
cd MyApplication

REM Run the application
dotnet run
```

# <a name="othertabother"></a>[Diğer](#tab/other)
```bash
# Create a new ASP.NET Core MVC application
dotnet new razor -o MyApplication

# Change to the new directory that was just created
cd MyApplication

# Run the application
dotnet run
```
---

![Komut satırı çıkışı](publish-to-azure-webapp-using-cli/_static/new_prj.png)

Http://localhost: 5000 için göz atarak uygulamayı test etme.

![Yerel olarak çalışan Web sitesi](publish-to-azure-webapp-using-cli/_static/app_test.png)


## <a name="create-the-azure-app-service-instance"></a>Azure App Service örneği oluşturma

Kullanarak [Azure bulut Kabuk](/azure/cloud-shell/quickstart), bir kaynak grubu, uygulama hizmeti planı ve bir App Service web uygulaması oluşturun.

```azurecli-interactive
# Generate a unique Web App name
let randomNum=$RANDOM*$RANDOM
webappname=tutorialApp$randomNum

# Create the DotNetAzureTutorial resource group
az group create --name DotNetAzureTutorial --location EastUS

# Create an App Service plan.
az appservice plan create --name $webappname --resource-group DotNetAzureTutorial --sku FREE

# Create the Web App
az webapp create --name $webappname --resource-group DotNetAzureTutorial --plan $webappname
```

Dağıtım öncesinde aşağıdaki komutu kullanarak hesap düzeyinde dağıtım kimlik bilgilerini ayarlayın:

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```

Bir dağıtım URL'si Git kullanarak uygulamayı dağıtmak için gereklidir.  Bu gibi URL'sini alın.

```azurecli-interactive
az webapp deployment source config-local-git -n $webappname -g DotNetAzureTutorial --query [url] -o tsv
```
Biten görüntülenen URL'yi Not `.git`. Sonraki adımda kullanılır.

## <a name="deploy-the-application-using-git"></a>Git kullanarak uygulamayı dağıtın

Yerel makinenizden Git kullanarak dağıtmak hazırsınız.

> [!NOTE]
> Satır sonları ilgili Git tüm uyarılar yoksaymak güvenlidir.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
```cmd
REM Initialize the local Git repository
git init

REM Add the contents of the working directory to the repo
git add --all

REM Commit the changes to the local repo
git commit -a -m "Initial commit"

REM Add the URL as a Git remote repository
git remote add azure <THE GIT URL YOU NOTED EARLIER>

REM Push the local repository to the remote
git push azure master
```

# <a name="othertabother"></a>[Diğer](#tab/other)
```bash
# Initialize the local Git repository
git init

# Add the contents of the working directory to the repo
git add --all

# Commit the changes to the local repo
git commit -a -m "Initial commit"

# Add the URL as a Git remote repository
git remote add azure <THE GIT URL YOU NOTED EARLIER>

# Push the local repository to the remote
git push azure master
```
---

Git önceki ayarlanan dağıtım kimlik bilgilerini ister. Kimlik doğrulandıktan sonra uygulama uzak konuma gönderilir, oluşturulur ve dağıtılır.

![Git dağıtımı çıktı](publish-to-azure-webapp-using-cli/_static/post_deploy.png)

## <a name="test-the-application"></a>Uygulamayı test etme

Göz atarak uygulamayı test `https://<web app name>.azurewebsites.net`.  Bulut Kabuğu (veya Azure CLI) adresini görüntülemek için aşağıdakini kullanın:

```azurecli-interactive
az webapp show -n $webappname -g DotNetAzureTutorial --query defaultHostName -o tsv
```

![Azure'da çalışan uygulama](publish-to-azure-webapp-using-cli/_static/app_deployed.png)

## <a name="clean-up"></a>Temizleme

Uygulamayı test etme ve kod ve kaynakları İnceleme tamamlandığında, web app ve planı kaynak grubunu silerek silin.

```azurecli-interactive
az group delete -n DotNetAzureTutorial
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, öğrenilen nasıl yapılır:

> [!div class="checklist"]
> * Azure CLI kullanarak Azure App Service Web sitesi oluşturma
> * Azure App Service Git komut satırı aracını kullanarak bir ASP.NET Core uygulamayı dağıtma

Ardından, CosmosDB kullanan mevcut bir web uygulamasına dağıtmak için komut satırını kullanmayı öğrenin.

> [!div class="nextstepaction"]
> [.NET Core ile komut satırından Azure'a dağıtma](/dotnet/azure/dotnet-quickstart-xplat)
