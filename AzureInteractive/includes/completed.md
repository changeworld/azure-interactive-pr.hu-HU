---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079476"
---
Sikeresen létrehozott egy teljes körű, kiszolgáló nélküli alkalmazást Azure-szolgáltatásokkal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazás használatával, az alábbi paranccsal törölheti az oktatóanyag során létrehozott erőforrásokat:

```azurecli
az group delete --name first-serverless-app
```

Amikor a rendszer kéri, írja be a `y` parancsot.  

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Azure-blobtároló konfigurálása statikus webhely üzemeltetésére és képek feltöltésére.
> * Képek feltöltése az Azure-blobtárolóba az Azure Functions használatával.
> * Képek átméretezése az Azure Functions használatával.
> * Képek metaadatainak tárolása az Azure Cosmos DB-ben.
> * Képfeliratok automatikus létrehozása a Cognitive Services Vision API használatával.
> * A webalkalmazás védelme a felhasználók Azure Active Directoryval való hitelesítésével.

Ha tudni szeretné, hogyan csatlakoztathat még több szolgáltatást a Functionshöz, lépjen tovább a Logic Apps oktatóanyagára. 

> [!div class="nextstepaction"]
> [A Functions és a Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)