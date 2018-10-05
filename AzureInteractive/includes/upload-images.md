---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 51c7d3e64424d499b473f3b138ce249a9cfd0182
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460083"
---
Az alkalmazás, amelyet létre fog hozni, egy fényképgaléria. Ügyféloldali JavaScriptet használ az API-k hívásához, amelyek segítségével feltölti és megjeleníti a képeket. Ez a modul egy kiszolgáló nélküli függvényt használó API létrehozását mutatja be, amely a fénykép feltöltéséhez időkorlátos URL-címet hoz létre. A webalkalmazás a létrehozott URL-cím segítségével tölti fel a képet a Blob Storage-ba a [Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) használatával.

## <a name="create-a-blob-storage-container-for-images"></a>Blob Storage-tároló létrehozása a képek számára

Az alkalmazásnak egy különálló Storage-tárolóra van szüksége a képek feltöltéséhez és tárolásához.

1. Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe (bash). Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához.

1.  Hozzon létre egy olyan új, **images** nevű tárolót a tárfiókban, amely nyilvános hozzáféréssel rendelkezik a blobokhoz.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

Az Azure Functions egy kiszolgáló nélküli függvényeket futtató szolgáltatás. A kiszolgáló nélküli függvényeket események aktiválhatják (hívhatják meg), például egy HTTP-kérés vagy egy blob létrejötte egy Storage-tárolóban.

Az Azure-függvényalkalmazás egy vagy több kiszolgáló nélküli függvény tárolója.

Hozzon létre egy új, egyéni névvel rendelkező Azure-függvényalkalmazást a korábban létrehozott, **first-serverless-app** elnevezésű erőforráscsoportban. A függvényalkalmazásoknak tárfiókra van szükségük. Ehhez az oktatóanyaghoz használja a már létező tárfiókot.

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

Az oktatóanyagban létrehozott függvényalkalmazáshoz a Functions futtatókörnyezet 1.x verziója szükséges. A `FUNCTIONS_WORKER_RUNTIME` alkalmazásbeállítás `~1` értékre állítása a legújabb 1.x verzióra rögzíti a függvényalkalmazást. Adja meg az alkalmazásbeállításokat az [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) paranccsal.

A következő Azure CLI-parancsban az <app_name> a függvényalkalmazás neve.

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_WORKER_RUNTIME=~1
```

## <a name="create-an-http-triggered-serverless-function"></a>Hozzon létre egy HTTP-eseményindítóval aktivált, kiszolgáló nélküli függvényt

A fényképgaléria-webalkalmazás HTTP-kérést hajt végre egy időkorlátos URL létrehozására a kiszolgáló nélküli függvény felé, amellyel biztonságosan töltheti fel a képet a Blob Storage-ba. A függvényt egy HTTP-kérés aktiválja, és az Azure Storage SDK-t használja a biztonságos URL létrehozásához és visszaadásához.

1. A létrehozása után keresse meg a függvényalkalmazást az Azure Portalon a keresőmező használatával, majd a megnyitásához kattintson rá.

    ![A függvényalkalmazás megnyitása](media/functions-first-serverless-web-app/2-search-function-app.png)

1. A függvényalkalmazás ablakának bal oldali navigációs sávjában helyezze a mutatót a **Függvények** elem fölé, és kattintson a **+** elemre egy új kiszolgáló nélküli függvény létrehozásának megkezdéséhez.

    ![Új függvény létrehozása](media/functions-first-serverless-web-app/2-new-function.png)

1. Kattintson az **Egyéni függvény** elemre a függvénysablonok listájának megtekintéséhez.

1. Keresse meg a **HttpTrigger** sablont, és válassza ki a használandó nyelvet (C# vagy JavaScript).

1. Használja az alábbi értékeket a függvény létrehozásához, amely a blob feltöltéséhez használt URL-t létrehozza.

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | ---|
    | **Nyelv** | C# vagy JavaScript | Válassza ki a használni kívánt nyelvet. |
    | **A függvény neve** | GetUploadUrl | Pontosan úgy írja be a nevet, ahogy itt látható, hogy az alkalmazás megtalálja a függvényt. |
    | **Engedélyszint** | Névtelen | Engedélyezze, hogy a függvény nyilvánosan elérhető legyen. |

    ![Adja meg egy új, HTTP-eseményindítóval aktivált függvény beállításait](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. A függvény létrehozásához kattintson a **Létrehozás** elemre.

1. **C#** 

    1. Amikor megjelenik a függvény forráskódja, cserélje le az összes **run.csx** elemet a [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx) tartalmára.

1. **JavaScript** 

    1. (JavaScript) Ennek a függvénynek szüksége van az npm-ből származó `azure-storage` csomagra a közös hozzáférésű jogosultságkód (SAS) token létrehozásához a biztonságos URL-cím összeállításakor. Az npm-csomag telepítéséhez kattintson a függvényalkalmazás nevére a bal oldali navigációs sávon, majd válassza a **Platformfunkciók** lehetőséget.

    1. (JavaScript) Kattintson a **Konzol** elemre a konzolablak megjelenítéséhez.

        ![A konzolablak megnyitása](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) A `cd d:\home\site\wwwroot` parancs futtatásával győződjön meg arról, hogy a **d:\home\site\wwwroot** az aktuális könyvtár.

    1. (JavaScript) Futtassa az `npm init -y` parancsot egy üres **package.json** fájl létrehozásához.

    1. (JavaScript) Futtassa az `npm install --save azure-storage` parancsot a konzolban a csomag telepítéséhez és mentéséhez a **package.json** fájlba. A művelet végrehajtása egy-két percet igénybe vehet.

    1. (JavaScript) Kattintson a (**GetUploadUrl**) függvény nevére a bal oldali navigációs sávban a függvény megjelenítéséhez, majd cserélje le az összes **index.js** elemet a [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js) tartalmára.
    
        ![index.js frissítés után](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.

1. Kattintson a **Save** (Mentés) gombra. A naplók panelen győződjön meg arról, hogy a függvény fordítása sikeres volt.

A függvény létrehozza az ún. közös hozzáférésű jogosultságkód (SAS) URL-jét, amelynek használatával fájlokat tölthet fel a Blob Storage-ba. A SAS URL csak egy rövid időtartamra érvényes, és egyetlen fájl feltöltésére használható. További információkért a [közös hozzáférésű jogosultságkód (SAS) használatáról](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) tekintse át a Blob Storage dokumentációját.


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Adjon egy környezeti változót a Storage kapcsolati sztringjéhez

Az imént létrehozott függvény egy kapcsolati sztringet igényel a Storage-fiókhoz, hogy létrehozhassa a SAS URL-t. A kapcsolati sztring alkalmazásbeállításként is tárolható, nem szükséges a függvényben rögzíteni. Az alkalmazásbeállításokat a környezeti változókhoz hasonlóan a függvényalkalmazásokból érik el a függvények.

1. A Cloud Shellben kérdezze le a Storage-fiók kapcsolati sztringjét, majd mentse bash változóként **STORAGE_CONNECTION_STRING** néven.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Győződjön meg arról, hogy a változó beállítása sikeres volt.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Hozzon létre új egy alkalmazásbeállítást **AZURE_STORAGE_CONNECTION_STRING** néven az előző lépésben mentett érték felhasználásával.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Győződjön meg arról, hogy a parancs kimenete tartalmazza az új alkalmazásbeállítást a helyes értékkel együtt.


## <a name="test-the-serverless-function"></a>A kiszolgáló nélküli függvény tesztelése

A függvények létrehozása és szerkesztése mellett az Azure Portal beépített eszközöket is biztosít azok teszteléséhez.

1. A HTTP kiszolgáló nélküli függvény teszteléséhez kattintson a kódablak jobb oldalán a **Tesztelés** gombra, amely kibontja a tesztelési panelt.

1. Cserélje a **HTTP-metódust** a **GET-metódusra**.

1. A **Lekérdezés** menüpont alatt kattintson a *Paraméter hozzáadása** elemre, és adja hozzá a következő paramétereket:

    | név      |  érték   | 
    | --- | --- |
    | fájlnév | image1.jpg |

1. Kattintson a **Futtatás** menüpontra a tesztelési panelen, hogy HTTP-kérést küldjön a függvénynek.

1. A függvény a kimenetén egy, a feltöltéshez használható URL-címmel tér vissza. A naplók panelen megjelenik a függvényvégrehajtás.

    ![A függvény sikeres lefutását jelző naplók](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>A CORS konfigurálása a függvényalkalmazásban

Mivel az alkalmazás előtere a Blob Storage-ban üzemel, a tartományneve eltér az Azure-függvényalkalmazásétól. Hogy az ügyféloldali JavaScript sikeresen meg tudja hívni az imént létrehozott függvényt, a függvényalkalmazást konfigurálnia kell az eltérő eredetű erőforrások megosztásához (CORS).

1. A függvényalkalmazás ablak bal oldali navigációs sávjában kattintson a függvényalkalmazás nevére.

1. Kattintson a **Platformfunkciók** lehetőségre a speciális funkciók listájának megtekintéséhez.

1. Az **API** menüpont alatt válassza a **CORS** lehetőséget.

    ![CORS kiválasztása](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Adjon egy engedélyezett forrást az alkalmazás URL-jéhez az előző modulból a zárás elhagyásával **/** (például: `https://firstserverlessweb.z4.web.core.windows.net`).

    ![A CORS-beállítások jelzik, hogy kiszolgáló nélküli webalkalmazás URL-je lett hozzáadva](media/functions-first-serverless-web-app/2-add-cors.png)

1. Kattintson a **Save** (Mentés) gombra.

1. C#

   1. (C#) Lépjen vissza a `GetUploadUrl` függvényre, majd válassza az **Integrálás** lapot.

   1. (C#) A Kiválasztott HTTP-metódusok területen alatt válassza ki az **OPTIONS** lehetőséget.

      A **GET**, **POST** és **OPTIONS** mindegyikét jelölje ki. A CORS az OPTIONS metódust használja, ez alapértelmezés szerint nincs kiválasztva a C# függvényekhez.  

   1. (C#) Kattintson a **Mentés** gombra.

1. A portálon navigáljon a függvényalkalmazásra, válassza az **Áttekintés** lapot, majd kattintson az **Újraindítás** gombra, és győződjön meg arról, hogy a CORS módosításai érvénybe léptek.

## <a name="configure-cors-in-the-storage-account"></a>A CORS konfigurálása a Storage-fiókban

Mivel az alkalmazás a fájlok feltöltéséhez ügyféloldali JavaScript-hívásokat intéz a Blob Storage-hoz, konfigurálnia kell a Storage-fiókot a CORS-hoz.

1. Futtassa a következő parancsot, hogy az összes forrásról engedélyezze a Storage-fiókba való fájlfeltöltést.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>A webalkalmazás módosítása képek feltöltéséhez

A webalkalmazás a beállításokat a **settings.js** fájlból kéri le. A következő lépésekben hozzon létre egy fájlt a Cloud Shell segítségével, majd állítsa be a `window.apiBaseUrl` értéket a függvényalkalmazás URL-címéhez és a `window.blobBaseUrl` értéket az Azure Blob Storage-végpont URL-jéhez.

1. Győződjön meg róla a Cloud Shellben, hogy a **www/dist** mappa az aktuális könyvtár.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Kérdezze le a függvényalkalmazás URL-jét, és tárolja egy bash változóban **FUNCTION_APP_URL** néven.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Győződjön meg arról, hogy a változó helyesen lett beállítva.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Az API-hívások kiindulási URI-ját a következőképpen tudja beállítani a függvényalkalmazásokhoz: hozza létre a **settings.js** fájlt, és adja hozzá a függvényalkalmazás URL-címét a következők szerint.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    Elvégezheti a módosítást az alábbi parancs futtatásával, vagy egy parancssor-szerkesztővel, például a VIM-mel.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Győződjön meg arról, hogy a fájl írása sikeres volt.

    ```azurecli
    cat settings.js
    ```

1. Kérdezze le a Blob Storage kiindulási URL-címét, és tárolja egy bash változóban **BLOB_BASE_URL** néven.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Győződjön meg arról, hogy a változó helyesen lett beállítva.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Az API-hívások kiindulási URI-ját a következőképpen tudja beállítani a függvényalkalmazásokhoz: fűzze hozzá a tároló URL-jét a következő kódsor szerint a **settings.js** fájlhoz.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    Elvégezheti a módosítást az alábbi parancs futtatásával, vagy egy parancssor-szerkesztővel, például a VIM-mel.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Győződjön meg arról, hogy a fájl írása sikeres volt, és már 2 sort tartalmaz.

    ```azurecli
    cat settings.js
    ```

1. Töltse fel a fájlt a Blob Storage-ba.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>A webalkalmazás tesztelése

Ezen a ponton a katalógusbeli alkalmazás fel tud tölteni egy képet a Blob Storage-ba, de a képek megjelenítésére még nem alkalmas. Megpróbál meghívni egy `GetImages` függvényt, amely még nem létezik, mert létrehozására majd egy későbbi modulban kerül sor. Ez a hívás meghiúsul, és úgy tűnik, hogy a weblap az „Elemzés...” állapotban elakad, a választott kép feltöltése azonban sikerül.

Az Azure Portal **images** tárolójának megtekintésével meggyőződhet arról, hogy a kép feltöltése sikeres volt.

1. Egy böngészőablakban keresse meg az alkalmazást. Válasszon ki egy képfájlt, és töltse fel. A feltöltés befejeződött, de mivel még nem adtuk hozzá a képmegjelenítési képességet, az alkalmazás nem jeleníti meg a feltöltött képet. (Úgy tűnik, hogy a weblap a „Kép elemzése...” állapotban elakad, ezt később javíthatja ki.)

1. A Cloud Shellben erősítse meg, hogy a kép fel lett töltve az **images** tárolóba.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Mielőtt továbblépne a következő oktatóanyagra, törölje az összes fájlt az **images** tárolóból.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Összegzés

Ebben a modulban létrehozhatott egy Azure-függvényalkalmazást, és megtanulhatta, hogyan engedélyezheti a webalkalmazásoknak egy kiszolgáló nélküli függvény használatával, hogy képeket töltsenek fel a Blob Storage-ba. Ezután megtanulhatja, hogyan hozhat létre miniatűröket a feltöltött képekhez egy Blob-eseményindítóval aktivált, kiszolgáló nélküli függvény használatával.
