---
title: Azure Monitor VM-tillägg för Linux | Microsoft Docs
description: Distribuera Log Analytics-agenten på Linux-dator med hjälp av tillägg för virtuell dator.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: roiyz
ms.openlocfilehash: 2c16e4c105a69d0d48d0b161db2caa390837bf26
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514548"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Azure Monitor VM-tillägg för Linux

## <a name="overview"></a>Översikt

Azure Monitor-loggar ger funktioner för övervakning, varningar och aviseringar reparation i molnet och lokala tillgångar. Tillägget för virtuell dator Log Analytics-agenten för Linux är publicerat och stöds av Microsoft. Tillägget Log Analytics-agenten installeras på virtuella Azure-datorer och registreras virtuella datorer i en befintlig Log Analytics-arbetsyta. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativen för Azure Monitor-tillägget för virtuell dator för Linux.

>[!NOTE]
>Som en del av pågående övergången från Microsoft Operations Management Suite (OMS) till Azure Monitor betecknas OMS-agenten för Windows eller Linux som Log Analytics-agenten för Windows och Log Analytics-agenten för Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Tillägget Log Analytics-agenten kan köras mot dessa Linux-distributioner.

| Distribution | Version |
|---|---|
| CentOS Linux | 6 (x86/x64) och 7 (x 64) |
| Amazon Linux | 2017.09 (x64) | 
| Oracle Linux | 6 och 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 6 (x86/x64) och 7 (x 64) |
| Debian GNU/Linux | 8 och 9 (x86/x64) |
| Ubuntu | 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) och 18.04 LTS (x64) |
| SUSE Linux Enterprise Server | 12 (x 64) och 15 (x 64) |

>[!NOTE]
>OpenSSL som är lägre än version 1.x stöds inte på valfri plattform och version 1.10 stöds bara på x86_64 plattformar (64-bitars).  
>

### <a name="agent-prerequisites"></a>Krav för klientagenten

Följande tabell visar de paket som krävs för distributioner som stöds Linux som agenten ska installeras på.

|Nödvändigt paket |Beskrivning |Lägsta version |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotek | 2.5-12 
|Openssl    | OpenSSL-bibliotek | 1.0.x eller 1.1.x |
|Curl | cURL webbklienten | 7.15.5 |
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Standard syslog-daemon på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för syslog-händelseinsamling. Om du vill samla in syslog-data från den här versionen av dessa distributioner bör rsyslog-daemon installeras och konfigureras för att ersätta sysklog.

### <a name="agent-and-vm-extension-version"></a>Version av agenten och tillägg för virtuell dator
Följande tabell innehåller en mappning av versionen av Azure Monitor VM-tillägg och Log Analytics-agenten paket för varje version. En länk till viktig information om Paketversion för Log Analytics-agenten ingår. Viktig information innehåller information om felkorrigeringar och nya funktioner som är tillgängliga för en viss agent-version.  

| Azure Monitor Linux VM-tilläggsversion | Paketversion för log Analytics-agenten | 
|--------------------------------|--------------------------|
| 1.11.9 | [1.11.0-7](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-7) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2 125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatiskt etablerar Log Analytics-agenten och ansluter den till en standard Log Analytics-arbetsyta som skapats av ASC i din Azure-prenumeration. Om du använder Azure Security Center kan inte köra stegen i det här dokumentet. Gör detta skriver över den konfigurerade arbetsytan och skadar anslutningen med Azure Security Center.

### <a name="internet-connectivity"></a>Internetanslutning

Tillägget Log Analytics-agenten för Linux kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget Log Analytics-agenten. Tillägget kräver arbetsytans ID och arbetsytenyckel från målet Log Analytics-arbetsytan; Dessa värden kan vara [hittades i Log Analytics-arbetsytan](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) i Azure-portalen. Eftersom arbetsytenyckeln ska behandlas som känsliga data, ska den lagras i en skyddad Konfigurationsinställningen. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är skiftlägeskänsliga.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Enligt schemat ovan förutsätter att den ska placeras på rotnivå för mallen. Om du placerar den i den virtuella datorresursen i mallen den `type` och `name` egenskaper ska ändras enligt [längre ner](#template-deployment).
>

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (t.ex.) | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution, till exempel Kom igång med Azure Monitor-loggar. En Resource Manager-mall som innehåller Log Analytics-agenten VM-tillägget kan hittas på den [Azure Quick Start-galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

JSON-konfiguration för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

I följande exempel förutsätter att VM-tillägget är kapslade i den virtuella datorresursen. När kapsla tillägget resursen JSON placeras i den `"resources": []` objekt av den virtuella datorn.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

När du monterar tillägget JSON i roten på mallen resursnamnet innehåller en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Log Analytics-agenten VM-tillägget till en befintlig virtuell dator. Ersätt den *workspaceId* och *workspaceKey* med de från Log Analytics-arbetsytan. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras innebörd

| Felkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 9 | Aktivera som kallas för tidigt | [Uppdatera Azure Linux Agent](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) till den senaste tillgängliga versionen. |
| 10 | Virtuell dator är redan ansluten till en Log Analytics-arbetsyta | Om du vill ansluta den virtuella datorn till arbetsytan som angetts i schemat för tillägget stopOnMultipleConnections inställd på false i offentliga inställningar eller ta bort den här egenskapen. Den här virtuella datorn debiteras när för varje arbetsyta som den är ansluten till. |
| 11 | Ogiltig konfiguration som angetts för tillägget | Följ föregående exempel för att ange alla egenskapsvärden som krävs för distributionen. |
| 17 | Logga Analytics paketet installationsfel | 
| 19 | OMI paketet installationsfel | 
| 20 | Installationsfel för SCX-paket |
| 51 | Det här tillägget stöds inte på den Virtuella datorns operativsystem | |
| 55 | Det går inte att ansluta till Azure Monitor-tjänsten eller paket som krävs saknas eller dpkg Pakethanteraren är låst| Kontrollera att datorn är ansluten till Internet eller att en giltig HTTP-proxy har angetts. Dessutom kan kontrollera för arbetsyte-ID och kontrollera curl och tar är installerade. |

Ytterligare information kan hittas på den [felsökningsguide för Log Analytics-agenten för Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
