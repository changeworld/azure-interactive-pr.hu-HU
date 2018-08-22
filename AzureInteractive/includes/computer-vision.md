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
Ezen a ponton az alkalmazás egy funkcionális gyűjtemény, amely lehetővé teszi képek feltöltését és megtekintését. Ebben a modulban megismerheti a Microsoft Cognitive Services Computer Vision API-jának használatát, amellyel feliratokat hozhat létre a feltöltött képekhez, és mentheti ezeket a feliratokat a kép metaadataival a Cosmos DB-ben.

## <a name="create-a-computer-vision-account"></a>Computer Vision-fiók létrehozása

A Microsoft Cognitive Services a fejlesztők számára elérhető szolgáltatásgyűjtemény, amellyel intelligensebbé tehetik az alkalmazásaikat. A Computer Vision API egy kiszolgáló nélküli szolgáltatás, amely speciális algoritmusok használatával feldolgozza a képeket, majd információkat ad vissza róluk. A szolgáltatás ingyenes szinttel is rendelkezik, amely havonta akár 5000 API-hívást tesz lehetővé.

1. Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe. Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához. 

1. Hozzon létre egy új, **Computer Vision** altípusú, egyéni névvel rendelkező Cognitive Services-fiókot az erőforráscsoportban. Az ingyenes szinten használja az **F0** SKU értéket. Ha már van Computer Vision-fiókja, lehet, hogy létre kell hoznia egy Standard fiókot (S1), ami költségekkel járhat.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Függvényalkalmazás beállításainak létrehozása a Computer Vision URL-címéhez és kulcsához

A Computer Vision API meghívásához URL-cím és kulcs szükséges.

1. Szerezze be a Computer Vision API-kulcsát és URL-címét, majd mentse ezeket Bash-változókba.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Hozzon létre **COMP_VISION_KEY** és **COMP_VISION_URL** nevű alkalmazásbeállításokat a függvényalkalmazásban.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>A Computer Vision API meghívása a ResizeImage függvényből

A következő lépések során a **ResizeImage** függvény módosításával meghívja a Computer Vision API-t, amely leírja a feltöltött képeket, és menti a leírást a Cosmos DB-ben.

1. Nyissa meg a függvényalkalmazást az Azure Portalon.

1. **C#**

    1. (C#) A bal oldali navigációs sávval keresse meg a **ResizeImage** függvényt, és nyissa meg a hozzá tartozó kódablakot.

    1. (C#) Cserélje le a kódot a [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx) fájl tartalmára. Ez a kód a `HttpClient` használatával meghívja a Computer Vision API-t, majd menti az eredményeket a Cosmos DB-ben.

1. **JavaScript**

    1. (JavaScript) Ehhez a függvényhez szükség van az npm-ből származó `axios` csomagra a Computer Vision API felé indított HTTP-híváshoz. Az npm-csomag telepítéséhez kattintson a függvényalkalmazás nevére a bal oldali navigációs sávon, majd válassza a **Platformfunkciók** lehetőséget.

    1. (JavaScript) Kattintson a **Konzol** elemre a konzolablak megjelenítéséhez.

    1. (JavaScript) Futtassa az `npm install axios` parancsot a konzolban. A művelet végrehajtása egy-két percet igénybe vehet.

    1. (JavaScript) A függvény megjelenítéséhez kattintson a függvény nevére (**ResizeImage**) a bal oldali navigációs sávon, majd cserélje le az **index.js** teljes tartalmát a [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js) tartalmára.

1. Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.

1. Kattintson a **Save** (Mentés) gombra. Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.


## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. Nyissa meg az alkalmazást egy böngészőben. Válasszon ki egy képfájlt, és töltse fel.

1. Néhány másodperc múlva az új kép miniatűrje megjelenik a lapon. Vigye a mutatót a kép fölé a Computer Vision által létrehozott leírás megtekintéséhez.


## <a name="summary"></a>Összegzés

Ebben a modulban megismerte, hogyan hozhat létre automatikusan feliratokat a feltöltött képekhez a Microsoft Cognitive Services Computer Vision API-jával. A következőkben megismerheti, hogyan adhat hozzá hitelesítést az alkalmazáshoz az Azure App Service-hitelesítéssel.