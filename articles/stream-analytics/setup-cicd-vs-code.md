---
title: Kontinuerlig integrering och utveckla med Azure Stream Analytics CI/CD npm-paket
description: Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD npm-paket för att konfigurera en kontinuerlig integrering och distributionsprocessen.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158508"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>Kontinuerlig integrering och utveckla med Stream Analytics CI/CD npm-paket
Den här artikeln beskriver hur du använder Azure Stream Analytics CI/CD npm-paket för att konfigurera en kontinuerlig integrering och distributionsprocessen.

## <a name="build-the-vs-code-project"></a>Skapa VS Code-projektet

Du kan aktivera kontinuerlig integrering och distribution för Azure Stream Analytics-jobb med hjälp av den **asa-streamanalytics-cicd** npm-paket. Npm-paketet innehåller verktyg för att skapa Azure Resource Manager-mallar för [Stream Analytics Visual Studio Code projekt](quick-create-vs-code.md). Det kan användas i Windows, macOS och Linux utan att installera Visual Studio Code.

När du har [hämtade paketet](https://www.npmjs.com/package/azure-streamanalytics-cicd), använder du följande kommando för att mata ut Azure Resource Manager-mallar. Den **scriptPath** argumentet är den absoluta sökvägen till den **asaql** filen i projektet. Kontrollera att det asaproj.json och JobConfig.json filer finns i samma mapp med skriptfilen. Om den **outputPath** inte anges mallarna kommer att placeras i den **distribuera** mapp under projektets **bin** mapp.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exempel (på macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

När ett Stream Analytics Visual Studio Code-projekt skapas har måste genererar följande två Azure Resource Manager mallfiler under den **bin / [Debug/hos återförsäljare] / distribuera** mapp: 

*  Resource Manager-mallfilen

       [ProjectName].JobTemplate.json 

*  Resource Manager-parameterfilen

       [ProjectName].JobTemplate.parameters.json   

Standardparametrarna i filen parameters.json är från inställningarna i ditt projekt i Visual Studio Code. Ersätt parametrarna i enlighet med detta om du vill distribuera till en annan miljö.

> [!NOTE]
> För alla autentiseringsuppgifterna som standardvärden anges till null. Du är **krävs** du anger värden innan du distribuerar till molnet.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Mer information om hur du [distribuera med en Resource Manager-mallfilen och Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Mer information om hur du [använda ett objekt som en parameter i en Resource Manager-mall](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Om du vill använda hanterade identiteter för Azure Data Lake Store Gen1 som utdatamottagare, måste du ge åtkomst till tjänstens huvudnamn med hjälp av PowerShell innan du distribuerar till Azure. Mer information om hur du [distribuera ADLS Gen1 med hanterad identitet med Resource Manager-mall](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa cloud Azure Stream Analytics-jobb i Visual Studio Code (förhandsversion)](quick-create-vs-code.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio Code (förhandsversion)](vscode-local-run.md)
* [Utforska Azure Stream Analytics med Visual Studio Code (förhandsversion)](vscode-explore-jobs.md)
