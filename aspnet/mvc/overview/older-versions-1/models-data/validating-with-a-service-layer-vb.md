---
uid: mvc/overview/older-versions-1/models-data/validating-with-a-service-layer-vb
title: "Bir hizmet Katmanı (VB) doğrulanıyor | Microsoft Docs"
author: StephenWalther
description: "Doğrulama mantığınız denetleyici eylemleri dışında ve ayrı bir hizmet katmana taşıma konusunda bilgi edinin. Bu öğreticide, Stephen Walther açıklar nasıl..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/02/2009
ms.topic: article
ms.assetid: 344bb38e-4965-4c47-bda1-f6d29ae5b83a
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/models-data/validating-with-a-service-layer-vb
msc.type: authoredcontent
ms.openlocfilehash: 5a8f1dd888c7fa6a3353b7b748a0ffa30b94149c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2017
---
<a name="validating-with-a-service-layer-vb"></a><span data-ttu-id="f4dec-104">Bir hizmet Katmanı (VB) ile doğrulama</span><span class="sxs-lookup"><span data-stu-id="f4dec-104">Validating with a Service Layer (VB)</span></span>
====================
<span data-ttu-id="f4dec-105">tarafından [Stephen Walther](https://github.com/StephenWalther)</span><span class="sxs-lookup"><span data-stu-id="f4dec-105">by [Stephen Walther](https://github.com/StephenWalther)</span></span>

> <span data-ttu-id="f4dec-106">Doğrulama mantığınız denetleyici eylemleri dışında ve ayrı bir hizmet katmana taşıma konusunda bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="f4dec-106">Learn how to move your validation logic out of your controller actions and into a separate service layer.</span></span> <span data-ttu-id="f4dec-107">Bu öğreticide, hizmet katmanınız denetleyicisi katmanından yalıtarak sorunları sharp ayrılması nasıl bulundurabilirsiniz Stephen Walther açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-107">In this tutorial, Stephen Walther explains how you can maintain a sharp separation of concerns by isolating your service layer from your controller layer.</span></span>


<span data-ttu-id="f4dec-108">Bu öğretici, bir ASP.NET MVC uygulamasındaki doğrulama gerçekleştirme bir yöntemi tanımlamak için hedefidir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-108">The goal of this tutorial is to describe one method of performing validation in an ASP.NET MVC application.</span></span> <span data-ttu-id="f4dec-109">Bu öğreticide, doğrulama mantığınız denetleyicilerinizi dışında ve ayrı bir hizmet katmana taşıma hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="f4dec-109">In this tutorial, you learn how to move your validation logic out of your controllers and into a separate service layer.</span></span>

## <a name="separating-concerns"></a><span data-ttu-id="f4dec-110">Sorunları ayırma</span><span class="sxs-lookup"><span data-stu-id="f4dec-110">Separating Concerns</span></span>

<span data-ttu-id="f4dec-111">Bir ASP.NET MVC uygulamasını derlerken, denetleyici eylemleri içinde veritabanı mantığınızı girmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-111">When you build an ASP.NET MVC application, you should not place your database logic inside your controller actions.</span></span> <span data-ttu-id="f4dec-112">Veritabanı ve denetleyici mantığınızı karıştırma uygulamanızı zamanla korumak daha zor hale getirir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-112">Mixing your database and controller logic makes your application more difficult to maintain over time.</span></span> <span data-ttu-id="f4dec-113">Ayrı deposu katmanındaki tüm veritabanı mantığınızı yerleştirin önerilir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-113">The recommendation is that you place all of your database logic in a separate repository layer.</span></span>

<span data-ttu-id="f4dec-114">Örneğin, 1 listeleme ProductRepository adlı basit bir depoya içerir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-114">For example, Listing 1 contains a simple repository named the ProductRepository.</span></span> <span data-ttu-id="f4dec-115">Ürün depo tüm uygulama için veri erişim kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-115">The product repository contains all of the data access code for the application.</span></span> <span data-ttu-id="f4dec-116">Listenin ayrıca ürün depo uygulayan IProductRepository arabirimi içerir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-116">The listing also includes the IProductRepository interface that the product repository implements.</span></span>

<span data-ttu-id="f4dec-117">**1 - Models\ProductRepository.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-117">**Listing 1 - Models\ProductRepository.vb**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample1.vb)]

<span data-ttu-id="f4dec-118">Listeleme 2 denetleyicisi deposu katman kendi İNDİS() ve Create() Eylemler kullanır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-118">The controller in Listing 2 uses the repository layer in both its Index() and Create() actions.</span></span> <span data-ttu-id="f4dec-119">Bu denetleyici herhangi bir veritabanı mantık içermiyor dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f4dec-119">Notice that this controller does not contain any database logic.</span></span> <span data-ttu-id="f4dec-120">Bir depo katmanı oluşturma sorunları temiz ayrılması sağlamanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="f4dec-120">Creating a repository layer enables you to maintain a clean separation of concerns.</span></span> <span data-ttu-id="f4dec-121">Denetleyicileri için uygulama akış denetimi mantığı sorumludur ve veri erişim mantığı için depo sorumludur.</span><span class="sxs-lookup"><span data-stu-id="f4dec-121">Controllers are responsible for application flow control logic and the repository is responsible for data access logic.</span></span>

<span data-ttu-id="f4dec-122">**2 - Controllers\ProductController.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-122">**Listing 2 - Controllers\ProductController.vb**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample2.vb)]

## <a name="creating-a-service-layer"></a><span data-ttu-id="f4dec-123">Bir hizmet katmanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="f4dec-123">Creating a Service Layer</span></span>

<span data-ttu-id="f4dec-124">Bu nedenle, uygulama akış denetimi mantığı denetleyicide yer alır ve bir depoda veri erişim mantığı alır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-124">So, application flow control logic belongs in a controller and data access logic belongs in a repository.</span></span> <span data-ttu-id="f4dec-125">Bu durumda, burada doğrulama mantığınız put?</span><span class="sxs-lookup"><span data-stu-id="f4dec-125">In that case, where do you put your validation logic?</span></span> <span data-ttu-id="f4dec-126">Bir seçenektir doğrulama mantığınız yerleştirmek için bir *hizmet katmanı*.</span><span class="sxs-lookup"><span data-stu-id="f4dec-126">One option is to place your validation logic in a *service layer*.</span></span>

<span data-ttu-id="f4dec-127">Bir hizmet katmanı, bir ASP.NET MVC uygulamasındaki bir denetleyici ve depo katmanı arasındaki iletişimi aracılık ek bir katmanıdır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-127">A service layer is an additional layer in an ASP.NET MVC application that mediates communication between a controller and repository layer.</span></span> <span data-ttu-id="f4dec-128">Hizmet katmanı iş mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-128">The service layer contains business logic.</span></span> <span data-ttu-id="f4dec-129">Özellikle, doğrulama mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-129">In particular, it contains validation logic.</span></span>

<span data-ttu-id="f4dec-130">Örneğin, listeleme 3 Ürün hizmet katmanında bir CreateProduct() yöntemi vardır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-130">For example, the product service layer in Listing 3 has a CreateProduct() method.</span></span> <span data-ttu-id="f4dec-131">CreateProduct() yöntemi ürünün ürün depoya geçirmeden önce yeni bir ürün doğrulamak için ValidateProduct() yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-131">The CreateProduct() method calls the ValidateProduct() method to validate a new product before passing the product to the product repository.</span></span>

<span data-ttu-id="f4dec-132">**3 - Models\ProductService.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-132">**Listing 3 - Models\ProductService.vb**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample3.vb)]

<span data-ttu-id="f4dec-133">Ürün denetleyicisi listeleme yerine depo katman hizmet katmanı kullanmak üzere 4 güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="f4dec-133">The Product controller has been updated in Listing 4 to use the service layer instead of the repository layer.</span></span> <span data-ttu-id="f4dec-134">Denetleyici katman hizmet katmanına alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-134">The controller layer talks to the service layer.</span></span> <span data-ttu-id="f4dec-135">Hizmet katmanı deposu katmana alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-135">The service layer talks to the repository layer.</span></span> <span data-ttu-id="f4dec-136">Her katman ayrı bir sorumluluğa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-136">Each layer has a separate responsibility.</span></span>

<span data-ttu-id="f4dec-137">**4 - Controllers\ProductController.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-137">**Listing 4 - Controllers\ProductController.vb**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample4.vb)]

<span data-ttu-id="f4dec-138">Ürün hizmet ürün denetleyicisi oluşturucuda oluşturduğunuz dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f4dec-138">Notice that the product service is created in the product controller constructor.</span></span> <span data-ttu-id="f4dec-139">Ürün hizmet oluşturulduğunda, model durumu sözlüğü hizmete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-139">When the product service is created, the model state dictionary is passed to the service.</span></span> <span data-ttu-id="f4dec-140">Ürün hizmet model durumunu doğrulama hata iletileri denetleyiciye geri geçirmek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-140">The product service uses model state to pass validation error messages back to the controller.</span></span>

## <a name="decoupling-the-service-layer"></a><span data-ttu-id="f4dec-141">Hizmet katmanı ayırma</span><span class="sxs-lookup"><span data-stu-id="f4dec-141">Decoupling the Service Layer</span></span>

<span data-ttu-id="f4dec-142">Denetleyici ve bir yönüyle hizmet katmanları yalıtmak başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="f4dec-142">We have failed to isolate the controller and service layers in one respect.</span></span> <span data-ttu-id="f4dec-143">Denetleyici ve hizmet katmanları model durumu iletişim kurar.</span><span class="sxs-lookup"><span data-stu-id="f4dec-143">The controller and service layers communicate through model state.</span></span> <span data-ttu-id="f4dec-144">Diğer bir deyişle, hizmet katmanı, ASP.NET MVC çerçevesi belirli bir özellik bir bağımlılığa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-144">In other words, the service layer has a dependency on a particular feature of the ASP.NET MVC framework.</span></span>

<span data-ttu-id="f4dec-145">Hizmet katmanı mümkün olduğunca bizim denetleyicisi katmandan ayırt etmek istiyoruz.</span><span class="sxs-lookup"><span data-stu-id="f4dec-145">We want to isolate the service layer from our controller layer as much as possible.</span></span> <span data-ttu-id="f4dec-146">Teorik olarak, size herhangi bir türde uygulama ve yalnızca bir ASP.NET MVC uygulaması ile hizmet katmanı kullanmak üzere görebilmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-146">In theory, we should be able to use the service layer with any type of application and not only an ASP.NET MVC application.</span></span> <span data-ttu-id="f4dec-147">Örneğin, gelecekte, biz uygulamamız için ön uç WPF oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f4dec-147">For example, in the future, we might want to build a WPF front-end for our application.</span></span> <span data-ttu-id="f4dec-148">Bizim hizmet katmanından bir model durumu biz ASP.NET MVC bağımlılığı kaldırmak için bir yol bulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-148">We should find a way to remove the dependency on ASP.NET MVC model state from our service layer.</span></span>

<span data-ttu-id="f4dec-149">Model durumu artık kullanmasını sağlamak amacıyla listeleme 5'te hizmet katmanı güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="f4dec-149">In Listing 5, the service layer has been updated so that it no longer uses model state.</span></span> <span data-ttu-id="f4dec-150">Bunun yerine, IValidationDictionary arabirimini uygulayan herhangi bir sınıf kullanır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-150">Instead, it uses any class that implements the IValidationDictionary interface.</span></span>

<span data-ttu-id="f4dec-151">**5 - (ayrılmış) Models\ProductService.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-151">**Listing 5 - Models\ProductService.vb (decoupled)**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample5.vb)]

<span data-ttu-id="f4dec-152">IValidationDictionary arabirimi listeleme 6'tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-152">The IValidationDictionary interface is defined in Listing 6.</span></span> <span data-ttu-id="f4dec-153">Bu basit arabirim tek bir yöntem ve tek bir özellik vardır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-153">This simple interface has a single method and a single property.</span></span>

<span data-ttu-id="f4dec-154">**6 - Models\IValidationDictionary.cs listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-154">**Listing 6 - Models\IValidationDictionary.cs**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample6.vb)]

<span data-ttu-id="f4dec-155">Adlı ModelStateWrapper sınıfı 7 listeleme sınıfında IValidationDictionary arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="f4dec-155">The class in Listing 7, named the ModelStateWrapper class, implements the IValidationDictionary interface.</span></span> <span data-ttu-id="f4dec-156">Model durumu sözlüğü oluşturucusuna geçirerek ModelStateWrapper sınıfı örneğini oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f4dec-156">You can instantiate the ModelStateWrapper class by passing a model state dictionary to the constructor.</span></span>

<span data-ttu-id="f4dec-157">**7 - Models\ModelStateWrapper.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-157">**Listing 7 - Models\ModelStateWrapper.vb**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample7.vb)]

<span data-ttu-id="f4dec-158">Son olarak, güncelleştirilmiş denetleyicisi listeleme 8'de hizmet katmanın kurucusunda oluştururken ModelStateWrapper kullanır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-158">Finally, the updated controller in Listing 8 uses the ModelStateWrapper when creating the service layer in its constructor.</span></span>

<span data-ttu-id="f4dec-159">**8 - Controllers\ProductController.vb listeleme**</span><span class="sxs-lookup"><span data-stu-id="f4dec-159">**Listing 8 - Controllers\ProductController.vb**</span></span>

[!code-vb[Main](validating-with-a-service-layer-vb/samples/sample8.vb)]

<span data-ttu-id="f4dec-160">IValidationDictionary kullanarak arabirimi ve ModelStateWrapper sınıfı sağlar bize tamamen bizim hizmet katmanı bizim denetleyicisi katmandan yalıtmak.</span><span class="sxs-lookup"><span data-stu-id="f4dec-160">Using the IValidationDictionary interface and the ModelStateWrapper class enables us to completely isolate our service layer from our controller layer.</span></span> <span data-ttu-id="f4dec-161">Hizmet katmanı artık model durumuna bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f4dec-161">The service layer is no longer dependent on model state.</span></span> <span data-ttu-id="f4dec-162">Hizmet katmanı için IValidationDictionary arabirimini uygulayan herhangi bir sınıf geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f4dec-162">You can pass any class that implements the IValidationDictionary interface to the service layer.</span></span> <span data-ttu-id="f4dec-163">Örneğin, WPF uygulaması simple collection sınıfı IValidationDictionary arabirimiyle uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="f4dec-163">For example, a WPF application might implement the IValidationDictionary interface with a simple collection class.</span></span>

## <a name="summary"></a><span data-ttu-id="f4dec-164">Özet</span><span class="sxs-lookup"><span data-stu-id="f4dec-164">Summary</span></span>

<span data-ttu-id="f4dec-165">Bu öğreticinin amacı, bir ASP.NET MVC uygulamasındaki doğrulama gerçekleştirmek için bir yaklaşım tartışmak için oluştu.</span><span class="sxs-lookup"><span data-stu-id="f4dec-165">The goal of this tutorial was to discuss one approach to performing validation in an ASP.NET MVC application.</span></span> <span data-ttu-id="f4dec-166">Bu öğreticide, tüm doğrulama mantığınız denetleyicilerinizi dışında ve ayrı bir hizmet katmana taşımak öğrendiniz.</span><span class="sxs-lookup"><span data-stu-id="f4dec-166">In this tutorial, you learned how to move all of your validation logic out of your controllers and into a separate service layer.</span></span> <span data-ttu-id="f4dec-167">Ayrıca, hizmet katmanı denetleyicisi katmanından ModelStateWrapper sınıfı oluşturarak yalıtmak öğrendiniz.</span><span class="sxs-lookup"><span data-stu-id="f4dec-167">You also learned how to isolate your service layer from your controller layer by creating a ModelStateWrapper class.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="f4dec-168">[Önceki](validating-with-the-idataerrorinfo-interface-vb.md)
[sonraki](validation-with-the-data-annotation-validators-vb.md)</span><span class="sxs-lookup"><span data-stu-id="f4dec-168">[Previous](validating-with-the-idataerrorinfo-interface-vb.md)
[Next](validation-with-the-data-annotation-validators-vb.md)</span></span>
