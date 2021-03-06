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
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460024"
---
Ebben az oktatóanyagban egy egyszerű webalkalmazást helyezünk üzembe, amely egy HTML-alapú felhasználói felületet jelenít meg. Egy kiszolgáló nélküli háttérmodul lehetővé teszi az alkalmazás számára, hogy képeket töltsön fel, és automatikusan a képeket leíró feliratokat kapjon vissza.

![Futó webalkalmazás](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

Az alábbi ábra az alkalmazás által használt Azure-szolgáltatásokat mutatja be:

1. A Blob Storage-tároló szolgáltatja a statikus webtartalmakat (HTML, CSS, JS), és tárolja a képeket.
2. Az Azure Functions kezeli a képek feltöltését, átméretezését, valamint a metaadatok tárolását.
3. A Cosmos DB tárolja a képek metaadatait.
4. A Logic Apps lekéri a képek feliratait a Computer Vision API-ról.
5. Az Azure Active Directory a felhasználók hitelesítését kezeli.

![A megoldás architektúrájának ábrája](media/functions-first-serverless-web-app/0-architecture.jpg)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure-blobtároló konfigurálása statikus webhely üzemeltetésére és képek feltöltésére.
> * Képek feltöltése az Azure-blobtárolóba az Azure Functions használatával.
> * Képek átméretezése az Azure Functions használatával.
> * Képek metaadatainak tárolása az Azure Cosmos DB-ben.
> * Képfeliratok automatikus létrehozása a Cognitive Services Vision API használatával.
> * A webalkalmazás védelme a felhasználók Azure Active Directoryval való hitelesítésével.
