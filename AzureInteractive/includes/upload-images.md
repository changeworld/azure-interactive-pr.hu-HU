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
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079473"
---
<span data-ttu-id="28ca4-103">Az alkalmazás, amelyet létre fog hozni, egy fényképgaléria.</span><span class="sxs-lookup"><span data-stu-id="28ca4-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="28ca4-104">Ügyféloldali JavaScriptet használ az API-k hívásához, amelyek segítségével feltölti és megjeleníti a képeket.</span><span class="sxs-lookup"><span data-stu-id="28ca4-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="28ca4-105">Ez a modul egy kiszolgáló nélküli függvényt használó API létrehozását mutatja be, amely a fénykép feltöltéséhez időkorlátos URL-címet hoz létre.</span><span class="sxs-lookup"><span data-stu-id="28ca4-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="28ca4-106">A webalkalmazás a létrehozott URL-cím segítségével tölti fel a képet a Blob Storage-ba a [Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) használatával.</span><span class="sxs-lookup"><span data-stu-id="28ca4-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="28ca4-107">Blob Storage-tároló létrehozása a képek számára</span><span class="sxs-lookup"><span data-stu-id="28ca4-107">Create a blob storage container for images</span></span>

<span data-ttu-id="28ca4-108">Az alkalmazásnak egy különálló Storage-tárolóra van szüksége a képek feltöltéséhez és tárolásához.</span><span class="sxs-lookup"><span data-stu-id="28ca4-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="28ca4-109">Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe (bash).</span><span class="sxs-lookup"><span data-stu-id="28ca4-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="28ca4-110">Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához.</span><span class="sxs-lookup"><span data-stu-id="28ca4-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="28ca4-111">Hozzon létre egy olyan új, **images** nevű tárolót a tárfiókban, amely nyilvános hozzáféréssel rendelkezik a blobokhoz.</span><span class="sxs-lookup"><span data-stu-id="28ca4-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="28ca4-112">Azure-függvényalkalmazás létrehozása</span><span class="sxs-lookup"><span data-stu-id="28ca4-112">Create an Azure Function app</span></span>

<span data-ttu-id="28ca4-113">Az Azure Functions egy kiszolgáló nélküli függvényeket futtató szolgáltatás.</span><span class="sxs-lookup"><span data-stu-id="28ca4-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="28ca4-114">A kiszolgáló nélküli függvényeket események aktiválhatják (hívhatják meg), például egy HTTP-kérés vagy egy blob létrejötte egy Storage-tárolóban.</span><span class="sxs-lookup"><span data-stu-id="28ca4-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="28ca4-115">Az Azure-függvényalkalmazás egy vagy több kiszolgáló nélküli függvény tárolója.</span><span class="sxs-lookup"><span data-stu-id="28ca4-115">An Azure Function app is a container for one or more serverless functions.</span></span>

1. <span data-ttu-id="28ca4-116">Hozzon létre egy új, egyéni névvel rendelkező Azure-függvényalkalmazást a korábban létrehozott, **first-serverless-app** elnevezésű erőforráscsoportban.</span><span class="sxs-lookup"><span data-stu-id="28ca4-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="28ca4-117">A függvényalkalmazásoknak tárfiókra van szükségük. Ehhez az oktatóanyaghoz használja a már létező tárfiókot.</span><span class="sxs-lookup"><span data-stu-id="28ca4-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="28ca4-118">Hozzon létre egy HTTP-eseményindítóval aktivált, kiszolgáló nélküli függvényt</span><span class="sxs-lookup"><span data-stu-id="28ca4-118">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="28ca4-119">A fényképgaléria-webalkalmazás HTTP-kérést hajt végre egy időkorlátos URL létrehozására a kiszolgáló nélküli függvény felé, amellyel biztonságosan töltheti fel a képet a Blob Storage-ba.</span><span class="sxs-lookup"><span data-stu-id="28ca4-119">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="28ca4-120">A függvényt egy HTTP-kérés aktiválja, és az Azure Storage SDK-t használja a biztonságos URL létrehozásához és visszaadásához.</span><span class="sxs-lookup"><span data-stu-id="28ca4-120">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="28ca4-121">A létrehozása után keresse meg a függvényalkalmazást az Azure Portalon a keresőmező használatával, majd a megnyitásához kattintson rá.</span><span class="sxs-lookup"><span data-stu-id="28ca4-121">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![A függvényalkalmazás megnyitása](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="28ca4-123">A függvényalkalmazás ablakának bal oldali navigációs sávjában helyezze a mutatót a **Függvények** elem fölé, és kattintson a **+** elemre egy új kiszolgáló nélküli függvény létrehozásának megkezdéséhez.</span><span class="sxs-lookup"><span data-stu-id="28ca4-123">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Új függvény létrehozása](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="28ca4-125">Kattintson az **Egyéni függvény** elemre a függvénysablonok listájának megtekintéséhez.</span><span class="sxs-lookup"><span data-stu-id="28ca4-125">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="28ca4-126">Keresse meg a **HttpTrigger** sablont, és válassza ki a használandó nyelvet (C# vagy JavaScript).</span><span class="sxs-lookup"><span data-stu-id="28ca4-126">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="28ca4-127">Használja az alábbi értékeket a függvény létrehozásához, amely a blob feltöltéséhez használt URL-t létrehozza.</span><span class="sxs-lookup"><span data-stu-id="28ca4-127">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="28ca4-128">Beállítás</span><span class="sxs-lookup"><span data-stu-id="28ca4-128">Setting</span></span>      |  <span data-ttu-id="28ca4-129">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="28ca4-129">Suggested value</span></span>   | <span data-ttu-id="28ca4-130">Leírás</span><span class="sxs-lookup"><span data-stu-id="28ca4-130">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="28ca4-131">**Nyelv**</span><span class="sxs-lookup"><span data-stu-id="28ca4-131">**Language**</span></span> | <span data-ttu-id="28ca4-132">C# vagy JavaScript</span><span class="sxs-lookup"><span data-stu-id="28ca4-132">C# or JavaScript</span></span> | <span data-ttu-id="28ca4-133">Válassza ki a használni kívánt nyelvet.</span><span class="sxs-lookup"><span data-stu-id="28ca4-133">Select the language you want to use.</span></span> |
    | <span data-ttu-id="28ca4-134">**A függvény neve**</span><span class="sxs-lookup"><span data-stu-id="28ca4-134">**Name your function**</span></span> | <span data-ttu-id="28ca4-135">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="28ca4-135">GetUploadUrl</span></span> | <span data-ttu-id="28ca4-136">Pontosan úgy írja be a nevet, ahogy itt látható, hogy az alkalmazás megtalálja a függvényt.</span><span class="sxs-lookup"><span data-stu-id="28ca4-136">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="28ca4-137">**Engedélyszint**</span><span class="sxs-lookup"><span data-stu-id="28ca4-137">**Authorization level**</span></span> | <span data-ttu-id="28ca4-138">Névtelen</span><span class="sxs-lookup"><span data-stu-id="28ca4-138">Anonymous</span></span> | <span data-ttu-id="28ca4-139">Engedélyezze, hogy a függvény nyilvánosan elérhető legyen.</span><span class="sxs-lookup"><span data-stu-id="28ca4-139">Allow the function to be accessed publicly.</span></span> |

    ![Adja meg egy új, HTTP-eseményindítóval aktivált függvény beállításait](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="28ca4-141">A függvény létrehozásához kattintson a **Létrehozás** elemre.</span><span class="sxs-lookup"><span data-stu-id="28ca4-141">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="28ca4-142">**C#**</span><span class="sxs-lookup"><span data-stu-id="28ca4-142">**C#**</span></span> 

    1. <span data-ttu-id="28ca4-143">Amikor megjelenik a függvény forráskódja, cserélje le az összes **run.csx** elemet a [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx) tartalmára.</span><span class="sxs-lookup"><span data-stu-id="28ca4-143">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="28ca4-144">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="28ca4-144">**JavaScript**</span></span> 

    1. <span data-ttu-id="28ca4-145">(JavaScript) Ennek a függvénynek szüksége van az npm-ből származó `azure-storage` csomagra a közös hozzáférésű jogosultságkód (SAS) token létrehozásához a biztonságos URL-cím összeállításakor.</span><span class="sxs-lookup"><span data-stu-id="28ca4-145">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="28ca4-146">Az npm-csomag telepítéséhez kattintson a függvényalkalmazás nevére a bal oldali navigációs sávon, majd válassza a **Platformfunkciók** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="28ca4-146">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="28ca4-147">(JavaScript) Kattintson a **Konzol** elemre a konzolablak megjelenítéséhez.</span><span class="sxs-lookup"><span data-stu-id="28ca4-147">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![A konzolablak megnyitása](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="28ca4-149">(JavaScript) A `cd d:\home\site\wwwroot` parancs futtatásával győződjön meg arról, hogy a **d:\home\site\wwwroot** az aktuális könyvtár.</span><span class="sxs-lookup"><span data-stu-id="28ca4-149">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="28ca4-150">(JavaScript) Futtassa az `npm init -y` parancsot egy üres **package.json** fájl létrehozásához.</span><span class="sxs-lookup"><span data-stu-id="28ca4-150">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="28ca4-151">(JavaScript) Futtassa az `npm install --save azure-storage` parancsot a konzolban a csomag telepítéséhez és mentéséhez a **package.json** fájlba.</span><span class="sxs-lookup"><span data-stu-id="28ca4-151">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="28ca4-152">A művelet végrehajtása egy-két percet igénybe vehet.</span><span class="sxs-lookup"><span data-stu-id="28ca4-152">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="28ca4-153">(JavaScript) Kattintson a (**GetUploadUrl**) függvény nevére a bal oldali navigációs sávban a függvény megjelenítéséhez, majd cserélje le az összes **index.js** elemet a [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js) tartalmára.</span><span class="sxs-lookup"><span data-stu-id="28ca4-153">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js frissítés után](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="28ca4-155">Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.</span><span class="sxs-lookup"><span data-stu-id="28ca4-155">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="28ca4-156">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="28ca4-156">Click **Save**.</span></span> <span data-ttu-id="28ca4-157">A naplók panelen győződjön meg arról, hogy a függvény fordítása sikeres volt.</span><span class="sxs-lookup"><span data-stu-id="28ca4-157">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="28ca4-158">A függvény létrehozza az ún. közös hozzáférésű jogosultságkód (SAS) URL-jét, amelynek használatával fájlokat tölthet fel a Blob Storage-ba.</span><span class="sxs-lookup"><span data-stu-id="28ca4-158">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="28ca4-159">A SAS URL csak egy rövid időtartamra érvényes, és egyetlen fájl feltöltésére használható.</span><span class="sxs-lookup"><span data-stu-id="28ca4-159">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="28ca4-160">További információkért a [közös hozzáférésű jogosultságkód (SAS) használatáról](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) tekintse át a Blob Storage dokumentációját.</span><span class="sxs-lookup"><span data-stu-id="28ca4-160">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="28ca4-161">Adjon egy környezeti változót a Storage kapcsolati sztringjéhez</span><span class="sxs-lookup"><span data-stu-id="28ca4-161">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="28ca4-162">Az imént létrehozott függvény egy kapcsolati sztringet igényel a Storage-fiókhoz, hogy létrehozhassa a SAS URL-t.</span><span class="sxs-lookup"><span data-stu-id="28ca4-162">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="28ca4-163">A kapcsolati sztring alkalmazásbeállításként is tárolható, nem szükséges a függvényben rögzíteni.</span><span class="sxs-lookup"><span data-stu-id="28ca4-163">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="28ca4-164">Az alkalmazásbeállításokat a környezeti változókhoz hasonlóan a függvényalkalmazásokból érik el a függvények.</span><span class="sxs-lookup"><span data-stu-id="28ca4-164">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="28ca4-165">A Cloud Shellben kérdezze le a Storage-fiók kapcsolati sztringjét, majd mentse bash változóként **STORAGE_CONNECTION_STRING** néven.</span><span class="sxs-lookup"><span data-stu-id="28ca4-165">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="28ca4-166">Győződjön meg arról, hogy a változó beállítása sikeres volt.</span><span class="sxs-lookup"><span data-stu-id="28ca4-166">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="28ca4-167">Hozzon létre új egy alkalmazásbeállítást **AZURE_STORAGE_CONNECTION_STRING** néven az előző lépésben mentett érték felhasználásával.</span><span class="sxs-lookup"><span data-stu-id="28ca4-167">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="28ca4-168">Győződjön meg arról, hogy a parancs kimenete tartalmazza az új alkalmazásbeállítást a helyes értékkel együtt.</span><span class="sxs-lookup"><span data-stu-id="28ca4-168">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="28ca4-169">A kiszolgáló nélküli függvény tesztelése</span><span class="sxs-lookup"><span data-stu-id="28ca4-169">Test the serverless function</span></span>

<span data-ttu-id="28ca4-170">A függvények létrehozása és szerkesztése mellett az Azure Portal beépített eszközöket is biztosít azok teszteléséhez.</span><span class="sxs-lookup"><span data-stu-id="28ca4-170">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="28ca4-171">A HTTP kiszolgáló nélküli függvény teszteléséhez kattintson a kódablak jobb oldalán a **Tesztelés** gombra, amely kibontja a tesztelési panelt.</span><span class="sxs-lookup"><span data-stu-id="28ca4-171">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="28ca4-172">Cserélje a **HTTP-metódust** a **GET-metódusra**.</span><span class="sxs-lookup"><span data-stu-id="28ca4-172">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="28ca4-173">A **Lekérdezés** menüpont alatt kattintson a *Paraméter hozzáadása*\* elemre, és adja hozzá a következő paramétereket:</span><span class="sxs-lookup"><span data-stu-id="28ca4-173">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="28ca4-174">név</span><span class="sxs-lookup"><span data-stu-id="28ca4-174">name</span></span>      |  <span data-ttu-id="28ca4-175">érték</span><span class="sxs-lookup"><span data-stu-id="28ca4-175">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="28ca4-176">fájlnév</span><span class="sxs-lookup"><span data-stu-id="28ca4-176">filename</span></span> | <span data-ttu-id="28ca4-177">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="28ca4-177">image1.jpg</span></span> |

1. <span data-ttu-id="28ca4-178">Kattintson a **Futtatás** menüpontra a tesztelési panelen, hogy HTTP-kérést küldjön a függvénynek.</span><span class="sxs-lookup"><span data-stu-id="28ca4-178">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="28ca4-179">A függvény a kimenetén egy, a feltöltéshez használható URL-címmel tér vissza.</span><span class="sxs-lookup"><span data-stu-id="28ca4-179">The function returns an upload URL in the output.</span></span> <span data-ttu-id="28ca4-180">A naplók panelen megjelenik a függvényvégrehajtás.</span><span class="sxs-lookup"><span data-stu-id="28ca4-180">The function execution appears in the logs panel.</span></span>

    ![A függvény sikeres lefutását jelző naplók](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="28ca4-182">A CORS konfigurálása a függvényalkalmazásban</span><span class="sxs-lookup"><span data-stu-id="28ca4-182">Configure CORS in the function app</span></span>

<span data-ttu-id="28ca4-183">Mivel az alkalmazás előtere a Blob Storage-ban üzemel, a tartományneve eltér az Azure-függvényalkalmazásétól.</span><span class="sxs-lookup"><span data-stu-id="28ca4-183">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="28ca4-184">Hogy az ügyféloldali JavaScript sikeresen meg tudja hívni az imént létrehozott függvényt, a függvényalkalmazást konfigurálnia kell az eltérő eredetű erőforrások megosztásához (CORS).</span><span class="sxs-lookup"><span data-stu-id="28ca4-184">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="28ca4-185">A függvényalkalmazás ablak bal oldali navigációs sávjában kattintson a függvényalkalmazás nevére.</span><span class="sxs-lookup"><span data-stu-id="28ca4-185">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="28ca4-186">Kattintson a **Platformfunkciók** lehetőségre a speciális funkciók listájának megtekintéséhez.</span><span class="sxs-lookup"><span data-stu-id="28ca4-186">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="28ca4-187">Az **API** menüpont alatt válassza a **CORS** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="28ca4-187">Under **API**, click **CORS**.</span></span>

    ![CORS kiválasztása](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="28ca4-189">Adjon egy engedélyezett forrást az alkalmazás URL-jéhez az előző modulból a zárás elhagyásával **/** (például: `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="28ca4-189">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![A CORS-beállítások jelzik, hogy kiszolgáló nélküli webalkalmazás URL-je lett hozzáadva](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="28ca4-191">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="28ca4-191">Click **Save**.</span></span>

1. <span data-ttu-id="28ca4-192">C#</span><span class="sxs-lookup"><span data-stu-id="28ca4-192">C#</span></span>

   1. <span data-ttu-id="28ca4-193">(C#) Lépjen vissza a `GetUploadUrl` függvényre, majd válassza az **Integrálás** lapot.</span><span class="sxs-lookup"><span data-stu-id="28ca4-193">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="28ca4-194">(C#) A Kiválasztott HTTP-metódusok területen alatt válassza ki az **OPTIONS** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="28ca4-194">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="28ca4-195">A **GET**, **POST** és **OPTIONS** mindegyikét jelölje ki.</span><span class="sxs-lookup"><span data-stu-id="28ca4-195">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="28ca4-196">A CORS az OPTIONS metódust használja, ez alapértelmezés szerint nincs kiválasztva a C# függvényekhez.</span><span class="sxs-lookup"><span data-stu-id="28ca4-196">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="28ca4-197">(C#) Kattintson a **Mentés** gombra.</span><span class="sxs-lookup"><span data-stu-id="28ca4-197">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="28ca4-198">A portálon navigáljon a függvényalkalmazásra, válassza az **Áttekintés** lapot, majd kattintson az **Újraindítás** gombra, és győződjön meg arról, hogy a CORS módosításai érvénybe léptek.</span><span class="sxs-lookup"><span data-stu-id="28ca4-198">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="28ca4-199">A CORS konfigurálása a Storage-fiókban</span><span class="sxs-lookup"><span data-stu-id="28ca4-199">Configure CORS in the Storage account</span></span>

<span data-ttu-id="28ca4-200">Mivel az alkalmazás a fájlok feltöltéséhez ügyféloldali JavaScript-hívásokat intéz a Blob Storage-hoz, konfigurálnia kell a Storage-fiókot a CORS-hoz.</span><span class="sxs-lookup"><span data-stu-id="28ca4-200">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="28ca4-201">Futtassa a következő parancsot, hogy az összes forrásról engedélyezze a Storage-fiókba való fájlfeltöltést.</span><span class="sxs-lookup"><span data-stu-id="28ca4-201">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="28ca4-202">A webalkalmazás módosítása képek feltöltéséhez</span><span class="sxs-lookup"><span data-stu-id="28ca4-202">Modify the web app to upload images</span></span>

<span data-ttu-id="28ca4-203">A webalkalmazás a beállításokat a **settings.js** fájlból kéri le.</span><span class="sxs-lookup"><span data-stu-id="28ca4-203">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="28ca4-204">A következő lépésekben hozzon létre egy fájlt a Cloud Shell segítségével, majd állítsa be a `window.apiBaseUrl` értéket a függvényalkalmazás URL-címéhez és a `window.blobBaseUrl` értéket az Azure Blob Storage-végpont URL-jéhez.</span><span class="sxs-lookup"><span data-stu-id="28ca4-204">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="28ca4-205">Győződjön meg róla a Cloud Shellben, hogy a **www/dist** mappa az aktuális könyvtár.</span><span class="sxs-lookup"><span data-stu-id="28ca4-205">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="28ca4-206">Kérdezze le a függvényalkalmazás URL-jét, és tárolja egy bash változóban **FUNCTION_APP_URL** néven.</span><span class="sxs-lookup"><span data-stu-id="28ca4-206">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="28ca4-207">Győződjön meg arról, hogy a változó helyesen lett beállítva.</span><span class="sxs-lookup"><span data-stu-id="28ca4-207">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="28ca4-208">Az API-hívások kiindulási URI-ját a következőképpen tudja beállítani a függvényalkalmazásokhoz: hozza létre a **settings.js** fájlt, és adja hozzá a függvényalkalmazás URL-címét a következők szerint.</span><span class="sxs-lookup"><span data-stu-id="28ca4-208">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="28ca4-209">Elvégezheti a módosítást az alábbi parancs futtatásával, vagy egy parancssor-szerkesztővel, például a VIM-mel.</span><span class="sxs-lookup"><span data-stu-id="28ca4-209">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="28ca4-210">Győződjön meg arról, hogy a fájl írása sikeres volt.</span><span class="sxs-lookup"><span data-stu-id="28ca4-210">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="28ca4-211">Kérdezze le a Blob Storage kiindulási URL-címét, és tárolja egy bash változóban **BLOB_BASE_URL** néven.</span><span class="sxs-lookup"><span data-stu-id="28ca4-211">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="28ca4-212">Győződjön meg arról, hogy a változó helyesen lett beállítva.</span><span class="sxs-lookup"><span data-stu-id="28ca4-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="28ca4-213">Az API-hívások kiindulási URI-ját a következőképpen tudja beállítani a függvényalkalmazásokhoz: fűzze hozzá a tároló URL-jét a következő kódsor szerint a **settings.js** fájlhoz.</span><span class="sxs-lookup"><span data-stu-id="28ca4-213">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="28ca4-214">Elvégezheti a módosítást az alábbi parancs futtatásával, vagy egy parancssor-szerkesztővel, például a VIM-mel.</span><span class="sxs-lookup"><span data-stu-id="28ca4-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="28ca4-215">Győződjön meg arról, hogy a fájl írása sikeres volt, és már 2 sort tartalmaz.</span><span class="sxs-lookup"><span data-stu-id="28ca4-215">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="28ca4-216">Töltse fel a fájlt a Blob Storage-ba.</span><span class="sxs-lookup"><span data-stu-id="28ca4-216">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="28ca4-217">A webalkalmazás tesztelése</span><span class="sxs-lookup"><span data-stu-id="28ca4-217">Test the web application</span></span>

<span data-ttu-id="28ca4-218">Ezen a ponton a katalógusbeli alkalmazás fel tud tölteni egy képet a Blob Storage-ba, de a képek megjelenítésére még nem alkalmas.</span><span class="sxs-lookup"><span data-stu-id="28ca4-218">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="28ca4-219">Megpróbál meghívni egy `GetImages` függvényt, amely még nem létezik, mert létrehozására majd egy későbbi modulban kerül sor.</span><span class="sxs-lookup"><span data-stu-id="28ca4-219">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="28ca4-220">Ez a hívás meghiúsul, és úgy tűnik, hogy a weblap az „Elemzés...” állapotban elakad, a választott kép feltöltése azonban sikerül.</span><span class="sxs-lookup"><span data-stu-id="28ca4-220">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="28ca4-221">Az Azure Portal **images** tárolójának megtekintésével meggyőződhet arról, hogy a kép feltöltése sikeres volt.</span><span class="sxs-lookup"><span data-stu-id="28ca4-221">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="28ca4-222">Egy böngészőablakban keresse meg az alkalmazást.</span><span class="sxs-lookup"><span data-stu-id="28ca4-222">In a browser window, browse to the application.</span></span> <span data-ttu-id="28ca4-223">Válasszon ki egy képfájlt, és töltse fel.</span><span class="sxs-lookup"><span data-stu-id="28ca4-223">Select an image file and upload it.</span></span> <span data-ttu-id="28ca4-224">A feltöltés befejeződött, de mivel még nem adtuk hozzá a képmegjelenítési képességet, az alkalmazás nem jeleníti meg a feltöltött képet.</span><span class="sxs-lookup"><span data-stu-id="28ca4-224">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="28ca4-225">(Úgy tűnik, hogy a weblap a „Kép elemzése...” állapotban elakad, ezt később javíthatja ki.)</span><span class="sxs-lookup"><span data-stu-id="28ca4-225">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="28ca4-226">A Cloud Shellben erősítse meg, hogy a kép fel lett töltve az **images** tárolóba.</span><span class="sxs-lookup"><span data-stu-id="28ca4-226">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="28ca4-227">Mielőtt továbblépne a következő oktatóanyagra, törölje az összes fájlt az **images** tárolóból.</span><span class="sxs-lookup"><span data-stu-id="28ca4-227">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="28ca4-228">Összegzés</span><span class="sxs-lookup"><span data-stu-id="28ca4-228">Summary</span></span>

<span data-ttu-id="28ca4-229">Ebben a modulban létrehozhatott egy Azure-függvényalkalmazást, és megtanulhatta, hogyan engedélyezheti a webalkalmazásoknak egy kiszolgáló nélküli függvény használatával, hogy képeket töltsenek fel a Blob Storage-ba.</span><span class="sxs-lookup"><span data-stu-id="28ca4-229">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="28ca4-230">Ezután megtanulhatja, hogyan hozhat létre miniatűröket a feltöltött képekhez egy Blob-eseményindítóval aktivált, kiszolgáló nélküli függvény használatával.</span><span class="sxs-lookup"><span data-stu-id="28ca4-230">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>