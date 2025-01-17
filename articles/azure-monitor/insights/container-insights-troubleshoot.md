---
title: Hur du felsöker Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs hur du kan felsöka och lösa problem med Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 2e3e39ef24d82393d981c0ce276b3338419e0b2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521759"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Felsökning Azure Monitor för behållare

När du konfigurerar övervakning av ditt Azure Kubernetes Service (AKS)-kluster med Azure Monitor för behållare kan du kan stöta på ett problem som förhindrar insamling av data eller rapporterar status. Den här artikeln beskriver några vanliga problem och felsökning.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Behörighetsfel under onboarding- eller update-åtgärd
När du aktiverar Azure Monitor för behållare eller uppdaterar ett kluster för att stödja samla in mått, kan du få ett felmeddelande liknar följande: *klienten < användarens identitet >' med objektet har inte id ”< användarens objectId >” behörighet att utföra åtgärden ”Microsoft.Authorization/roleAssignments/write' över område*

Under onboarding eller uppdatering, beviljar den **övervakning mått Publisher** rolltilldelning har påbörjats på klusterresursen. Användaren initierar processen att aktivera Azure Monitor för behållare eller uppdateringen för insamling av mått måste ha åtkomst till den **Microsoft.Authorization/roleAssignments/write** behörighet på AKS-kluster resurs-omfattningen. Endast medlemmar i den **ägare** och **administratör för användaråtkomst** inbyggda roller beviljas åtkomst till den här behörigheten. Om dina säkerhetsprinciper behöver tilldela detaljerade behörigheter på kolumnnivå, vi rekommenderar att du ser [anpassade roller](../../role-based-access-control/custom-roles.md) och tilldela den till användare som behöver den. 

Du kan också manuellt bevilja den här rollen från Azure portal genom att utföra följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver **Kubernetes**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Azure Kubernetes**.
3. I listan över Kubernetes-kluster, väljer du ett i listan.
2. I den vänstra menyn klickar du på **åtkomstkontroll (IAM)** .
3. Välj **+ Lägg till** att lägga till en rolltilldelning och välj den **övervakning mått Publisher** roll och under den **Välj** skriver **AKS** till Filtrera resultatet för just kluster tjänsthuvudnamn som definierats i prenumerationen. Välj det i listan som är specifik för klustret.
4. Välj **spara** Slutför tilldela rollen. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor för behållare har aktiverats men inte rapporterar någon information
Om Azure Monitor för behållare har aktiverats och konfigurerats, men du kan inte visa statusinformation eller inga resultat som returneras från en loggfråga, kan du diagnostisera problemet genom att följa dessa steg: 

1. Kontrollera statusen för agenten genom att köra kommandot: 

    `kubectl get ds omsagent --namespace=kube-system`

    Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Kontrollera distributionsstatusen med agentversion *06072018* eller senare med kommandot:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Utdata bör likna följande exempel, som anger att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Kontrollera status för poden att verifiera att den körs med hjälp av kommandot: `kubectl get pods --namespace=kube-system`

    Utdata bör likna följande exempel med statusen *kör* för omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Loggarna för agenten. När behållare agenten distribueras, kör en snabb kontroll genom att köra OMI kommandon och visar versionen av agenten och providern. 

5. Kontrollera att agenten har distribuerats korrekt genom att köra kommandot: `kubectl logs omsagent-484hw --namespace=kube-system`

    Statusen bör likna följande exempel:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Felmeddelanden

I tabellen nedan sammanfattas kända fel kan uppstå när du använder Azure Monitor för behållare.

| Felmeddelanden  | Åtgärd |  
| ---- | --- |  
| Felmeddelande `No data for selected filters`  | Det kan ta lite tid att upprätta övervakning dataflöde för nyligen skapade kluster. Tillåt minst 10 – 15 minuter innan data visas för klustret. |   
| Felmeddelande `Error retrieving data` | Medan Azure Kubenetes Service-kluster att konfigurera för hälso- och prestandaövervakning, upprättas en anslutning mellan kluster och Azure Log Analytics-arbetsyta. En Log Analytics-arbetsyta används för att lagra alla övervakningsdata för klustret. Det här felet kan uppstå när Log Analytics-arbetsytan har tagits bort eller tappas bort. Kontrollera om din arbetsyta är tillgänglig genom att granska [hantera åtkomst](../platform/manage-access.md#view-workspace-details). Om arbetsytan saknas, måste du återaktivera övervakning av ditt kluster med Azure Monitor för behållare. Om du vill återaktivera, behöver du [inaktivera](container-insights-optout.md) övervakning för klustret och [aktivera](container-insights-enable-new-cluster.md) Azure Monitor för behållare igen. |  
| `Error retrieving data` När du lägger till Azure Monitor för behållare via az aks cli | När aktiverar övervakning med hjälp av `az aks cli`, Azure Monitor för behållare kan inte distribueras korrekt. Kontrollera om lösningen har distribuerats. Gör detta genom att gå till Log Analytics-arbetsytan och se om lösningen är tillgänglig genom att välja **lösningar** från fönstret till vänster. För att lösa problemet måste du distribuera lösningen igen genom att följa anvisningarna [så här distribuerar du Azure Monitor för behållare](container-insights-onboard.md) |  

För att diagnosticera problemet, har vi lagt till ett felsökning skript som är tillgängliga [här](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Nästa steg
Med övervakning aktiverat för att avbilda hälsomått för både noder i AKS och poddar är dessa health-mått tillgängliga i Azure-portalen. Läs hur du använder Azure Monitor för behållare i [visa Azure Kubernetes Service health](container-insights-analyze.md).