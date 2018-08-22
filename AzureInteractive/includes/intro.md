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
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079461"
---
<span data-ttu-id="85148-103">Ebben az oktatóanyagban egy egyszerű webalkalmazást helyezünk üzembe, amely egy HTML-alapú felhasználói felületet jelenít meg.</span><span class="sxs-lookup"><span data-stu-id="85148-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="85148-104">Egy kiszolgáló nélküli háttérmodul lehetővé teszi az alkalmazás számára, hogy képeket töltsön fel, és automatikusan a képeket leíró feliratokat kapjon vissza.</span><span class="sxs-lookup"><span data-stu-id="85148-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Futó webalkalmazás](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="85148-106">Az alábbi ábra az alkalmazás által használt Azure-szolgáltatásokat mutatja be:</span><span class="sxs-lookup"><span data-stu-id="85148-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="85148-107">A Blob Storage-tároló szolgáltatja a statikus webtartalmakat (HTML, CSS, JS), és tárolja a képeket.</span><span class="sxs-lookup"><span data-stu-id="85148-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="85148-108">Az Azure Functions kezeli a képek feltöltését, átméretezését, valamint a metaadatok tárolását.</span><span class="sxs-lookup"><span data-stu-id="85148-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="85148-109">A Cosmos DB tárolja a képek metaadatait.</span><span class="sxs-lookup"><span data-stu-id="85148-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="85148-110">A Logic Apps lekéri a képek feliratait a Computer Vision API-ról.</span><span class="sxs-lookup"><span data-stu-id="85148-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="85148-111">Az Azure Active Directory a felhasználók hitelesítését kezeli.</span><span class="sxs-lookup"><span data-stu-id="85148-111">Azure Active Directory manages user authentication.</span></span>

![A megoldás architektúrájának ábrája](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="85148-113">Eben az oktatóanyagban az alábbiakkal fog megismerkedni:</span><span class="sxs-lookup"><span data-stu-id="85148-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="85148-114">Azure-blobtároló konfigurálása statikus webhely üzemeltetésére és képek feltöltésére.</span><span class="sxs-lookup"><span data-stu-id="85148-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="85148-115">Képek feltöltése az Azure-blobtárolóba az Azure Functions használatával.</span><span class="sxs-lookup"><span data-stu-id="85148-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="85148-116">Képek átméretezése az Azure Functions használatával.</span><span class="sxs-lookup"><span data-stu-id="85148-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="85148-117">Képek metaadatainak tárolása az Azure Cosmos DB-ben.</span><span class="sxs-lookup"><span data-stu-id="85148-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="85148-118">Képfeliratok automatikus létrehozása a Cognitive Services Vision API használatával.</span><span class="sxs-lookup"><span data-stu-id="85148-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="85148-119">A webalkalmazás védelme a felhasználók Azure Active Directoryval való hitelesítésével.</span><span class="sxs-lookup"><span data-stu-id="85148-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>