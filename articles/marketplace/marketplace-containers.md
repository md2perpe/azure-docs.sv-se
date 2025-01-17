---
title: Behållare erbjuder publicera Guide för Azure Marketplace
description: Den här artikeln beskrivs kraven för att publicera behållare i Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 41a09be36262ff09c383b8ccb64a94230a11d3f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937920"
---
# <a name="containers-offer-publishing-guide"></a>Behållare erbjuder publicera Guide

Behållare erbjuder dig publicera behållaravbildningen till Azure Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionen erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är ”Hämta nu”.

Använd typ av erbjudande behållaren när din lösning är en Docker-behållaravbildning som tillhandahålls som en Kubernetes-baserade Azure-behållartjänst.

>[!NOTE]
>Till exempel en Kubernetes-baserade Azure-behållartjänst som Azure Kubernetes Service eller Azure Container Instances, valet av Azure-kunder för ett Kubernetes-baserad behållare för körning.  

Microsoft stöder för närvarande kostnadsfria och bring-your-own-license (BYOL) licensieringsmodellerna.

## <a name="containers-offer"></a>Behållare-erbjudande

| Krav | Information |  
|:--- |:--- |  
| Fakturering och mätning | Stöd för antingen den kostnadsfria eller BYOL faktureringsmodell. |  
| Avbildning som byggts från Dockerfile | Behållaravbildningar måste baseras på Docker-avbildning-specifikationen och måste skapas från en Dockerfile.<ul> <li>Mer information om hur du skapar docker-avbildningar finns i avsnittet användning finns på [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Värd för i ACR | Behållaravbildningar måste finnas i en lagringsplats i Azure Container Registry (ACR).<ul> <li>Mer information om hur du arbetar med ACR finns i snabbstarten: Skapa ett behållarregister med hjälp av Azure portal sidan finns på [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Bild-märkning | Behållaravbildningar måste innehålla minst 1 taggen (högsta taggar: 16).<ul> <li>Mer information om en bild-märkning finns på sidan docker-tagg finns på [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort det, 

- [Registrera](https://azuremarketplace.microsoft.com/sell) i marketplace.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig

- [Logga in på partnerportalen i molnet](https://cloudpartner.azure.com) att skapa eller slutföra ditt erbjudande.
- Se [behållare](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) för mer information.
