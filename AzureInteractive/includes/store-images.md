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
<span data-ttu-id="7d3e7-103">Az Azure Cosmos DB a Microsoft kiszolgáló nélküli, globálisan elosztott, többmodelles adatbázis-szolgáltatása.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="7d3e7-104">Ebben a modulban elsajátíthatja, hogyan tárolhat és kérhet le képmetaadatokat a Cosmos DB-ben tárolt JSON-dokumentumokban az Azure Functions használatával.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="7d3e7-105">Cosmos DB-fiók, -adatbázis és -gyűjtemény létrehozása</span><span class="sxs-lookup"><span data-stu-id="7d3e7-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="7d3e7-106">A Cosmos DB-fiók olyan Azure-erőforrás, amely Cosmos DB-adatbázisokat tartalmaz.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="7d3e7-107">Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="7d3e7-108">Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="7d3e7-109">Hozzon létre egy Cosmos DB-fiókot egyedi néven ugyanabban az erőforráscsoportban, amelyben az oktatóanyag többi erőforrása található.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="7d3e7-110">A Cosmos DB-fiók létrehozása után hozzon létre egy új adatbázist **imagesdb** néven a fiókban.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="7d3e7-111">Az adatbázis létrehozása után hozzon létre az adatbázisban egy **images** nevű új gyűjteményt 400 kérelemegység (RU) átviteli sebességgel.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="7d3e7-112">Dokumentum mentése a Cosmos DB-be miniatűr létrehozásakor</span><span class="sxs-lookup"><span data-stu-id="7d3e7-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="7d3e7-113">A Cosmos DB kimeneti kötésével dokumentumokat hozhat létre egy Cosmos DB-gyűjteményben az Azure Functionsből.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="7d3e7-114">A következő lépésekben konfigurálni fog egy Cosmos DB kimeneti kötést a **ResizeImage** függvényben, és módosítja a függvényt, hogy egy mentendő dokumentumot (objektumot) adjon vissza.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="7d3e7-115">Nyissa meg a függvényalkalmazást az Azure Portalon.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="7d3e7-116">A bal oldali menüben bontsa ki a **ResizeImage** függvényt, majd válassza az **Integrálás** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="7d3e7-117">A **Kimenetek** területen kattintson az **Új kimenet** elemre.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="7d3e7-118">Keresse meg az **Azure Cosmos DB** elemet, és jelölje ki.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="7d3e7-119">Ezután kattintson a **Kiválasztás** elemre.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-119">Then click **Select**.</span></span>

    ![Új kimenet kiválasztása](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="7d3e7-121">Az **Azure Cosmos DB-kimenet** szakasz mezőiben adja meg a következő értékeket.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="7d3e7-122">Beállítás</span><span class="sxs-lookup"><span data-stu-id="7d3e7-122">Setting</span></span>      |  <span data-ttu-id="7d3e7-123">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="7d3e7-123">Suggested value</span></span>   | <span data-ttu-id="7d3e7-124">Leírás</span><span class="sxs-lookup"><span data-stu-id="7d3e7-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="7d3e7-125">**Dokumentumparaméter neve**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-125">**Document parameter name**</span></span> | <span data-ttu-id="7d3e7-126">Válassza **A függvény visszaadott értékének használata** lehetőséget</span><span class="sxs-lookup"><span data-stu-id="7d3e7-126">Select **Use function return value**</span></span> | <span data-ttu-id="7d3e7-127">A szövegmező értéke automatikusan a **$return** értéket veszi fel.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="7d3e7-128">**Adatbázis neve**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-128">**Database name**</span></span> | <span data-ttu-id="7d3e7-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="7d3e7-129">imagesdb</span></span> | <span data-ttu-id="7d3e7-130">Használja a létrehozott adatbázis nevét.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="7d3e7-131">**Gyűjtemény neve**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-131">**Collection name**</span></span> | <span data-ttu-id="7d3e7-132">images</span><span class="sxs-lookup"><span data-stu-id="7d3e7-132">images</span></span> | <span data-ttu-id="7d3e7-133">Használja a létrehozott gyűjtemény nevét.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="7d3e7-134">Az **Azure Cosmos DB-fiók kapcsolata** elem mellett kattintson az **új** elemre.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="7d3e7-135">Válassza ki a korábban létrehozott Cosmos DB-fiókot.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-135">Select the Cosmos DB account you previously created.</span></span>

    ![Az Azure Cosmos DB kimeneti kötés beállításainak megadása](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="7d3e7-137">A Cosmos DB kimeneti kötésének létrehozásához kattintson a **Mentés** gombra.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="7d3e7-138">Kattintson a bal oldalon a **ResizeImage** függvény nevére a függvény megnyitásához.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="7d3e7-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-139">**C#**</span></span>

    1. <span data-ttu-id="7d3e7-140">(C#) Módosítsa a függvény visszatérési típusát **void** típusról **object** típusra.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="7d3e7-141">(C#) Adja hozzá a következő kódot a függvény végéhez, hogy a mentendő dokumentumot adja vissza:</span><span class="sxs-lookup"><span data-stu-id="7d3e7-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![A ResizeImages függvény run.csx fájlja a módosítások után](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="7d3e7-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-143">**JavaScript**</span></span>

    1. <span data-ttu-id="7d3e7-144">(JavaScript) Módosítsa a `else` záradék `context.done()` utasítását, hogy küldje vissza a menteni kívánt dokumentumot a Cosmos DB-be.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

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

1. <span data-ttu-id="7d3e7-145">Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="7d3e7-146">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-146">Click **Save**.</span></span> <span data-ttu-id="7d3e7-147">Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="7d3e7-148">Függvény létrehozása a Cosmos DB-ben található képek listázására</span><span class="sxs-lookup"><span data-stu-id="7d3e7-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="7d3e7-149">A webalkalmazáshoz egy API szükséges, amely lekéri a képek metaadatait a Cosmos DB-ből.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="7d3e7-150">A következő lépésekben</span><span class="sxs-lookup"><span data-stu-id="7d3e7-150">In the following steps.</span></span> <span data-ttu-id="7d3e7-151">létrehozhat egy HTTP-n aktivált függvényt, amely egy Cosmos DB bemeneti kötéssel kérdezi le az adatbázis-gyűjteményt.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-151">you create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="7d3e7-152">A függvényalkalmazásban vigye a mutatót a **Függvények** elem fölé a bal oldalon, és a **+** elemre kattintva hozzon létre egy új függvényt.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="7d3e7-153">Keresse meg a **HttpTrigger** sablont, és válassza ki.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="7d3e7-154">Az alábbi értékekkel hozzon létre egy függvényt, amely előállít egy URL-címet a képek lekéréséhez.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="7d3e7-155">Beállítás</span><span class="sxs-lookup"><span data-stu-id="7d3e7-155">Setting</span></span>      |  <span data-ttu-id="7d3e7-156">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="7d3e7-156">Suggested value</span></span>   | <span data-ttu-id="7d3e7-157">Leírás</span><span class="sxs-lookup"><span data-stu-id="7d3e7-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="7d3e7-158">**A függvény neve**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-158">**Name your function**</span></span> | <span data-ttu-id="7d3e7-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="7d3e7-159">GetImages</span></span> | <span data-ttu-id="7d3e7-160">Pontosan úgy írja be a nevet, ahogy itt látható, hogy az alkalmazás megtalálja a függvényt.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="7d3e7-161">**Engedélyszint**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-161">**Authorization level**</span></span> | <span data-ttu-id="7d3e7-162">Névtelen</span><span class="sxs-lookup"><span data-stu-id="7d3e7-162">Anonymous</span></span> | <span data-ttu-id="7d3e7-163">Engedélyezze, hogy a függvény nyilvánosan elérhető legyen.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="7d3e7-164">Kattintson a **Create** (Létrehozás) gombra.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-164">Click **Create**.</span></span>

1. <span data-ttu-id="7d3e7-165">Az új függvény létrehozása után kattintson az **Integrálás** elemre a függvény neve alatt a bal oldali menüben.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="7d3e7-166">Kattintson az **Új bemenet** elemre, majd válassza az **Azure Cosmos DB** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Új bemenet kiválasztása](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="7d3e7-168">Kattintson a **Kiválasztás** gombra.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-168">Click **Select**.</span></span>

1. <span data-ttu-id="7d3e7-169">Adja meg az alábbi értékeket:</span><span class="sxs-lookup"><span data-stu-id="7d3e7-169">Fill out the following values:</span></span>

    | <span data-ttu-id="7d3e7-170">Beállítás</span><span class="sxs-lookup"><span data-stu-id="7d3e7-170">Setting</span></span>      |  <span data-ttu-id="7d3e7-171">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="7d3e7-171">Suggested value</span></span>   | <span data-ttu-id="7d3e7-172">Leírás</span><span class="sxs-lookup"><span data-stu-id="7d3e7-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="7d3e7-173">**Dokumentumparaméter neve**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-173">**Document parameter name**</span></span> | <span data-ttu-id="7d3e7-174">documents</span><span class="sxs-lookup"><span data-stu-id="7d3e7-174">documents</span></span> | <span data-ttu-id="7d3e7-175">Egyezik a paraméter nevével a függvényben.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="7d3e7-176">**Adatbázis neve**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-176">**Database name**</span></span> | <span data-ttu-id="7d3e7-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="7d3e7-177">imagesdb</span></span> |  |
    | <span data-ttu-id="7d3e7-178">**Gyűjtemény neve**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-178">**Collection name**</span></span> | <span data-ttu-id="7d3e7-179">images</span><span class="sxs-lookup"><span data-stu-id="7d3e7-179">images</span></span> |  |
    | <span data-ttu-id="7d3e7-180">**SQL-lekérdezés**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-180">**SQL query**</span></span> | <span data-ttu-id="7d3e7-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="7d3e7-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="7d3e7-182">A dokumentumok beolvasása, először a legutóbbiaké.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="7d3e7-183">**Azure Cosmos DB-fiók kapcsolata**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="7d3e7-184">Meglévő kapcsolati sztring kiválasztása</span><span class="sxs-lookup"><span data-stu-id="7d3e7-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="7d3e7-185">A bemeneti kötés létrehozásához kattintson a **Mentés** elemre.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="7d3e7-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-186">**C#**</span></span>

    1. <span data-ttu-id="7d3e7-187">A függvény nevére kattintva nyissa meg a kódablakot, és cserélje le a **run.csx** teljes tartalmát a [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx) tartalmára.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="7d3e7-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7d3e7-188">**JavaScript**</span></span>

    1. <span data-ttu-id="7d3e7-189">A függvény nevére kattintva nyissa meg a kódablakot, és cserélje le az **index.js** teljes tartalmát a [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js) tartalmára.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="7d3e7-190">Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="7d3e7-191">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-191">Click **Save**.</span></span> <span data-ttu-id="7d3e7-192">Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="7d3e7-193">Az alkalmazás tesztelése</span><span class="sxs-lookup"><span data-stu-id="7d3e7-193">Test the application</span></span>

1. <span data-ttu-id="7d3e7-194">Nyissa meg az alkalmazást egy böngészőben.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-194">Open the application in a browser.</span></span> <span data-ttu-id="7d3e7-195">Válasszon ki egy képfájlt, és töltse fel.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="7d3e7-196">Néhány másodperc múlva az új kép miniatűrje megjelenik a lapon.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="7d3e7-197">Az Azure Portalon a keresőmezőben keresse meg név alapján a Cosmos DB-fiókot.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="7d3e7-198">Kattintson rá a megnyitásához.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-198">Click it to open it.</span></span>

1. <span data-ttu-id="7d3e7-199">A bal oldalon kattintson az **Adatkezelőre**, amelyben a gyűjtemények és dokumentumok között tallózhat.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="7d3e7-200">Az **imagesdb** adatbázis alatt válassza ki az **images** gyűjteményt.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="7d3e7-201">Győződjön meg arról, hogy létrejött egy dokumentum a feltöltött képhez.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Egy feltöltött képhez tartozó dokumentum az Adatkezelőben](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="7d3e7-203">Összegzés</span><span class="sxs-lookup"><span data-stu-id="7d3e7-203">Summary</span></span>

<span data-ttu-id="7d3e7-204">Ebben a modulban elsajátította, hogyan hozhat létre Cosmos DB-fiókokat, -adatbázisokat és -gyűjteményeket.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="7d3e7-205">Emellett megtanulta azt is, hogyan mentheti és olvashatja be a képek metaadatait a Cosmos DB-gyűjteményben a Cosmos DB kötéseivel.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="7d3e7-206">Ezután megtanulhatja, hogyan hozhat létre feliratokat automatikusan mindegyik feltöltött képhez a Microsoft Cognitive Services használatával.</span><span class="sxs-lookup"><span data-stu-id="7d3e7-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>
