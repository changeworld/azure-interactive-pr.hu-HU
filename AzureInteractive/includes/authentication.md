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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460041"
---
<span data-ttu-id="ac8d0-103">Az Azure App Service-hitelesítés lehetővé teszi a kulcsrakész hitelesítéstámogatást az Azure-függvényalkalmazásokban.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="ac8d0-104">Zökkenőmentesen integrálható a Facebookkal, a Twitterrel, a Microsoft-fiókkal, a Google-lel és Azure Active Directory alkalmazásokat.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="ac8d0-105">Az App Service-hitelesítés hozzáadása a webalkalmazás háttérrendszeri API-k védelmére szolgál.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="ac8d0-106">Az App Service-hitelesítés engedélyezése</span><span class="sxs-lookup"><span data-stu-id="ac8d0-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="ac8d0-107">Nyissa meg a függvényalkalmazást az Azure Portalon.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="ac8d0-108">A **Platformfunkciók** területen válassza a **Hitelesítés/Engedélyezés** lehetőséget.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Hitelesítés / Engedélyezés kiválasztása](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="ac8d0-110">Válassza ki a következő értékeket:</span><span class="sxs-lookup"><span data-stu-id="ac8d0-110">Select the following values:</span></span>
    
    | <span data-ttu-id="ac8d0-111">Beállítás</span><span class="sxs-lookup"><span data-stu-id="ac8d0-111">Setting</span></span>      |  <span data-ttu-id="ac8d0-112">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="ac8d0-112">Suggested value</span></span>   | <span data-ttu-id="ac8d0-113">Leírás</span><span class="sxs-lookup"><span data-stu-id="ac8d0-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="ac8d0-114">**App Service-hitelesítés**</span><span class="sxs-lookup"><span data-stu-id="ac8d0-114">**App Service Authentication**</span></span> | <span data-ttu-id="ac8d0-115">Bekapcsolva</span><span class="sxs-lookup"><span data-stu-id="ac8d0-115">On</span></span> | <span data-ttu-id="ac8d0-116">Engedélyezze a hitelesítést.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-116">Enable authentication.</span></span> |
    | <span data-ttu-id="ac8d0-117">**Nem hitelesített kérés esetén elvégzendő művelet**</span><span class="sxs-lookup"><span data-stu-id="ac8d0-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="ac8d0-118">Bejelentkezés az Azure Active Directoryval</span><span class="sxs-lookup"><span data-stu-id="ac8d0-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="ac8d0-119">Válasszon ki egy konfigurált hitelesítési módszert (alább).</span><span class="sxs-lookup"><span data-stu-id="ac8d0-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="ac8d0-120">**Hitelesítésszolgáltatók**</span><span class="sxs-lookup"><span data-stu-id="ac8d0-120">**Authentication Providers**</span></span> | <span data-ttu-id="ac8d0-121">Lásd alább</span><span class="sxs-lookup"><span data-stu-id="ac8d0-121">See below</span></span> | <span data-ttu-id="ac8d0-122">Lásd alább</span><span class="sxs-lookup"><span data-stu-id="ac8d0-122">See below</span></span> |
    | <span data-ttu-id="ac8d0-123">**Jogkivonat-tároló**</span><span class="sxs-lookup"><span data-stu-id="ac8d0-123">**Token store**</span></span> | <span data-ttu-id="ac8d0-124">Bekapcsolva</span><span class="sxs-lookup"><span data-stu-id="ac8d0-124">On</span></span> | <span data-ttu-id="ac8d0-125">Engedélyezze az App Service-nek a jogkivonatok tárolását és kezelését.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="ac8d0-126">**Engedélyezett külső átirányítási URL-címek**</span><span class="sxs-lookup"><span data-stu-id="ac8d0-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="ac8d0-127">Az Ön alkalmazásának URL-címe, például: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="ac8d0-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="ac8d0-128">URL-cím(ek), amely(ek)re az App Service átirányíthatja a felhasználókat hitelesítés után.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="ac8d0-129">Válassza az **Azure Active Directory** lehetőséget az **Azure Active Directory-beállítások** megjelenítéséhez.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="ac8d0-130">A **Felügyeleti mód** beállításnál válassza az **Expressz** értéket, és adja meg a következő információkat.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="ac8d0-131">Beállítás</span><span class="sxs-lookup"><span data-stu-id="ac8d0-131">Setting</span></span>      |  <span data-ttu-id="ac8d0-132">Ajánlott érték</span><span class="sxs-lookup"><span data-stu-id="ac8d0-132">Suggested value</span></span>   | <span data-ttu-id="ac8d0-133">Leírás</span><span class="sxs-lookup"><span data-stu-id="ac8d0-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="ac8d0-134">**Felügyeleti mód**</span><span class="sxs-lookup"><span data-stu-id="ac8d0-134">**Management mode**</span></span> | <span data-ttu-id="ac8d0-135">Expressz, Új AD-alkalmazás létrehozása</span><span class="sxs-lookup"><span data-stu-id="ac8d0-135">Express, Create new AD app</span></span> | <span data-ttu-id="ac8d0-136">Egy szolgáltatásnév és az Azure Active Directory-hitelesítés automatikus beállítása.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="ac8d0-137">**Alkalmazás létrehozása**</span><span class="sxs-lookup"><span data-stu-id="ac8d0-137">**Create app**</span></span> | <span data-ttu-id="ac8d0-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="ac8d0-138">my-serverless-webapp</span></span> | <span data-ttu-id="ac8d0-139">Adjon meg egy egyedi alkalmazásnevet.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="ac8d0-140">Kattintson az **OK** gombra az Azure Active Directory-beállítások mentéséhez.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Hitelesítés/Engedélyezés és Azure Active Directory-beállítások](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="ac8d0-142">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="ac8d0-143">A webalkalmazás módosítása a hitelesítés engedélyezéséhez</span><span class="sxs-lookup"><span data-stu-id="ac8d0-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="ac8d0-144">Győződjön meg róla a Cloud Shellben, hogy a **www/dist** mappa az aktuális könyvtár.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="ac8d0-145">A függvényalkalmazásban való hitelesítés engedélyezéséhez fűzze hozzá a következő kódsort a **settings.js**-fájlhoz.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="ac8d0-146">Hajtsa végre a módosítást, és tekintse meg az eredményt az alábbi parancsokkal, vagy egy parancssor-szerkesztővel, például a VIM-mel.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="ac8d0-147">Erősítse meg a fájl módosítását.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="ac8d0-148">Töltse fel a fájlt a Blob Storage-ba.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="ac8d0-149">Az alkalmazás tesztelése</span><span class="sxs-lookup"><span data-stu-id="ac8d0-149">Test the application</span></span>

1. <span data-ttu-id="ac8d0-150">Nyissa meg az alkalmazást egy böngészőben.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-150">Open the application in a browser.</span></span> <span data-ttu-id="ac8d0-151">Kattintson a **Bejelentkezés** elemre, és jelentkezzen be.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="ac8d0-152">Válasszon ki egy képfájlt, és töltse fel.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-152">Select an image file and upload it.</span></span>

    ![Bejelentkezés lap](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="ac8d0-154">Összegzés</span><span class="sxs-lookup"><span data-stu-id="ac8d0-154">Summary</span></span>

<span data-ttu-id="ac8d0-155">Ebben a modulban megtanulta, hogyan adhat hitelesítést az alkalmazáshoz az Azure App Service-hitelesítéssel.</span><span class="sxs-lookup"><span data-stu-id="ac8d0-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
