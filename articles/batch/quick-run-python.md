---
title: Azure Snabbstart – Köra Batch-jobb – Python
description: Kör snabbt ett Batch-jobb och aktiviteter med hjälp av Batch Python-klientbiblioteket.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: quickstart
ms.date: 11/27/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 5788f6e699833c606b1bdeaf63a9aac13da2a0e9
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513276"
---
# <a name="quickstart-run-your-first-batch-job-with-the-python-api"></a>Snabbstart: Kör ditt första Batch-jobb med Python-API

Denna snabbstart kör ett Azure Batch-jobb från en app som bygger på Azure Batch Python-API: et. Appen överför flera indatafiler till Azure-lagring och skapar sedan en *pool* med Batch-beräkningsnoder (virtuella datorer). Sedan skapar den ett *exempeljobb* som kör *aktiviteter* för att bearbeta varje indatafil på poolen med ett grundläggande kommando. När du har slutfört den här snabbstarten kommer du att förstå huvudbegreppen för Batch-tjänsten och vara redo att testa Batch med mer realistiska arbetsbelastningar i större skala.
 
![Snabbstart för apparbetsflöde](./media/quick-run-python/sampleapp.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 2.7 eller 3.3 eller senare](https://www.python.org/downloads/)

* [pip](https://pip.pypa.io/en/stable/installing/)-pakethanterare

* Ett Azure Batch-konto och ett länkat Azure Storage-konto. För att skapa dessa konton finns Batch-snabbstart med hjälp av [Azure-portalen](quick-create-portal.md) eller [Azure CLI](quick-create-cli.md). 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-the-sample"></a>Hämta exemplet

[Ladda ned eller klona exempelappen](https://github.com/Azure-Samples/batch-python-quickstart) från GitHub. Om du vill klona exempellagringsplatsen med en Git-klient använder du följande kommando:

```bash
git clone https://github.com/Azure-Samples/batch-python-quickstart.git
```

Navigera till den katalog som innehåller Python-skriptet `python_quickstart_client.py`.

I Python-miljön installerar du de nödvändiga paketen med `pip`.

```bash
pip install -r requirements.txt
```

Öppna filen `config.py`. Uppdatera autentiseringssträngarna med Batch- och lagringskontouppgifter med värdena för dina konton. Exempel:

```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

## <a name="run-the-app"></a>Kör appen

Kör skriptet för att se Batch-arbetsflödet i praktiken:

```bash
python python_quickstart_client.py
```

Granska koden för att lära dig vad varje del av appen gör när skriptet har körts. 

När du kör exempelappen ser konsolens utdata ut ungefär så här. Under körningen uppstår det en paus vid `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` medan poolens beräkningsnoder startas. Aktiviteter köas för att köras när den första beräkningsnoden körs. Gå till Batch-kontot i [Azure-portalen](https://portal.azure.com) för att övervaka poolen, beräkningsnoderna, jobbet och aktiviteterna på Batch-kontot.

```output
Sample start: 11/26/2018 4:02:54 PM

Container [input] created.
Uploading file taskdata0.txt to container [input]...
Uploading file taskdata1.txt to container [input]...
Uploading file taskdata2.txt to container [input]...
Creating pool [PythonQuickstartPool]...
Creating job [PythonQuickstartJob]...
Adding 3 tasks to job [PythonQuickstartJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
```

När aktiviteterna har slutförts kan du se utdata som liknar följande för varje aktivitet:

```output
Printing task output...
Task: Task0
Node: tvm-2850684224_3-20171205t000401z
Standard out:
Batch processing began with mainframe computers and punch cards. Today it still plays a central role in business, engineering, science, and other pursuits that require running lots of automated tasks....
...
```

Körningen tar normalt runt 3 minuter om du kör programmet med standardkonfigurationen. Den ursprungliga poolinstallationen tar längst tid.

## <a name="review-the-code"></a>Granska koden

Python-appen i den här snabbstarten gör följande:

* Överför tre små textfiler till en blobcontainer i ditt Azure-lagringskonto. Dessa filer är indata som ska bearbetas av Batch-aktiviteter.
* Skapar en pool med två beräkningsnoder som kör Ubuntu 18.04 LTS.
* Skapar ett jobb och tre aktiviteter som ska köras på noderna. Varje aktivitet bearbetar en av indatafilerna på en kommandorad för Bash-gränssnitt.
* Visar filer som returneras av aktiviteterna.

Se filen `python_quickstart_client.py` och information i följande avsnitt.

### <a name="preliminaries"></a>Förberedelser

För att interagera med ett lagringskonto använder appen paketet [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) för att skapa ett [BlockBlobService](/python/api/azure.storage.blob.blockblobservice.blockblobservice)-objekt.

```python
blob_client = azureblob.BlockBlobService(
    account_name=config._STORAGE_ACCOUNT_NAME,
    account_key=config._STORAGE_ACCOUNT_KEY)
```

Appen använder referensen `blob_client` för att skapa en container i lagringskontot och för att överföra filer till containern. De lagrade filerna har definierats som Batch [ResourceFile](/python/api/azure.batch.models.resourcefile)-objekt som Batch senare kan hämta till beräkningsnoder.

```python
input_file_paths = [os.path.join(sys.path[0], 'taskdata0.txt'),
                    os.path.join(sys.path[0], 'taskdata1.txt'),
                    os.path.join(sys.path[0], 'taskdata2.txt')]

input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

Appen skapar ett [BatchServiceClient](/python/api/azure.batch.batchserviceclient)-objekt för att skapa och hantera pooler, jobb och aktiviteter i Batch-tjänsten. Batch-klienten i exemplet använder autentisering med delad nyckel. Batch har även stöd för Azure Active Directory-autentisering.

```python
credentials = batch_auth.SharedKeyCredentials(config._BATCH_ACCOUNT_NAME,
                                              config._BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    batch_url=config._BATCH_ACCOUNT_URL)
```

### <a name="create-a-pool-of-compute-nodes"></a>Skapa en pool med beräkningsnoder

För att skapa en Batch-pool använder appen klassen [PoolAddParameter](/python/api/azure.batch.models.pooladdparameter) för att ange antalet noder, VM-storlek och en poolkonfiguration. Här anger ett [VirtualMachineConfiguration](/python/api/azure.batch.models.virtualmachineconfiguration)-objekt en [ImageReference](/python/api/azure.batch.models.imagereference) till en Ubuntu Server 18.04 LTS-avbildning som har publicerats på Azure Marketplace. Batch stöder ett brett utbud av Linux- och Windows Server-avbildningar på Azure Marketplace samt anpassade VM-avbildningar.

Antalet noder (`_POOL_NODE_COUNT`) och VM-storlek (`_POOL_VM_SIZE`) är definierade konstanter. Exemplet skapar som standard en pool med 2 noder i storleken *Standard_A1_v2*. Storleken som föreslås erbjuder en bra balans mellan prestanda och kostnad för det här snabba exemplet.

Metoden [pool.add](/python/api/azure.batch.operations.pooloperations) skickar poolen till Batch-tjänsten.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
        ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=config._POOL_VM_SIZE,
    target_dedicated_nodes=config._POOL_NODE_COUNT
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-batch-job"></a>Skapa ett Batch-jobb

Ett Batch-jobb är en logisk gruppering av en eller flera aktiviteter. Ett jobb omfattar inställningar som är gemensamma för aktiviteter, till exempel prioritet och vilken pool som aktiviteterna ska köras på. Appen använder klassen [JobAddParameter](/python/api/azure.batch.models.jobaddparameter) för att skapa ett jobb på din pool. Metoden [job.add](/python/api/azure.batch.operations.joboperations) skickar poolen till Batch-tjänsten. Från början har jobbet inga aktiviteter.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))
batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Skapa aktiviteter

Appen skapar en lista med aktivitetsobjekt med hjälp av klassen [TaskAddParameter](/python/api/azure.batch.models.taskaddparameter). Varje aktivitet bearbetar ett indata `resource_files`-objekt med en `command_line`-parameter. I det här exemplet kör kommandoraden Bash-gränssnittskommandot `cat` för att visa textfilen. Detta kommando är ett enkelt exempel i demonstrationssyfte. När du använder Batch är det på kommandoraden som du anger din app eller ditt skript. Batch tillhandahåller ett antal sätt att distribuera appar och skript till beräkningsnoder.

Sedan lägger appen till aktiviteter i jobbet med metoden [task.add_collection](/python/api/azure.batch.operations.taskoperations) som köar dem för att köras på beräkningsnoderna. 

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    command = "/bin/bash -c \"cat {}\"".format(input_file.file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="view-task-output"></a>Visa aktivitetens utdata

Appen övervakar aktivitetstillståndet för att säkerställa att aktiviteterna slutförs. Sedan visar appen den `stdout.txt`-fil som skapats av varje slutförd aktivitet. När aktiviteten körs skrivs utdata från aktivitetskommandot till `stdout.txt`:

```python
tasks = batch_service_client.task.list(job_id)

for task in tasks:

    node_id = batch_service_client.task.get(job_id, task.id).node_info.node_id
    print("Task: {}".format(task.id))
    print("Node: {}".format(node_id))

    stream = batch_service_client.file.get_from_task(
        job_id, task.id, config._STANDARD_OUT_FILE_NAME)

    file_text = _read_stream_as_string(
        stream,
        encoding)
    print("Standard output:")
    print(file_text)
```

## <a name="clean-up-resources"></a>Rensa resurser

Appen tar automatiskt bort den lagringscontainer den skapar och ger dig möjlighet att ta bort Batch-poolen och jobbet. Du debiteras för poolen medan noderna körs, även om inga jobb är schemalagda. Ta bort poolen när du inte längre behöver den. När du tar bort poolen raderas alla aktivitetsutdata på noderna. 

När de inte längre behövs tar du bort resursgruppen, Batch-kontot och lagringskontot. Om du vill göra det i Azure-portalen väljer du resursgruppen för Batch-kontot och klickar på **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten körde du en liten app som skapats med Batch Python-API för att skapa en Batch-pool och ett Batch-jobb. Jobbet körde exempelaktiviteter och hämtade utdata som skapats på noderna. Nu när du förstår nyckelbegreppen för Batch-tjänsten är du redo att testa Batch med mer realistiska arbetsbelastningar i större skala. Om du vill lära dig mer om Azure Batch och gå igenom parallell arbetsbelastning med ett verkligt program ska du fortsätta med självstudierna om Batch Python.

> [!div class="nextstepaction"]
> [Bearbeta en parallell arbetsbelastning med Python](tutorial-parallel-python.md)
