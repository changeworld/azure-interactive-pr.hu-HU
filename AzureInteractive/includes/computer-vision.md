---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079496"
---
<span data-ttu-id="d8fc8-103">Ezen a ponton az alkalmazás egy funkcionális gyűjtemény, amely lehetővé teszi képek feltöltését és megtekintését.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="d8fc8-104">Ebben a modulban megismerheti a Microsoft Cognitive Services Computer Vision API-jának használatát, amellyel feliratokat hozhat létre a feltöltött képekhez, és mentheti ezeket a feliratokat a kép metaadataival a Cosmos DB-ben.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="d8fc8-105">Computer Vision-fiók létrehozása</span><span class="sxs-lookup"><span data-stu-id="d8fc8-105">Create a Computer Vision account</span></span>

<span data-ttu-id="d8fc8-106">A Microsoft Cognitive Services a fejlesztők számára elérhető szolgáltatásgyűjtemény, amellyel intelligensebbé tehetik az alkalmazásaikat.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="d8fc8-107">A Computer Vision API egy kiszolgáló nélküli szolgáltatás, amely speciális algoritmusok használatával feldolgozza a képeket, majd információkat ad vissza róluk.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="d8fc8-108">A szolgáltatás ingyenes szinttel is rendelkezik, amely havonta akár 5000 API-hívást tesz lehetővé.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="d8fc8-109">Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="d8fc8-110">Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="d8fc8-111">Hozzon létre egy új, **Computer Vision** altípusú, egyéni névvel rendelkező Cognitive Services-fiókot az erőforráscsoportban.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="d8fc8-112">Az ingyenes szinten használja az **F0** SKU értéket.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="d8fc8-113">Ha már van Computer Vision-fiókja, lehet, hogy létre kell hoznia egy Standard fiókot (S1), ami költségekkel járhat.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="d8fc8-114">Függvényalkalmazás beállításainak létrehozása a Computer Vision URL-címéhez és kulcsához</span><span class="sxs-lookup"><span data-stu-id="d8fc8-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="d8fc8-115">A Computer Vision API meghívásához URL-cím és kulcs szükséges.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="d8fc8-116">Szerezze be a Computer Vision API-kulcsát és URL-címét, majd mentse ezeket Bash-változókba.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="d8fc8-117">Hozzon létre **COMP_VISION_KEY** és **COMP_VISION_URL** nevű alkalmazásbeállításokat a függvényalkalmazásban.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="d8fc8-118">A Computer Vision API meghívása a ResizeImage függvényből</span><span class="sxs-lookup"><span data-stu-id="d8fc8-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="d8fc8-119">A következő lépések során a **ResizeImage** függvény módosításával meghívja a Computer Vision API-t, amely leírja a feltöltött képeket, és menti a leírást a Cosmos DB-ben.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="d8fc8-120">Nyissa meg a függvényalkalmazást az Azure Portalon.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="d8fc8-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="d8fc8-121">**C#**</span></span>

    1. <span data-ttu-id="d8fc8-122">(C#) A bal oldali navigációs sávval keresse meg a **ResizeImage** függvényt, és nyissa meg a hozzá tartozó kódablakot.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="d8fc8-123">(C#) Cserélje le a kódot a [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx) fájl tartalmára.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="d8fc8-124">Ez a kód a `HttpClient` használatával meghívja a Computer Vision API-t, majd menti az eredményeket a Cosmos DB-ben.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="d8fc8-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="d8fc8-125">**JavaScript**</span></span>

    1. <span data-ttu-id="d8fc8-126">(JavaScript) Ehhez a függvényhez szükség van az npm-ből származó `axios` csomagra a Computer Vision API felé indított HTTP-híváshoz.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="d8fc8-127">Az npm-csomag telepítéséhez kattintson a függvényalkalmazás nevére a bal oldali navigációs sávon, majd válassza a **Platformfunkciók** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="d8fc8-128">(JavaScript) Kattintson a **Konzol** elemre a konzolablak megjelenítéséhez.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="d8fc8-129">(JavaScript) Futtassa az `npm install axios` parancsot a konzolban.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="d8fc8-130">A művelet végrehajtása egy-két percet igénybe vehet.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="d8fc8-131">(JavaScript) A függvény megjelenítéséhez kattintson a függvény nevére (**ResizeImage**) a bal oldali navigációs sávon, majd cserélje le az **index.js** teljes tartalmát a [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js) tartalmára.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="d8fc8-132">Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="d8fc8-133">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-133">Click **Save**.</span></span> <span data-ttu-id="d8fc8-134">Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="d8fc8-135">Az alkalmazás tesztelése</span><span class="sxs-lookup"><span data-stu-id="d8fc8-135">Test the application</span></span>

1. <span data-ttu-id="d8fc8-136">Nyissa meg az alkalmazást egy böngészőben.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-136">Open the application in a browser.</span></span> <span data-ttu-id="d8fc8-137">Válasszon ki egy képfájlt, és töltse fel.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="d8fc8-138">Néhány másodperc múlva az új kép miniatűrje megjelenik a lapon.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="d8fc8-139">Vigye a mutatót a kép fölé a Computer Vision által létrehozott leírás megtekintéséhez.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="d8fc8-140">Összegzés</span><span class="sxs-lookup"><span data-stu-id="d8fc8-140">Summary</span></span>

<span data-ttu-id="d8fc8-141">Ebben a modulban megismerte, hogyan hozhat létre automatikusan feliratokat a feltöltött képekhez a Microsoft Cognitive Services Computer Vision API-jával.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="d8fc8-142">A következőkben megismerheti, hogyan adhat hozzá hitelesítést az alkalmazáshoz az Azure App Service-hitelesítéssel.</span><span class="sxs-lookup"><span data-stu-id="d8fc8-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>