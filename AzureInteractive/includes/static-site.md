Az Azure Blob Storage egy alacsony költségű és nagymértékben skálázható szolgáltatás, amely statikus fájlok tárolására használható. Ebben az oktatóanyagban arra használja, hogy statikus tartalmat (például HTML, JavaScript, CSS) biztosítson a létrehozandó webalkalmazás számára.

## <a name="create-a-storage-account"></a>Storage-fiók létrehozása

A Storage-fiók egy Azure-erőforrás, amelyben táblák, üzenetsorok, fájlok, blobok (tárgyak) és virtuálisgép-lemezek tárolhatók.

1. Jelentkezzen be a Cloud Shellbe (Bash) az **Enter focus mode** (Fókusz mód megnyitása) gomb kiválasztásával. Ez a gomb a lap jobb felső vagy alsó sarkában található, a böngészőablak szélességétől függően. A Fókusz mód a böngészőablak jobb oldalán rögzíti a Cloud Shell-ablakot, így egyszerűen végrehajthatja az oktatóanyagban szereplő parancsokat.

1. Az Azure-ban az erőforráscsoport egy olyan tároló, amely a kapcsolódó Azure-erőforrásokat tárolja a könnyű kezelés érdekében. Hozzon létre egy **first-serverless-app** nevű új erőforráscsoportot.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. Az ehhez az oktatóanyaghoz tartozó statikus tartalom (HTML, CSS és JavaScript-fájlok), a Blob Storage-ban található. A Blob Storage használatához Storage-fiókra van szükség. Hozzon létre egy Storage-fiókot (általános célú V2) az erőforráscsoportban. A `<storage account name>` sztringet cserélje le egy egyedi névre.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Az [Azure Portal](https://portal.azure.com) tetején található keresősávval keresse meg az imént létrehozott tárfiókot, majd nyissa meg.

1. A bal oldali navigációs sávon válassza a **Statikus webhely (előzetes verzió)** elemet, és konfiguráljon egy tárolót a statikus webhely tárolására.
    - Válassza az **Engedélyezve** elemet a statikus webhely engedélyezéséhez.
    - Adja meg az *index.html* értéket az indexdokumentum neveként. A mezőben szürke betűvel már látható az *index.html* érték, de ez csak egy mintaszöveg, és ezt az értéket be kell írnia a mezőbe.
    - Kattintson a **Save** (Mentés) gombra.
    
    ![Statikus webhely beállításainak megadása](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Mentse az **Elsődleges végpontot** olyan helyre, ahonnan könnyen ki tudja másolni az oktatóanyag elvégzése közben. Ez a webalkalmazás URL-címe.

## <a name="upload-the-web-application"></a>A webalkalmazás feltöltése

1. Az oktatóanyagban létrehozott alkalmazás forrásfájljai egy [GitHub-adattárban](https://github.com/Azure-Samples/functions-first-serverless-web-application) találhatók. Győződjön meg arról, hogy a kezdőkönyvtárban van a Cloud Shellben, és klónozza ezt az adattárat.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    A rendszer klónozza az adattárat a következő helyre: `/home/<username>/functions-first-serverless-web-application`.

1. Az ügyféloldali webalkalmazás a **www** mappában található, és a Vue.js JavaScript-keretrendszerrel lett létrehozva. Lépjen a mappába, és futtassa az npm-parancsokat az alkalmazás függőségeinek telepítéséhez és az alkalmazás létrehozásához. Az utolsó parancsok végrehajtása több percig is eltarthat.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    Az alkalmazás létrejön a **dist** mappában.

1. A jelenlegi könyvtárat váltsa a **dist** mappára, és töltse fel az alkalmazást az **$web** blobtárolóba.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Nyissa meg a Storage statikus webhelyek elsődleges végpontjának URL-címét egy webböngészőben az alkalmazás megtekintéséhez.

    ![Az első kiszolgáló nélküli webalkalmazás kezdőlapja](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Összegzés

Ebben a modulban létrehozott egy **first-serverless-app** nevű erőforráscsoportot, amely egy Storage-fiókot tartalmaz. A Storage-fiókban lévő, **$web** nevű blobtároló tárolja és nyilvánosan elérhetővé teszi a webalkalmazás statikus tartalmát. A következőkben megismerheti, hogyan tölthet fel képeket a webalkalmazásból a Blob Storage-ba kiszolgáló nélküli függvényekkel.
