<span data-ttu-id="6dea4-101">Az Azure Blob Storage egy alacsony költségű és nagymértékben skálázható szolgáltatás, amely statikus fájlok tárolására használható.</span><span class="sxs-lookup"><span data-stu-id="6dea4-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="6dea4-102">Ebben az oktatóanyagban arra használja, hogy statikus tartalmat (például HTML, JavaScript, CSS) biztosítson a létrehozandó webalkalmazás számára.</span><span class="sxs-lookup"><span data-stu-id="6dea4-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="6dea4-103">Storage-fiók létrehozása</span><span class="sxs-lookup"><span data-stu-id="6dea4-103">Create a Storage account</span></span>

<span data-ttu-id="6dea4-104">A Storage-fiók egy Azure-erőforrás, amelyben táblák, üzenetsorok, fájlok, blobok (tárgyak) és virtuálisgép-lemezek tárolhatók.</span><span class="sxs-lookup"><span data-stu-id="6dea4-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="6dea4-105">Jelentkezzen be a Cloud Shellbe (Bash) az **Enter focus mode** (Fókusz mód megnyitása) gomb kiválasztásával.</span><span class="sxs-lookup"><span data-stu-id="6dea4-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="6dea4-106">Ez a gomb a lap jobb felső vagy alsó sarkában található, a böngészőablak szélességétől függően.</span><span class="sxs-lookup"><span data-stu-id="6dea4-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="6dea4-107">A Fókusz mód a böngészőablak jobb oldalán rögzíti a Cloud Shell-ablakot, így egyszerűen végrehajthatja az oktatóanyagban szereplő parancsokat.</span><span class="sxs-lookup"><span data-stu-id="6dea4-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="6dea4-108">Az Azure-ban az erőforráscsoport egy olyan tároló, amely a kapcsolódó Azure-erőforrásokat tárolja a könnyű kezelés érdekében.</span><span class="sxs-lookup"><span data-stu-id="6dea4-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="6dea4-109">Hozzon létre egy **first-serverless-app** nevű új erőforráscsoportot.</span><span class="sxs-lookup"><span data-stu-id="6dea4-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="6dea4-110">Az ehhez az oktatóanyaghoz tartozó statikus tartalom (HTML, CSS és JavaScript-fájlok), a Blob Storage-ban található.</span><span class="sxs-lookup"><span data-stu-id="6dea4-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="6dea4-111">A Blob Storage használatához Storage-fiókra van szükség.</span><span class="sxs-lookup"><span data-stu-id="6dea4-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="6dea4-112">Hozzon létre egy Storage-fiókot (általános célú V2) az erőforráscsoportban.</span><span class="sxs-lookup"><span data-stu-id="6dea4-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="6dea4-113">A `<storage account name>` sztringet cserélje le egy egyedi névre.</span><span class="sxs-lookup"><span data-stu-id="6dea4-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="6dea4-114">Az [Azure Portal](https://portal.azure.com) tetején található keresősávval keresse meg az imént létrehozott tárfiókot, majd nyissa meg.</span><span class="sxs-lookup"><span data-stu-id="6dea4-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="6dea4-115">A bal oldali navigációs sávon válassza a **Statikus webhely (előzetes verzió)** elemet, és konfiguráljon egy tárolót a statikus webhely tárolására.</span><span class="sxs-lookup"><span data-stu-id="6dea4-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="6dea4-116">Válassza az **Engedélyezve** elemet a statikus webhely engedélyezéséhez.</span><span class="sxs-lookup"><span data-stu-id="6dea4-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="6dea4-117">Adja meg az *index.html* értéket az indexdokumentum neveként.</span><span class="sxs-lookup"><span data-stu-id="6dea4-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="6dea4-118">A mezőben szürke betűvel már látható az *index.html* érték, de ez csak egy mintaszöveg, és ezt az értéket be kell írnia a mezőbe.</span><span class="sxs-lookup"><span data-stu-id="6dea4-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="6dea4-119">Kattintson a **Save** (Mentés) gombra.</span><span class="sxs-lookup"><span data-stu-id="6dea4-119">Click **Save**.</span></span>
    
    ![Statikus webhely beállításainak megadása](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="6dea4-121">Mentse az **Elsődleges végpontot** olyan helyre, ahonnan könnyen ki tudja másolni az oktatóanyag elvégzése közben.</span><span class="sxs-lookup"><span data-stu-id="6dea4-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="6dea4-122">Ez a webalkalmazás URL-címe.</span><span class="sxs-lookup"><span data-stu-id="6dea4-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="6dea4-123">A webalkalmazás feltöltése</span><span class="sxs-lookup"><span data-stu-id="6dea4-123">Upload the web application</span></span>

1. <span data-ttu-id="6dea4-124">Az oktatóanyagban létrehozott alkalmazás forrásfájljai egy [GitHub-adattárban](https://github.com/Azure-Samples/functions-first-serverless-web-application) találhatók.</span><span class="sxs-lookup"><span data-stu-id="6dea4-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="6dea4-125">Győződjön meg arról, hogy a kezdőkönyvtárban van a Cloud Shellben, és klónozza ezt az adattárat.</span><span class="sxs-lookup"><span data-stu-id="6dea4-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="6dea4-126">A rendszer klónozza az adattárat a következő helyre: `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="6dea4-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="6dea4-127">Az ügyféloldali webalkalmazás a **www** mappában található, és a Vue.js JavaScript-keretrendszerrel lett létrehozva.</span><span class="sxs-lookup"><span data-stu-id="6dea4-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="6dea4-128">Lépjen a mappába, és futtassa az npm-parancsokat az alkalmazás függőségeinek telepítéséhez és az alkalmazás létrehozásához.</span><span class="sxs-lookup"><span data-stu-id="6dea4-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="6dea4-129">Az utolsó parancsok végrehajtása több percig is eltarthat.</span><span class="sxs-lookup"><span data-stu-id="6dea4-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="6dea4-130">Az alkalmazás létrejön a **dist** mappában.</span><span class="sxs-lookup"><span data-stu-id="6dea4-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="6dea4-131">A jelenlegi könyvtárat váltsa a **dist** mappára, és töltse fel az alkalmazást az **$web** blobtárolóba.</span><span class="sxs-lookup"><span data-stu-id="6dea4-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="6dea4-132">Nyissa meg a Storage statikus webhelyek elsődleges végpontjának URL-címét egy webböngészőben az alkalmazás megtekintéséhez.</span><span class="sxs-lookup"><span data-stu-id="6dea4-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Az első kiszolgáló nélküli webalkalmazás kezdőlapja](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="6dea4-134">Összegzés</span><span class="sxs-lookup"><span data-stu-id="6dea4-134">Summary</span></span>

<span data-ttu-id="6dea4-135">Ebben a modulban létrehozott egy **first-serverless-app** nevű erőforráscsoportot, amely egy Storage-fiókot tartalmaz.</span><span class="sxs-lookup"><span data-stu-id="6dea4-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="6dea4-136">A Storage-fiókban lévő, **$web** nevű blobtároló tárolja és nyilvánosan elérhetővé teszi a webalkalmazás statikus tartalmát.</span><span class="sxs-lookup"><span data-stu-id="6dea4-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="6dea4-137">A következőkben megismerheti, hogyan tölthet fel képeket a webalkalmazásból a Blob Storage-ba kiszolgáló nélküli függvényekkel.</span><span class="sxs-lookup"><span data-stu-id="6dea4-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
