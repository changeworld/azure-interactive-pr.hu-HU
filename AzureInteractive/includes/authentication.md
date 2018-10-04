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
Az Azure App Service-hitelesítés lehetővé teszi a kulcsrakész hitelesítéstámogatást az Azure-függvényalkalmazásokban. Zökkenőmentesen integrálható a Facebookkal, a Twitterrel, a Microsoft-fiókkal, a Google-lel és Azure Active Directory alkalmazásokat. Az App Service-hitelesítés hozzáadása a webalkalmazás háttérrendszeri API-k védelmére szolgál.

## <a name="enable-app-service-authentication"></a>Az App Service-hitelesítés engedélyezése

1. Nyissa meg a függvényalkalmazást az Azure Portalon.

1. A **Platformfunkciók** területen válassza a **Hitelesítés/Engedélyezés** lehetőséget.

    ![Hitelesítés / Engedélyezés kiválasztása](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Válassza ki a következő értékeket:
    
    | Beállítás      |  Ajánlott érték   | Leírás                                        |
    | --- | --- | ---|
    | **App Service-hitelesítés** | Bekapcsolva | Engedélyezze a hitelesítést. |
    | **Nem hitelesített kérés esetén elvégzendő művelet** | Bejelentkezés az Azure Active Directoryval | Válasszon ki egy konfigurált hitelesítési módszert (alább). |
    | **Hitelesítésszolgáltatók** | Lásd alább | Lásd alább |
    | **Jogkivonat-tároló** | Bekapcsolva | Engedélyezze az App Service-nek a jogkivonatok tárolását és kezelését. |
    | **Engedélyezett külső átirányítási URL-címek** | Az Ön alkalmazásának URL-címe, például: https://firstserverlessweb.z4.web.core.windows.net/ | URL-cím(ek), amely(ek)re az App Service átirányíthatja a felhasználókat hitelesítés után. |

1. Válassza az **Azure Active Directory** lehetőséget az **Azure Active Directory-beállítások** megjelenítéséhez.

    1. A **Felügyeleti mód** beállításnál válassza az **Expressz** értéket, és adja meg a következő információkat.
    
        | Beállítás      |  Ajánlott érték   | Leírás                                        |
        | --- | --- | ---|
        | **Felügyeleti mód** | Expressz, Új AD-alkalmazás létrehozása | Egy szolgáltatásnév és az Azure Active Directory-hitelesítés automatikus beállítása. |
        | **Alkalmazás létrehozása** | my-serverless-webapp | Adjon meg egy egyedi alkalmazásnevet. |
    
    1. Kattintson az **OK** gombra az Azure Active Directory-beállítások mentéséhez.

    ![Hitelesítés/Engedélyezés és Azure Active Directory-beállítások](media/functions-first-serverless-web-app/6-create-aad.png)

1. Kattintson a **Save** (Mentés) gombra.


## <a name="modify-the-web-app-to-enable-authentication"></a>A webalkalmazás módosítása a hitelesítés engedélyezéséhez

1. Győződjön meg róla a Cloud Shellben, hogy a **www/dist** mappa az aktuális könyvtár.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. A függvényalkalmazásban való hitelesítés engedélyezéséhez fűzze hozzá a következő kódsort a **settings.js**-fájlhoz.

    `window.authEnabled = true`

    Hajtsa végre a módosítást, és tekintse meg az eredményt az alábbi parancsokkal, vagy egy parancssor-szerkesztővel, például a VIM-mel.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Erősítse meg a fájl módosítását.

    ```azurecli
    cat settings.js
    ```

1. Töltse fel a fájlt a Blob Storage-ba.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. Nyissa meg az alkalmazást egy böngészőben. Kattintson a **Bejelentkezés** elemre, és jelentkezzen be.

1. Válasszon ki egy képfájlt, és töltse fel.

    ![Bejelentkezés lap](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Összegzés

Ebben a modulban megtanulta, hogyan adhat hitelesítést az alkalmazáshoz az Azure App Service-hitelesítéssel.
