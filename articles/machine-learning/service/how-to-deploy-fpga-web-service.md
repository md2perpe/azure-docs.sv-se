---
title: Distribuera modeller på FPGA
titleSuffix: Azure Machine Learning service
description: Lär dig hur du distribuerar en webbtjänst med en modell som körs på en FPGA med Azure Machine Learning-tjänsten för extremt låg latens inferens.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6cb9de60fe63c936da7340e6ec540a37163216f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074981"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Distribuera en modell som en webbtjänst på en FPGA med Azure Machine Learning-tjänsten

Du kan distribuera en modell som en webbtjänst på [fältet programmable gate matriser (FPGA)](concept-accelerate-with-fpgas.md) med Azure Machine Learning maskinvara Accelererat-modeller. FPGA ger extremt låg latens inferens, även med en enda gruppstorlek. Inferens eller modell bedömning är fasen där distribuerade modellen används för förutsägelse oftast på produktionsdata.

Dessa modeller är tillgängliga:
  - ResNet 50
  - ResNet 152
  - DenseNet 121
  - VGG-16
  - SSD-VGG

FPGA är tillgängliga i de här Azure-regioner:
  - Östra USA
  - Sydostasien
  - Västra Europa
  - Västra USA 2

> [!IMPORTANT]
> För att optimera svarstid och dataflöde, måste klienten skickar data till modellen FPGA vara i en av regionerna ovan (det som du har distribuerat modellen till).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration.  Om du inte har någon kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

- FPGA kvot.  Använda Azure CLI för att kontrollera om du har kvot.
    ```shell
    az vm list-usage --location "eastus" -o table
    ```

    De andra platserna är ``southeastasia``, ``westeurope``, och ``westus2``.

    Sök efter ”Standard PBS-serien virtuella processorer” under kolumnen ”Name” och se till att du har minst 6 vcpu: er under ”CurrentValue”.

    Om du inte har kvoten kan sedan skicka formuläret med en begäran [här](https://aka.ms/accelerateAI).

- En arbetsyta för Azure Machine Learning-tjänsten och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.
 
- Python SDK för maskinvaruacceleration modeller:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Exempel på notebook-filer

För din bekvämlighet [exempel anteckningsböcker](https://aka.ms/aml-accel-models-notebooks) är tillgängliga för exemplet nedan och andra exempel.

## <a name="create-and-containerize-your-model"></a>Skapa och paketera modeller

Det här dokumentet beskrivs hur du skapar ett TensorFlow-diagram till Förbearbeta inmatad bild, blir en upplärda med ResNet-50 på en FPGA och kör sedan funktionerna via en klassificerare som tränats på ImageNet-datauppsättning.

Följ anvisningarna för att:

* Definiera TensorFlow-modell
* Konvertera modellen
* Distribuera modellen
* Använd distribuerade modell
* Ta bort distribuerade tjänster

### <a name="load-azure-ml-workspace"></a>Läsa in Azure ML-arbetsyta

Läsa in din Azure ML-arbetsyta.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Förbearbeta bild

Indata till webbtjänsten är en JPEG-bild.  Det första steget är att avkoda JPEG-bild och Förbearbeta den.  JPEG-bilder behandlas som strängar och resultatet är tensors som indata till modellen ResNet-50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Läsa in upplärda

Initiera modellen och ladda ned en TensorFlow kontrollpunkt för den quantized versionen av ResNet50 som ska användas som en upplärda.  Du kan ersätta ”QuantizedResnet50” i kodfragmentet nedan med genom att importera andra djupa neurala nätverk:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Lägg till klassificerare

Den här klassificerare har tränats på ImageNet-datauppsättning.  Exempel för att överföra inlärning och utbildning för din anpassade vikterna är tillgängliga i uppsättningen [exempel anteckningsböcker](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Spara modellen

Nu när preprocessor och ResNet-50 upplärda klassificeraren har lästs in, spara graph och associerade variabler som modell.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Spara inkommande och utgående tensors
Inkommande och utgående tensors som skapades under Förbearbeta och klassificerare steg krävs för modellen konvertering och inferens.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Spara indata och utdata tensors eftersom du behöver dem för konvertering och inferens modellbegäranden.

De tillgängliga modellerna och motsvarande standard klassificeraren utdata tensors är nedan, vilket är vad du använder för inferens om du använder standard-klassificerare.

+ Resnet50 QuantizedResnet50
  ```
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152 QuantizedResnet152
  ```
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121 QuantizedDensenet121
  ```
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16 QuantizedVgg16
  ```
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg QuantizedSsdVgg
  ```
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrera modellen

[Registrera](./concept-model-management-and-deployment.md) modellen som du skapade.  Att lägga till taggar och andra metadata om modellen hjälper dig att hålla koll på dina tränade modeller.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws,
                                  model_path = model_save_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Om du redan har registrerat en modell och vill läsa in den, kan du hämta den.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Konvertera modell

Konvertera diagrammet TensorFlow till öppna Neural Network Exchange-formatet ([ONNX](https://onnx.ai/)).  Du måste ange namnen på inkommande och utgående tensors och dessa namn som ska användas av klienten när du förbrukar webbtjänsten.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output = False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version, 
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Skapa Docker-avbildning

Den konvertera modellen och alla beroenden läggs till i en Docker-avbildning.  Den här Docker-avbildningen kan sedan distribueras och instansierats.  Mål för distribution som stöds inkluderar AKS i molnet eller en edge-enhet, till exempel [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Du kan också lägga till taggar och beskrivningar för registrerade Docker-avbildningen.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)
image.wait_for_creation(show_output = False)
```

Lista över avbildningarna efter tagg och få detaljerade loggar för felsökning.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Modelldistribution

### <a name="deploy-to-the-cloud"></a>Distribuera till molnet

Använd Azure Kubernetes Service (AKS) för att distribuera din modell som en webbtjänst i produktionsmiljön för hög skalbarhet. Du kan skapa en ny med SDK: N för Azure Machine Learning, CLI eller Azure-portalen.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)
```

AKS-distributionen kan ta ungefär 15 minuter.  Kontrollera om distributionen har slutförts.

```python
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Distribuera behållaren till AKS-klustret.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled = False)

aks_service_name ='my-aks-service'

aks_service = Webservice.deploy_from_image(workspace = ws,
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
```

#### <a name="test-the-cloud-service"></a>Testa Molntjänsten
Docker-avbildningen stöder gRPC och TensorFlow som betjänar ”förutsäga” API.  Du kan använda exemplet klienten för att anropa Docker-avbildning ska få förutsägelser från modellen.  Exempelkod för klienten är tillgänglig:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Om du vill använda TensorFlow betjänar kan du [ladda ned en exempel-klient](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Eftersom den här klassificerare som har tränats på den [ImageNet](http://www.image-net.org/) data ange, mappa klasser till läsbart etiketter.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensors, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Rensa tjänsten
Ta bort din webbtjänst, bild och modellen (måste utföras i angiven ordning eftersom det finns beroenden).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Distribuera till en lokal edge-server

Alla [gränsenheter för Azure Data Box](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) innehåller en FPGA för att köra modellen.  Endast en modell kan köras på FPGA i taget.  Om du vill köra en annan modell du bara distribuera en ny behållare. Anvisningar och exempelkod finns i [exemplet Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Skydda FPGA-webbtjänster

Information om hur du skyddar FPGA webbtjänster finns i den [skydda webbtjänster](how-to-secure-web-service.md) dokumentet.

## <a name="pbs-family-vms"></a>PBS-serien virtuella datorer

PBS-serien av virtuella Azure-datorer innehåller Intel Arria 10 FPGA.  Den visas som ”Standard PBS-serien virtuella processorer” när du kontrollera att din Azure-kvot allokering.  PB6 VM har sex virtuella processorer och en FPGA och automatiskt ska etableras genom Azure ML som en del av att distribuera en modell till en FPGA.  Den används endast med Azure ML och det går inte att köra godtycklig bitstreams.  Till exempel att du inte kommer att kunna flash FPGA med bitstreams gör kryptering, kodning, etc. 
