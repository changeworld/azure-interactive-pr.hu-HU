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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079356"
---
Az előző modul bemutatta, hogyan segítheti elő egy kiszolgáló nélküli függvény a képek biztonságos feltöltését egy webalkalmazásból a Blob Storage-ba. Ebben a modulban egy másik kiszolgáló nélküli függvényt hoz létre, amely felügyeli a feltöltött képeket és létrehozza a miniatűrjeiket.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Blob Storage-tároló létrehozása a miniatűrök számára

A teljes méretű képeket az **images** nevű tárolóban tárolja a rendszer. A képek miniatűrjeinek tárolására egy másik tárolóra van szükség.

1. Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe (bash).  Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához. 

1. Hozzon létre egy olyan új, **thumbnails** nevű tárolót a tárfiókban, amely nyilvános hozzáféréssel rendelkezik a blobokhoz.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Blob által aktivált, kiszolgáló nélküli függvény létrehozása

A trigger határozza meg a függvény meghívásának módját. A következőként létrehozott függvény blobtriggert használ: a rendszer automatikusan meghívja a függvényt, amikor feltölt egy blobot (képfájlt) az **images** tárolóba. Minden függvénynek egy triggerrel kell rendelkeznie. A triggerekhez társított adatok tartoznak, amelyek általában a függvényt aktiváló hasznos adatok.

A kötések határozzák meg, hogyan olvas vagy ír adatokat a függvény az Azure-ban vagy a külső szolgáltatásokban. Ez a függvény létrehozza az aktiváló kép miniatűr verzióját, és menti a miniatűrt a *thumbnails* tárolóba.

1. Nyissa meg a függvényalkalmazást az Azure Portalon.

1. A függvényalkalmazás ablakának bal oldali navigációs sávjában helyezze a mutatót a **Függvények** elem fölé, és kattintson a **+** elemre egy új kiszolgáló nélküli függvény létrehozásának megkezdéséhez. Ha megjelenik a gyors létrehozási lap, kattintson az **Egyéni függvény** elemre a függvénysablonok listájának megtekintéséhez.

1. Keresse meg a **BlobTrigger** sablont, majd válassza ki.

1. Használja az alábbi értékeket egy olyan függvény létrehozásához, amely miniatűröket hoz létre a feltöltött képekről.

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | ---|
    | **Nyelv** | C# vagy JavaScript | Válassza ki a kívánt nyelvet. |
    | **A függvény neve** | ResizeImage | Pontosan úgy írja be a nevet, ahogy itt látható, hogy az alkalmazás megtalálja a függvényt. |
    | **Elérési út** | images/{name} | Hajtsa végre a függvényt, amikor megjelenik egy fájl az **images** tárolóban. |
    | **Tárfiókkal kapcsolatos információk** | AZURE_STORAGE_CONNECTION_STRING | Használja a korábban létrehozott környezeti változót a kapcsolati sztringgel. |

    ![Az új függvény beállításainak megadása](media/functions-first-serverless-web-app/3-new-function.png)

1. A függvény létrehozásához kattintson a **Létrehozás** elemre.

1. Ha a függvény létrejött, kattintson az **Integrálás** elemre a trigger, a bemeneti és a kimeneti kötések megtekintéséhez.

1. Új kimeneti kötés létrehozásához kattintson az **Új kimenet** elemre.

    ![Az Integrálás lapon válassza az Új kimenet lehetőséget](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Kiválasztás** elemre. Lehetséges, hogy le kell görgetnie, hogy a **Kiválasztás** gomb láthatóvá váljon.

    ![Válassza az Azure Blob Storage lehetőséget](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Írja be a következő értékeket.

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | ---|
    | **Blobparaméter neve** | miniatűr | A függvény az ilyen nevű paramétert használja a miniatűrök írásához. |
    | **A függvény visszaadott értékének használata** | Nem |  |
    | **Elérési út** | thumbnails/{name} | A rendszer a **thumbnails** nevű tárolóba írja a miniatűröket. |
    | **Tárfiók kapcsolata** | AZURE_STORAGE_CONNECTION_STRING | Használja a korábban létrehozott környezeti változót a kapcsolati sztringgel. |

    ![A Blob kimeneti kötés beállításainak megadása](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Az ablak jobb felső sarkában kattintson a **Speciális szerkesztő** elemre a kötéseket jelölő JSON megjelenítéséhez.

    1. (JavaScript) A `blobTrigger` kötésben adjon hozzá egy `dataType` nevű tulajdonságot `binary` értékkel. Ez úgy konfigurálja a kötést, hogy a blobok tartalmát bináris adatokként küldje el a függvénynek.

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. Az új kötés létrehozásához kattintson a **Mentés** gombra.

1. **C#**

    1. (C#) Válassza a **ResizeImage** függvénynevet a bal oldali navigációs sávon a függvény forráskódjának megnyitásához.

    1. (C#) A függvényhez szükség van egy **ImageResizer** nevű NuGet-csomagra, amely létrehozza a miniatűröket. A rendszer egy **project.json** fájllal adja hozzá a NuGet-csomagokat a C#-függvényhez. A fájl létrehozásához kattintson a **Fájlok megtekintése** elemre a jobb oldalon, hogy megjelenjenek a függvényt alkotó fájlok.
    
    1. (C#) Kattintson a **Hozzáadás** elemre egy **project.json** nevű új fájl hozzáadásához.
    
    1. (C#) Másolja az újonnan létrehozott fájlba a [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) fájl tartalmát. Mentse a fájlt. A rendszer automatikusan visszaállítja a csomagokat a fájl feltöltésekor.
    
        ![a project.json fájl az ImageResizerrel](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) Válassza a **run.csx** lehetőséget a **Fájlok megtekintése** területen, és cserélje le a tartalmát a [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx) tartalmára.

1. **JavaScript** 

    1. (JavaScript) A függvényhez szükség van az npm-ből származó `jimp` csomagra a fénykép átméretezéséhez. Az npm-csomag telepítéséhez kattintson a függvényalkalmazás nevére a bal oldali navigációs sávon, majd válassza a **Platformfunkciók** lehetőséget.

    1. (JavaScript) Kattintson a **Konzol** elemre a konzolablak megjelenítéséhez.

    1. (JavaScript) Futtassa az `npm install jimp` parancsot a konzolban. A művelet végrehajtása egy-két percet igénybe vehet.

    1. (JavaScript) A függvény megjelenítéséhez kattintson a **ResizeImage** függvénynévre a bal oldali navigációs sávon, majd cserélje le az **index.js** teljes tartalmát a [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js) tartalmára.

1. Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.

1. Kattintson a **Save** (Mentés) gombra. Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.


## <a name="test-the-serverless-function"></a>A kiszolgáló nélküli függvény tesztelése

1. Nyissa meg az alkalmazást egy böngészőben. Válasszon ki egy képfájlt, és töltse fel. A feltöltés befejeződött, de mivel még nem adtuk hozzá a képmegjelenítési képességet, az alkalmazás nem jeleníti meg a feltöltött képet.

1. A Cloud Shellben erősítse meg, hogy a kép fel lett töltve az **images** tárolóba.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Győződjön meg arról, hogy a miniatűr létrejött a **thumbnails** tárolóban.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Szerezze be a miniatűr URL-címét.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Nyissa meg az URL-címet egy böngészőben, és győződjön meg arról, hogy a miniatűr megfelelően létrejött.

1. Mielőtt továbblépne a következő oktatóanyagra, törölje az **images** és a **thumbnails** tárolókban lévő fájlokat.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Összegzés

Ebben a modulban létrehozott egy kiszolgáló nélküli függvényt, amely miniatűröket hoz létre, amikor feltölt egy képet egy Blob Storage-tárolóba. A következő oktatóanyagból megtudhatja, hogyan használhatja az Azure Cosmos DB-t a képek metaadatainak tárolására és listázására.
