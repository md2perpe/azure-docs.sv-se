---
title: Schemareferens i Azure Container Registry webhook
description: Webhook-begäran JSON-nyttolast referens för Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61331349"
---
# <a name="azure-container-registry-webhook-reference"></a>Referens för Azure Container Registry-webhook

Du kan [konfigurerar webhooks](container-registry-webhook.md) för ditt behållarregister som genererar händelser när vissa åtgärder som utförs mot den. Till exempel aktivera webhooks som utlöses när en behållaravbildning eller Helm-diagrammet är skickas till ett register eller tas bort. När en webhook har utlösts skickar en HTTP eller HTTPS-begäran som innehåller information om händelsen till en slutpunkt som du anger i Azure Container Registry. Slutpunkten kan sedan bearbeta webhooken och agera utifrån dessa.

Följande avsnitt beskriver schemat för webhook-begäranden som genereras av händelser som stöds. Händelse-avsnitt innehåller nyttolast-schemat för händelsetypen, en exempel-nyttolasten för begäran och en eller flera exempel på kommandon som ska utlösa webhooken.

Information om hur du konfigurerar webhooks för Azure container registry finns i [med hjälp av Azure Container Registry-webhookar](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook-begäranden

### <a name="http-request"></a>HTTP-begäran

En utlösta webhook gör ett HTTP `POST` begäran till URL-slutpunkt som du angav när du konfigurerade webhooken.

### <a name="http-headers"></a>HTTP-huvuden

Webhook-begäran innehålla en `Content-Type` av `application/json` om du inte har angett en `Content-Type` anpassat sidhuvud för din webhook.

Inga andra rubriker läggs till i begäran utöver dessa anpassade sidhuvuden som du har angett för webhook.

## <a name="push-event"></a>Push-händelse

Webhook-utlöst när en behållaravbildning skickas till en databas.

### <a name="push-event-payload"></a>Push-händelsenyttolast

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelse.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|String|Den åtgärd som utlöste händelsen webhook.|
|[target](#target)|Komplex typ|Mål för den händelse som utlöste händelsen webhook.|
|[request](#request)|Komplex typ|Den begäran som genereras av webhook-händelse.|

### <a name="target"></a>Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typ för det refererade objektet.|
|`size`|Int32|Antal byte av innehållet. Samma som fältet längd.|
|`digest`|String|Samling av innehållet, enligt definitionen i registret V2 HTTP API-specifikationen.|
|`length`|Int32|Antal byte av innehållet. Samma som fältet.|
|`repository`|String|Namnet på lagringsplatsen.|
|`tag`|String|Taggnamnet bild.|

### <a name="request"></a>request

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|String|ID för begäran som initierade händelsen.|
|`host`|String|Externt tillgängliga värdnamnet för registry-instans som den anges av HTTP-rubriken på inkommande begäranden.|
|`method`|String|Metoden för begäran som genererade händelsen.|
|`useragent`|String|Användaren agent huvudet för begäran.|

### <a name="payload-example-image-push-event"></a>Nyttolast-exempel: bild push-händelse

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exempel [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) kommando som utlöser avbildningen **push** händelse-webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Diagrammet push-händelse

Webhook-utlöst när ett Helm-diagram skickas till en databas.

### <a name="chart-push-event-payload"></a>Diagrammet push händelsenyttolast

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelse.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|String|Den åtgärd som utlöste händelsen webhook.|
|[target](#helm_target)|Komplex typ|Mål för den händelse som utlöste händelsen webhook.|

### <a name="helm_target"></a>Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typ för det refererade objektet.|
|`size`|Int32|Antal byte av innehållet.|
|`digest`|String|Samling av innehållet, enligt definitionen i registret V2 HTTP API-specifikationen.|
|`repository`|String|Namnet på lagringsplatsen.|
|`tag`|String|Taggnamnet diagram.|
|`name`|String|Diagrammets namn.|
|`version`|String|Diagram-version.|

### <a name="payload-example-chart-push-event"></a>Nyttolast-exempel: diagram push-händelse

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exempel [Azure CLI](/cli/azure/acr) kommando som utlöser den **chart_push** händelse-webhook:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Ta bort händelse

Webhook utlöses när en avbildningslagringsplatsen eller manifest tas bort. Utlöses inte när en tagg har tagits bort.

### <a name="delete-event-payload"></a>Ta bort händelsenyttolast

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelse.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|String|Den åtgärd som utlöste händelsen webhook.|
|[target](#delete_target)|Komplex typ|Mål för den händelse som utlöste händelsen webhook.|
|[request](#delete_request)|Komplex typ|Den begäran som genereras av webhook-händelse.|

### <a name="delete_target"></a> Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typ för det refererade objektet.|
|`digest`|String|Samling av innehållet, enligt definitionen i registret V2 HTTP API-specifikationen.|
|`repository`|String|Namnet på lagringsplatsen.|

### <a name="delete_request"></a> Begäran

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|String|ID för begäran som initierade händelsen.|
|`host`|String|Externt tillgängliga värdnamnet för registry-instans som den anges av HTTP-rubriken på inkommande begäranden.|
|`method`|String|Metoden för begäran som genererade händelsen.|
|`useragent`|String|Användaren agent huvudet för begäran.|

### <a name="payload-example-image-delete-event"></a>Nyttolast-exempel: bild ta bort händelse

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exempel [Azure CLI](/cli/azure/acr) kommandon som utlösaren en **ta bort** händelse-webhook:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Diagrammet ta bort händelse

Webhook-utlöst när en Helm-diagrammet eller tas bort. 

### <a name="chart-delete-event-payload"></a>Händelsenyttolast för diagrammet delete

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|String|ID för webhook-händelse.|
|`timestamp`|DateTime|Den tid då händelsen webhook utlöstes.|
|`action`|String|Den åtgärd som utlöste händelsen webhook.|
|[target](#chart_delete_target)|Komplex typ|Mål för den händelse som utlöste händelsen webhook.|

### <a name="chart_delete_target"></a> Mål

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|String|MIME-typ för det refererade objektet.|
|`size`|Int32|Antal byte av innehållet.|
|`digest`|String|Samling av innehållet, enligt definitionen i registret V2 HTTP API-specifikationen.|
|`repository`|String|Namnet på lagringsplatsen.|
|`tag`|String|Taggnamnet diagram.|
|`name`|String|Diagrammets namn.|
|`version`|String|Diagram-version.|

### <a name="payload-example-chart-delete-event"></a>Nyttolast-exempel: diagram ta bort händelse

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exempel [Azure CLI](/cli/azure/acr) kommando som utlöser den **chart_delete** händelse-webhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Nästa steg

[Med Azure Container Registry-webhookar](container-registry-webhook.md)