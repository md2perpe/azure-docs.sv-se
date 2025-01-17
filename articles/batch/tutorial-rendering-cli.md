---
title: Rendera en scen i molnet – Azure Batch
description: Självstudie – Så renderar du en Autodesk 3ds Max-scen med Arnold med hjälp av Batch Rendering Service och kommandoradsgränssnittet i Azure
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: tutorial
ms.date: 12/11/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 5abc2e673438a1ffa22e8d010bf2ee395cd521ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127312"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>Självstudie: Rendera en scen med Azure Batch 

Azure Batch har renderingsfunktioner i molnskala där du betalar per användning. Azure Batch har stöd för renderingsappar som Autodesk Maya, 3ds Max, Arnold och V-Ray. I den här självstudien visas hur du renderar en liten scen med Batch med hjälp av kommandoradsgränssnittet i Azure. Lär dig att:

> [!div class="checklist"]
> * ladda upp en scen till Azure-lagringen
> * skapa en Batch-pool för rendering
> * rendera en scen med en bildruta
> * skala poolen och rendera en scen med flera bildrutor
> * ladda ned renderade utdata.

I den här självstudien renderar du en 3ds Max-scen med Batch med ray-tracing-renderaren [Arnold](https://www.autodesk.com/products/arnold/overview). Batch-poolen använder en Azure Marketplace-avbildning med förinstallerade grafik- och renderingsprogram som tillhandahåller licensiering med betalning per användning.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en användningsbaserad prenumeration eller annat Azure-köpalternativ för att använda renderingsprogram i Batch för betalning per användningstillfälle. **Användningsbaserad licensiering stöds inte om du använder ett kostnadsfritt Azure-erbjudande som ger penningkredit.**

3ds Max-exempelscenen till den här självstudien finns på [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene), tillsammans med ett Bash-exempelskript och JSON-konfigurationsfiler. 3ds Max-scenen kommer från [Autodesk 3ds Max-exempelfilerna](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe). (Autodesk 3ds Max-exempelfilerna är tillgängliga under en Creative Commons Attribution-NonCommercial-Share Alike-licens. Copyright © Autodesk, Inc.)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

Om du inte redan gjort det skapar du en resursgrupp, ett Batch-konto och ett länkat lagringskonto i din prenumeration. 

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

Skapa ett Azure Storage-konto i resursgruppen med kommandot [az storage account create](/cli/azure/storage/account#az-storage-account-create). I den här självstudien använder du lagringskontot till att lagra indata i form av en 3ds Max-scen samt renderade utdata.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
Skapa ett Batch-konto med kommandot [az batch account create](/cli/azure/batch/account#az-batch-account-create). I följande exempel skapas ett Batch-konto med namnet *mybatchaccount* i *myResourceGroup*, med en länk till det lagringskonto du skapade.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Du måste autentisera med Batch för att skapa och hantera beräkningspooler och jobb. Logga in på kontot med kommandot [az batch account login](/cli/azure/batch/account#az-batch-account-login). När du har loggat in använder dina `az batch`-kommandon den här kontokontexten. I följande exempel används autentisering med delad nyckel, baserat på kontonamn och nyckel för Batch. Batch har även stöd för autentisering via [Azure Active Directory](batch-aad-auth.md) när enskilda användare eller övervakade program ska autentiseras.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>Ladda upp en scen till lagringen

När du ska ladda upp indatascenen till lagringen måste du först komma åt lagringskontot och skapa en målcontainer för blobarna. Du kommer åt Azure Storage-kontot genom att exportera miljövariablerna `AZURE_STORAGE_KEY` och `AZURE_STORAGE_ACCOUNT`. I det första Bash-skalkommandot används kommandot [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) till att hämta den första kontonyckeln. När du ställer in de här miljövariablerna använder dina storage-kommandon den här kontokontexten.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

Skapa sedan en blob-container i lagringskontot för scenfilerna. I följande exempel används kommandot [az storage container create](/cli/azure/storage/container#az-storage-container-create) till att skapa en blobbehållare med namnet *scenefiles* som har offentlig läsbehörighet.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

Ladda ned scenen `MotionBlur-Dragon-Flying.max` från [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) till en lokal arbetskatalog. Exempel:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

Ladda upp scenfilen från din lokala arbetskatalog till blob-containern. I följande exempel används kommandot [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) som kan överföra flera filer:

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>Skapa en renderingspool

Skapa en Batch-pool för rendering med kommandot [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). I det här exemplet anger du inställningarna för poolen i en JSON-fil. Skapa en fil med namnet *mypool.json* i ditt nuvarande gränssnitt. Kopiera och klistra in följande innehåll. Se till att all text kopieras på rätt sätt. (Du kan ladda ned filen från [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json).)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.2"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch har stöd för dedikerade noder och [noder med låg prioritet](batch-low-pri-vms.md), och du kan använda en av eller båda typerna i dina pooler. Dedikerade noder är reserverade för din pool. Noder med låg prioritet erbjuds till ett reducerat pris från VM-överskottskapacitet i Azure. Noder med låg prioritet är inte tillgängliga om Azure inte har tillräckligt med kapacitet. 

Den angivna poolen innehåller en enda nod med låg prioritet som kör en Windows Server-avbildning med programvara för Batch Rendering-tjänsten. Den här poolen är licensierad för rendering med 3ds Max och Arnold. I ett senare steg skalar du ut poolen till ett större antal noder.

Skapa poolen genom att skicka JSON-filen till kommandot `az batch pool create`:

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
Det tar några minuter att etablera poolen. Om du vill se status för poolen kör du kommandot [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). Följande kommando hämtar allokeringstillståndet för poolen:

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

Fortsätt med följande steg för att skapa ett jobb och aktiviteter medan pooltillståndet ändras. Poolen är helt etablerad när allokeringstillståndet är `steady` och noderna körs.  

## <a name="create-a-blob-container-for-output"></a>Skapa en blob-container för utdata

I exemplen i den här självstudiekursen skapas en utdatafil för varje uppgift i renderingsjobbet. Innan du schemalägger jobbet ska du skapa en blob-container i lagringskontot, som mål för utdatafilerna. I följande exempel används kommandot [az storage container create](/cli/azure/storage/container#az-storage-container-create) till att skapa behållaren *job-myrenderjob* som har offentlig läsbehörighet. 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

Om du ska kunna skriva utdatafilerna till containern måste Batch använda en SAS-token. Skapa token med kommandot [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas). I det här exemplet skapas en token för skrivning till valfri blob-container i kontot, och token upphör att gälla den 15 november 2018:

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2019-11-15
```

Ta vara på den token som returneras av kommandot, den ser ut ungefär så här: Du använder denna token i ett senare steg.

```
se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>Rendera en scen med en bildruta

### <a name="create-a-job"></a>Skapa ett jobb

Skapa ett renderingsjobb som ska köras i poolen med hjälp av kommandot [az batch job create](/cli/azure/batch/job#az-batch-job-create). Från början har jobbet inga uppgifter.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>Skapa en uppgift

Använd kommandot [az batch task create](/cli/azure/batch/task#az-batch-task-create) till att skapa en renderingsuppgift i jobbet. I det här exemplet anger du uppgiftsinställningarna i en JSON-fil. Skapa en fil med namnet *myrendertask.json* i ditt nuvarande gränssnitt. Kopiera och klistra in följande innehåll. Se till att all text kopieras på rätt sätt. (Du kan ladda ned filen från [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json).)

Uppgiften anger ett 3ds Max-kommando för rendering av en enda bildruta från scenen *MotionBlur-DragonFlying.max*.

Ändra elementen `blobSource` och `containerURL` i JSON-filen så att de innehåller namnet på ditt lagringskonto och din SAS-token. 

> [!TIP]
> Din `containerURL` slutar med SAS-token och ser ut ungefär så här:
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "blobSource": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

Lägg till uppgiften i jobbet med följande kommando:

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch schemalägger uppgiften och uppgiften körs så snart en nod i poolen är tillgänglig.


### <a name="view-task-output"></a>Visa utdata för uppgiften

Det tar några minuter att köra uppgiften. Använd kommandot [az batch task show](/cli/azure/batch/task#az-batch-task-show) till att visa information om uppgiften.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

Uppgiften genererar *dragon0001.jpg* vid beräkningsnoden och laddar upp den till containern *job-myrenderjob* i lagringskontot. Om du vill visa utdata laddar du ned filen från lagringen till din lokala dator med kommandot [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download).

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

Öppna *dragon.jpg* på datorn. Den renderade bilden ser ut ungefär så här:

![Renderad drakbild 1](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>Skala ut poolen

Ändra nu poolen så att den är redo för ett större renderingsjobb med flera bildrutor. Batch har ett antal olika sätt att skala beräkningsresurser, till exempel [autoskalning](batch-automatic-scaling.md) där noder läggs till eller tas bort när uppgiftsbehoven ändras. I det här enkla exemplet används kommandot [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) till att öka antalet noder med låg prioritet i poolen till *6*:

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

Det tar några minuter att ändra storlek på poolen. Medan den här processen pågår konfigurerar du nästkommande uppgifter att köras i det befintliga renderingsjobbet.

## <a name="render-a-multiframe-scene"></a>Rendera en scen med flera bildrutor

Precis som i exemplet med en bildruta använder du kommandot [az batch task create](/cli/azure/batch/task#az-batch-task-create) till att skapa renderingsuppgifter i jobbet *myrenderjob*. Här anger du uppgiftsinställningarna i en JSON-fil med namnet *myrendertask_multi.json*. (Du kan ladda ned filen från [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json).) Var och en av de sex uppgifterna anger en Arnold-kommandorad för rendering av en bildruta från 3ds Max-scenen *MotionBlur-DragonFlying.max*.

Skapa en fil med namnet *myrendertask_multi.json* i ditt aktuella gränssnitt. Kopiera och klistra in innehållet från filen du hämtade. Ändra elementen `blobSource` och `containerURL` i JSON-filen så att de innehåller namnet på ditt lagringskonto och din SAS-token. Kom ihåg att ändra inställningarna för var och en av de sex uppgifterna. Spara filen och kör följande kommando för att placera uppgifterna i kö:

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>Visa utdata för uppgiften

Det tar några minuter att köra uppgiften. Använd kommandot [az batch task list](/cli/azure/batch/task#az-batch-task-list) till att visa status för uppgifterna. Exempel:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

Använd kommandot [az batch task show](/cli/azure/batch/task#az-batch-task-show) till att visa information om enskilda uppgifter. Exempel:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
Uppgifterna genererar utdatafiler med namnen *dragon0002.jpg* - *dragon0007.jpg* på beräkningsnoderna och laddar upp dem till containern *job-myrenderjob* i lagringskontot. Om du vill visa utdata laddar du ned filen till en lokal mapp med kommandot [az storage blob download](/cli/azure/storage/blob). Exempel:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

Öppna en av filerna på datorn. Den renderade bildrutan ser ut ungefär så här:

![Renderad drakbild 6](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, Batch-kontot, poolerna och alla relaterade resurser. Ta bort resurserna på följande sätt:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * ladda upp scener till Azure Storage
> * skapa en Batch-pool för rendering
> * rendera en scen med en enda bildruta med Arnold
> * skala poolen och rendera en scen med flera bildrutor
> * ladda ned renderade utdata.

Mer information om rendering i molnskala finns bland alternativ för Batch Rendering-tjänsten. 

> [!div class="nextstepaction"]
> [Batch-tjänsten för återgivning](batch-rendering-service.md)
