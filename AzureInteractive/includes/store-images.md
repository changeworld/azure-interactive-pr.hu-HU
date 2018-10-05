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
ms.openlocfilehash: 194a25dbf9abda80379aa5aab408ac4ffe9ab7f5
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460058"
---
Az Azure Cosmos DB a Microsoft kiszolgáló nélküli, globálisan elosztott, többmodelles adatbázis-szolgáltatása. Ebben a modulban elsajátíthatja, hogyan tárolhat és kérhet le képmetaadatokat a Cosmos DB-ben tárolt JSON-dokumentumokban az Azure Functions használatával.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Cosmos DB-fiók, -adatbázis és -gyűjtemény létrehozása

A Cosmos DB-fiók olyan Azure-erőforrás, amely Cosmos DB-adatbázisokat tartalmaz.

1. Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe.  Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához. 

1. Hozzon létre egy Cosmos DB-fiókot egyedi néven ugyanabban az erőforráscsoportban, amelyben az oktatóanyag többi erőforrása található.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. A Cosmos DB-fiók létrehozása után hozzon létre egy új adatbázist **imagesdb** néven a fiókban.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Az adatbázis létrehozása után hozzon létre az adatbázisban egy **images** nevű új gyűjteményt 400 kérelemegység (RU) átviteli sebességgel.

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Dokumentum mentése a Cosmos DB-be miniatűr létrehozásakor

A Cosmos DB kimeneti kötésével dokumentumokat hozhat létre egy Cosmos DB-gyűjteményben az Azure Functionsből. A következő lépésekben konfigurálni fog egy Cosmos DB kimeneti kötést a **ResizeImage** függvényben, és módosítja a függvényt, hogy egy mentendő dokumentumot (objektumot) adjon vissza.

1. Nyissa meg a függvényalkalmazást az Azure Portalon.

1. A bal oldali menüben bontsa ki a **ResizeImage** függvényt, majd válassza az **Integrálás** lehetőséget.

1. A **Kimenetek** területen kattintson az **Új kimenet** elemre.

1. Keresse meg az **Azure Cosmos DB** elemet, és jelölje ki. Ezután kattintson a **Kiválasztás** elemre.

    ![Új kimenet kiválasztása](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Az **Azure Cosmos DB-kimenet** szakasz mezőiben adja meg a következő értékeket.

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | ---|
    | **Dokumentumparaméter neve** | Válassza **A függvény visszaadott értékének használata** lehetőséget | A szövegmező értéke automatikusan a **$return** értéket veszi fel. |
    | **Adatbázis neve** | imagesdb | Használja a létrehozott adatbázis nevét. |
    | **Gyűjtemény neve** | images | Használja a létrehozott gyűjtemény nevét. |

1. Az **Azure Cosmos DB-fiók kapcsolata** elem mellett kattintson az **új** elemre. Válassza ki a korábban létrehozott Cosmos DB-fiókot.

    ![Az Azure Cosmos DB kimeneti kötés beállításainak megadása](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. A Cosmos DB kimeneti kötésének létrehozásához kattintson a **Mentés** gombra.

1. Kattintson a bal oldalon a **ResizeImage** függvény nevére a függvény megnyitásához.

1. **C#**

    1. (C#) Módosítsa a függvény visszatérési típusát **void** típusról **object** típusra.

    1. (C#) Adja hozzá a következő kódot a függvény végéhez, hogy a mentendő dokumentumot adja vissza:
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![A ResizeImages függvény run.csx fájlja a módosítások után](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Módosítsa a `else` záradék `context.done()` utasítását, hogy küldje vissza a menteni kívánt dokumentumot a Cosmos DB-be.

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.

1. Kattintson a **Save** (Mentés) gombra. Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Függvény létrehozása a Cosmos DB-ben található képek listázására

A webalkalmazáshoz egy API szükséges, amely lekéri a képek metaadatait a Cosmos DB-ből. A következő lépésekben létrehozhat egy HTTP-n aktivált függvényt, amely egy Cosmos DB bemeneti kötéssel kérdezi le az adatbázis-gyűjteményt.

1. A függvényalkalmazásban vigye a mutatót a **Függvények** elem fölé a bal oldalon, és a **+** elemre kattintva hozzon létre egy új függvényt.

1. Keresse meg a **HttpTrigger** sablont, és válassza ki.

1. Az alábbi értékekkel hozzon létre egy függvényt, amely előállít egy URL-címet a képek lekéréséhez.

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | ---|
    | **A függvény neve** | GetImages | Pontosan úgy írja be a nevet, ahogy itt látható, hogy az alkalmazás megtalálja a függvényt. |
    | **Engedélyszint** | Névtelen | Engedélyezze, hogy a függvény nyilvánosan elérhető legyen. |

1. Kattintson a **Create** (Létrehozás) gombra.

1. Az új függvény létrehozása után kattintson az **Integrálás** elemre a függvény neve alatt a bal oldali menüben.

1. Kattintson az **Új bemenet** elemre, majd válassza az **Azure Cosmos DB** lehetőséget. 

    ![Új bemenet kiválasztása](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Kattintson a **Kiválasztás** gombra.

1. Adja meg az alábbi értékeket:

    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | ---|
    | **Dokumentumparaméter neve** | documents | Egyezik a paraméter nevével a függvényben. |
    | **Adatbázis neve** | imagesdb |  |
    | **Gyűjtemény neve** | images |  |
    | **SQL-lekérdezés** | select * from c order by c._ts desc | A dokumentumok beolvasása, először a legutóbbiaké. |
    | **Azure Cosmos DB-fiók kapcsolata** | Meglévő kapcsolati sztring kiválasztása |  |

1. A bemeneti kötés létrehozásához kattintson a **Mentés** elemre.

1. **C#**

    1. A függvény nevére kattintva nyissa meg a kódablakot, és cserélje le a **run.csx** teljes tartalmát a [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx) tartalmára.

1. **JavaScript**

    1. A függvény nevére kattintva nyissa meg a kódablakot, és cserélje le az **index.js** teljes tartalmát a [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js) tartalmára.

1. Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.

1. Kattintson a **Save** (Mentés) gombra. Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.


## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. Nyissa meg az alkalmazást egy böngészőben. Válasszon ki egy képfájlt, és töltse fel.

1. Néhány másodperc múlva az új kép miniatűrje megjelenik a lapon.

1. Az Azure Portalon a keresőmezőben keresse meg név alapján a Cosmos DB-fiókot. Kattintson rá a megnyitásához.

1. A bal oldalon kattintson az **Adatkezelőre**, amelyben a gyűjtemények és dokumentumok között tallózhat.

1. Az **imagesdb** adatbázis alatt válassza ki az **images** gyűjteményt.

1. Győződjön meg arról, hogy létrejött egy dokumentum a feltöltött képhez.

    ![Egy feltöltött képhez tartozó dokumentum az Adatkezelőben](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Összegzés

Ebben a modulban elsajátította, hogyan hozhat létre Cosmos DB-fiókokat, -adatbázisokat és -gyűjteményeket. Emellett megtanulta azt is, hogyan mentheti és olvashatja be a képek metaadatait a Cosmos DB-gyűjteményben a Cosmos DB kötéseivel. Ezután megtanulhatja, hogyan hozhat létre feliratokat automatikusan mindegyik feltöltött képhez a Microsoft Cognitive Services használatával.
