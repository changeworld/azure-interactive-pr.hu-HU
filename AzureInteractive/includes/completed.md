---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459997"
---
<span data-ttu-id="4637b-103">Sikeresen létrehozott egy teljes körű, kiszolgáló nélküli alkalmazást Azure-szolgáltatásokkal.</span><span class="sxs-lookup"><span data-stu-id="4637b-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="4637b-104">Az erőforrások eltávolítása</span><span class="sxs-lookup"><span data-stu-id="4637b-104">Clean up resources</span></span>

<span data-ttu-id="4637b-105">Ha végzett az alkalmazás használatával, az alábbi paranccsal törölheti az oktatóanyag során létrehozott erőforrásokat:</span><span class="sxs-lookup"><span data-stu-id="4637b-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="4637b-106">Amikor a rendszer kéri, írja be a `y` parancsot.</span><span class="sxs-lookup"><span data-stu-id="4637b-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="4637b-107">További lépések</span><span class="sxs-lookup"><span data-stu-id="4637b-107">Next steps</span></span>

<span data-ttu-id="4637b-108">Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:</span><span class="sxs-lookup"><span data-stu-id="4637b-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="4637b-109">Azure-blobtároló konfigurálása statikus webhely üzemeltetésére és képek feltöltésére.</span><span class="sxs-lookup"><span data-stu-id="4637b-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="4637b-110">Képek feltöltése az Azure-blobtárolóba az Azure Functions használatával.</span><span class="sxs-lookup"><span data-stu-id="4637b-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="4637b-111">Képek átméretezése az Azure Functions használatával.</span><span class="sxs-lookup"><span data-stu-id="4637b-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="4637b-112">Képek metaadatainak tárolása az Azure Cosmos DB-ben.</span><span class="sxs-lookup"><span data-stu-id="4637b-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="4637b-113">Képfeliratok automatikus létrehozása a Cognitive Services Vision API használatával.</span><span class="sxs-lookup"><span data-stu-id="4637b-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="4637b-114">A webalkalmazás védelme a felhasználók Azure Active Directoryval való hitelesítésével.</span><span class="sxs-lookup"><span data-stu-id="4637b-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="4637b-115">Ha tudni szeretné, hogyan csatlakoztathat még több szolgáltatást a Functionshöz, lépjen tovább a Logic Apps oktatóanyagára.</span><span class="sxs-lookup"><span data-stu-id="4637b-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="4637b-116">A Functions és a Logic Apps</span><span class="sxs-lookup"><span data-stu-id="4637b-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
