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
<span data-ttu-id="8a258-103">Az előző modul bemutatta, hogyan segítheti elő egy kiszolgáló nélküli függvény a képek biztonságos feltöltését egy webalkalmazásból a Blob Storage-ba.</span><span class="sxs-lookup"><span data-stu-id="8a258-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="8a258-104">Ebben a modulban egy másik kiszolgáló nélküli függvényt hoz létre, amely felügyeli a feltöltött képeket és létrehozza a miniatűrjeiket.</span><span class="sxs-lookup"><span data-stu-id="8a258-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="8a258-105">Blob Storage-tároló létrehozása a miniatűrök számára</span><span class="sxs-lookup"><span data-stu-id="8a258-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="8a258-106">A teljes méretű képeket az **images** nevű tárolóban tárolja a rendszer.</span><span class="sxs-lookup"><span data-stu-id="8a258-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="8a258-107">A képek miniatűrjeinek tárolására egy másik tárolóra van szükség.</span><span class="sxs-lookup"><span data-stu-id="8a258-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="8a258-108">Győződjön meg arról, hogy továbbra is be van jelentkezve a Cloud Shellbe (bash).</span><span class="sxs-lookup"><span data-stu-id="8a258-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="8a258-109">Ha nincs, válassza az **Enter focus mode** (Fókusz mód megnyitása) lehetőséget egy Cloud Shell-ablak megnyitásához.</span><span class="sxs-lookup"><span data-stu-id="8a258-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="8a258-110">Hozzon létre egy olyan új, **thumbnails** nevű tárolót a tárfiókban, amely nyilvános hozzáféréssel rendelkezik a blobokhoz.</span><span class="sxs-lookup"><span data-stu-id="8a258-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="8a258-111">Blob által aktivált, kiszolgáló nélküli függvény létrehozása</span><span class="sxs-lookup"><span data-stu-id="8a258-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="8a258-112">A trigger határozza meg a függvény meghívásának módját.</span><span class="sxs-lookup"><span data-stu-id="8a258-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="8a258-113">A következőként létrehozott függvény blobtriggert használ: a rendszer automatikusan meghívja a függvényt, amikor feltölt egy blobot (képfájlt) az **images** tárolóba.</span><span class="sxs-lookup"><span data-stu-id="8a258-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="8a258-114">Minden függvénynek egy triggerrel kell rendelkeznie.</span><span class="sxs-lookup"><span data-stu-id="8a258-114">A function must have one trigger.</span></span> <span data-ttu-id="8a258-115">A triggerekhez társított adatok tartoznak, amelyek általában a függvényt aktiváló hasznos adatok.</span><span class="sxs-lookup"><span data-stu-id="8a258-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="8a258-116">A kötések határozzák meg, hogyan olvas vagy ír adatokat a függvény az Azure-ban vagy a külső szolgáltatásokban.</span><span class="sxs-lookup"><span data-stu-id="8a258-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="8a258-117">Ez a függvény létrehozza az aktiváló kép miniatűr verzióját, és menti a miniatűrt a *thumbnails* tárolóba.</span><span class="sxs-lookup"><span data-stu-id="8a258-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="8a258-118">Nyissa meg a függvényalkalmazást az Azure Portalon.</span><span class="sxs-lookup"><span data-stu-id="8a258-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="8a258-119">A függvényalkalmazás ablakának bal oldali navigációs sávjában helyezze a mutatót a **Függvények** elem fölé, és kattintson a **+** elemre egy új kiszolgáló nélküli függvény létrehozásának megkezdéséhez.</span><span class="sxs-lookup"><span data-stu-id="8a258-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="8a258-120">Ha megjelenik a gyors létrehozási lap, kattintson az **Egyéni függvény** elemre a függvénysablonok listájának megtekintéséhez.</span><span class="sxs-lookup"><span data-stu-id="8a258-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="8a258-121">Keresse meg a **BlobTrigger** sablont, majd válassza ki.</span><span class="sxs-lookup"><span data-stu-id="8a258-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="8a258-122">Használja az alábbi értékeket egy olyan függvény létrehozásához, amely miniatűröket hoz létre a feltöltött képekről.</span><span class="sxs-lookup"><span data-stu-id="8a258-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="8a258-123">Beállítás</span><span class="sxs-lookup"><span data-stu-id="8a258-123">Setting</span></span>      |  <span data-ttu-id="8a258-124">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="8a258-124">Suggested value</span></span>   | <span data-ttu-id="8a258-125">Leírás</span><span class="sxs-lookup"><span data-stu-id="8a258-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="8a258-126">**Nyelv**</span><span class="sxs-lookup"><span data-stu-id="8a258-126">**Language**</span></span> | <span data-ttu-id="8a258-127">C# vagy JavaScript</span><span class="sxs-lookup"><span data-stu-id="8a258-127">C# or JavaScript</span></span> | <span data-ttu-id="8a258-128">Válassza ki a kívánt nyelvet.</span><span class="sxs-lookup"><span data-stu-id="8a258-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="8a258-129">**A függvény neve**</span><span class="sxs-lookup"><span data-stu-id="8a258-129">**Name your function**</span></span> | <span data-ttu-id="8a258-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="8a258-130">ResizeImage</span></span> | <span data-ttu-id="8a258-131">Pontosan úgy írja be a nevet, ahogy itt látható, hogy az alkalmazás megtalálja a függvényt.</span><span class="sxs-lookup"><span data-stu-id="8a258-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="8a258-132">**Elérési út**</span><span class="sxs-lookup"><span data-stu-id="8a258-132">**Path**</span></span> | <span data-ttu-id="8a258-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="8a258-133">images/{name}</span></span> | <span data-ttu-id="8a258-134">Hajtsa végre a függvényt, amikor megjelenik egy fájl az **images** tárolóban.</span><span class="sxs-lookup"><span data-stu-id="8a258-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="8a258-135">**Tárfiókkal kapcsolatos információk**</span><span class="sxs-lookup"><span data-stu-id="8a258-135">**Storage account information**</span></span> | <span data-ttu-id="8a258-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="8a258-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="8a258-137">Használja a korábban létrehozott környezeti változót a kapcsolati sztringgel.</span><span class="sxs-lookup"><span data-stu-id="8a258-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Az új függvény beállításainak megadása](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="8a258-139">A függvény létrehozásához kattintson a **Létrehozás** elemre.</span><span class="sxs-lookup"><span data-stu-id="8a258-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="8a258-140">Ha a függvény létrejött, kattintson az **Integrálás** elemre a trigger, a bemeneti és a kimeneti kötések megtekintéséhez.</span><span class="sxs-lookup"><span data-stu-id="8a258-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="8a258-141">Új kimeneti kötés létrehozásához kattintson az **Új kimenet** elemre.</span><span class="sxs-lookup"><span data-stu-id="8a258-141">Click **New output** to create a new output binding.</span></span>

    ![Az Integrálás lapon válassza az Új kimenet lehetőséget](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="8a258-143">Válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Kiválasztás** elemre.</span><span class="sxs-lookup"><span data-stu-id="8a258-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="8a258-144">Lehetséges, hogy le kell görgetnie, hogy a **Kiválasztás** gomb láthatóvá váljon.</span><span class="sxs-lookup"><span data-stu-id="8a258-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Válassza az Azure Blob Storage lehetőséget](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="8a258-146">Írja be a következő értékeket.</span><span class="sxs-lookup"><span data-stu-id="8a258-146">Enter the following values.</span></span>

    | <span data-ttu-id="8a258-147">Beállítás</span><span class="sxs-lookup"><span data-stu-id="8a258-147">Setting</span></span>      |  <span data-ttu-id="8a258-148">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="8a258-148">Suggested value</span></span>   | <span data-ttu-id="8a258-149">Leírás</span><span class="sxs-lookup"><span data-stu-id="8a258-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="8a258-150">**Blobparaméter neve**</span><span class="sxs-lookup"><span data-stu-id="8a258-150">**Blob parameter name**</span></span> | <span data-ttu-id="8a258-151">miniatűr</span><span class="sxs-lookup"><span data-stu-id="8a258-151">thumbnail</span></span> | <span data-ttu-id="8a258-152">A függvény az ilyen nevű paramétert használja a miniatűrök írásához.</span><span class="sxs-lookup"><span data-stu-id="8a258-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="8a258-153">**A függvény visszaadott értékének használata**</span><span class="sxs-lookup"><span data-stu-id="8a258-153">**Use function return value**</span></span> | <span data-ttu-id="8a258-154">Nem</span><span class="sxs-lookup"><span data-stu-id="8a258-154">No</span></span> |  |
    | <span data-ttu-id="8a258-155">**Elérési út**</span><span class="sxs-lookup"><span data-stu-id="8a258-155">**Path**</span></span> | <span data-ttu-id="8a258-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="8a258-156">thumbnails/{name}</span></span> | <span data-ttu-id="8a258-157">A rendszer a **thumbnails** nevű tárolóba írja a miniatűröket.</span><span class="sxs-lookup"><span data-stu-id="8a258-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="8a258-158">**Tárfiók kapcsolata**</span><span class="sxs-lookup"><span data-stu-id="8a258-158">**Storage account connection**</span></span> | <span data-ttu-id="8a258-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="8a258-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="8a258-160">Használja a korábban létrehozott környezeti változót a kapcsolati sztringgel.</span><span class="sxs-lookup"><span data-stu-id="8a258-160">Use the environment variable previously created with the connection string.</span></span> |

    ![A Blob kimeneti kötés beállításainak megadása](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="8a258-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="8a258-162">**JavaScript**</span></span>

    1. <span data-ttu-id="8a258-163">(JavaScript) Az ablak jobb felső sarkában kattintson a **Speciális szerkesztő** elemre a kötéseket jelölő JSON megjelenítéséhez.</span><span class="sxs-lookup"><span data-stu-id="8a258-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="8a258-164">(JavaScript) A `blobTrigger` kötésben adjon hozzá egy `dataType` nevű tulajdonságot `binary` értékkel.</span><span class="sxs-lookup"><span data-stu-id="8a258-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="8a258-165">Ez úgy konfigurálja a kötést, hogy a blobok tartalmát bináris adatokként küldje el a függvénynek.</span><span class="sxs-lookup"><span data-stu-id="8a258-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

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

1. <span data-ttu-id="8a258-166">Az új kötés létrehozásához kattintson a **Mentés** gombra.</span><span class="sxs-lookup"><span data-stu-id="8a258-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="8a258-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="8a258-167">**C#**</span></span>

    1. <span data-ttu-id="8a258-168">(C#) Válassza a **ResizeImage** függvénynevet a bal oldali navigációs sávon a függvény forráskódjának megnyitásához.</span><span class="sxs-lookup"><span data-stu-id="8a258-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="8a258-169">(C#) A függvényhez szükség van egy **ImageResizer** nevű NuGet-csomagra, amely létrehozza a miniatűröket.</span><span class="sxs-lookup"><span data-stu-id="8a258-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="8a258-170">A rendszer egy **project.json** fájllal adja hozzá a NuGet-csomagokat a C#-függvényhez.</span><span class="sxs-lookup"><span data-stu-id="8a258-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="8a258-171">A fájl létrehozásához kattintson a **Fájlok megtekintése** elemre a jobb oldalon, hogy megjelenjenek a függvényt alkotó fájlok.</span><span class="sxs-lookup"><span data-stu-id="8a258-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="8a258-172">(C#) Kattintson a **Hozzáadás** elemre egy **project.json** nevű új fájl hozzáadásához.</span><span class="sxs-lookup"><span data-stu-id="8a258-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="8a258-173">(C#) Másolja az újonnan létrehozott fájlba a [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) fájl tartalmát.</span><span class="sxs-lookup"><span data-stu-id="8a258-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="8a258-174">Mentse a fájlt.</span><span class="sxs-lookup"><span data-stu-id="8a258-174">Save the file.</span></span> <span data-ttu-id="8a258-175">A rendszer automatikusan visszaállítja a csomagokat a fájl feltöltésekor.</span><span class="sxs-lookup"><span data-stu-id="8a258-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![a project.json fájl az ImageResizerrel](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="8a258-177">(C#) Válassza a **run.csx** lehetőséget a **Fájlok megtekintése** területen, és cserélje le a tartalmát a [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx) tartalmára.</span><span class="sxs-lookup"><span data-stu-id="8a258-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="8a258-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="8a258-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="8a258-179">(JavaScript) A függvényhez szükség van az npm-ből származó `jimp` csomagra a fénykép átméretezéséhez.</span><span class="sxs-lookup"><span data-stu-id="8a258-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="8a258-180">Az npm-csomag telepítéséhez kattintson a függvényalkalmazás nevére a bal oldali navigációs sávon, majd válassza a **Platformfunkciók** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="8a258-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="8a258-181">(JavaScript) Kattintson a **Konzol** elemre a konzolablak megjelenítéséhez.</span><span class="sxs-lookup"><span data-stu-id="8a258-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="8a258-182">(JavaScript) Futtassa az `npm install jimp` parancsot a konzolban.</span><span class="sxs-lookup"><span data-stu-id="8a258-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="8a258-183">A művelet végrehajtása egy-két percet igénybe vehet.</span><span class="sxs-lookup"><span data-stu-id="8a258-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="8a258-184">(JavaScript) A függvény megjelenítéséhez kattintson a **ResizeImage** függvénynévre a bal oldali navigációs sávon, majd cserélje le az **index.js** teljes tartalmát a [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js) tartalmára.</span><span class="sxs-lookup"><span data-stu-id="8a258-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="8a258-185">Kattintson a kódablak alatti **Naplók** elemre a naplók panel kibontásához.</span><span class="sxs-lookup"><span data-stu-id="8a258-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="8a258-186">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="8a258-186">Click **Save**.</span></span> <span data-ttu-id="8a258-187">Ellenőrizze a naplók panelen, hogy a függvény mentése sikerült-e, és nem tapasztalhatók-e hibák.</span><span class="sxs-lookup"><span data-stu-id="8a258-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="8a258-188">A kiszolgáló nélküli függvény tesztelése</span><span class="sxs-lookup"><span data-stu-id="8a258-188">Test the serverless function</span></span>

1. <span data-ttu-id="8a258-189">Nyissa meg az alkalmazást egy böngészőben.</span><span class="sxs-lookup"><span data-stu-id="8a258-189">Open the application in a browser.</span></span> <span data-ttu-id="8a258-190">Válasszon ki egy képfájlt, és töltse fel.</span><span class="sxs-lookup"><span data-stu-id="8a258-190">Select an image file and upload it.</span></span> <span data-ttu-id="8a258-191">A feltöltés befejeződött, de mivel még nem adtuk hozzá a képmegjelenítési képességet, az alkalmazás nem jeleníti meg a feltöltött képet.</span><span class="sxs-lookup"><span data-stu-id="8a258-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="8a258-192">A Cloud Shellben erősítse meg, hogy a kép fel lett töltve az **images** tárolóba.</span><span class="sxs-lookup"><span data-stu-id="8a258-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="8a258-193">Győződjön meg arról, hogy a miniatűr létrejött a **thumbnails** tárolóban.</span><span class="sxs-lookup"><span data-stu-id="8a258-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="8a258-194">Szerezze be a miniatűr URL-címét.</span><span class="sxs-lookup"><span data-stu-id="8a258-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="8a258-195">Nyissa meg az URL-címet egy böngészőben, és győződjön meg arról, hogy a miniatűr megfelelően létrejött.</span><span class="sxs-lookup"><span data-stu-id="8a258-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="8a258-196">Mielőtt továbblépne a következő oktatóanyagra, törölje az **images** és a **thumbnails** tárolókban lévő fájlokat.</span><span class="sxs-lookup"><span data-stu-id="8a258-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="8a258-197">Összegzés</span><span class="sxs-lookup"><span data-stu-id="8a258-197">Summary</span></span>

<span data-ttu-id="8a258-198">Ebben a modulban létrehozott egy kiszolgáló nélküli függvényt, amely miniatűröket hoz létre, amikor feltölt egy képet egy Blob Storage-tárolóba.</span><span class="sxs-lookup"><span data-stu-id="8a258-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="8a258-199">A következő oktatóanyagból megtudhatja, hogyan használhatja az Azure Cosmos DB-t a képek metaadatainak tárolására és listázására.</span><span class="sxs-lookup"><span data-stu-id="8a258-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
