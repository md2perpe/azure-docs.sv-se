---
title: Erbjudande om Azure-program-hanterat program publicera Guide
description: Den här artikeln beskrivs kraven för att publicera ett hanterat program på Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: 29546b0969751a43959a55860fc22e9f3c3e225b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154937"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-program: Erbjudande om hanterat program publicera Guide

Ett hanterat program är en av huvudmetoderna för att publicera en lösning i Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Det här är transaktionen erbjudanden som distribueras och faktureras via Marketplace. Anropet till åtgärden som en användare ser är ”Hämta nu”.

Använda Azure-appen: hanterade erbjudande apptyp när följande villkor krävs:
- Du distribuerar antingen en prenumeration-baserad lösning för din kund som använder en virtuell dator eller en hel IaaS-baserad lösning.
- Du eller dina kunder kräver att lösningen hanteras av en partner.

>[!NOTE]
>En partner kan exempelvis vara en SI eller leverantör av hanterade tjänster (MSP).  

## <a name="managed-application-offer"></a>Hanterade program erbjudande

|Krav |Information  |
|---------|---------|
|Distribueras till en kunds Azure-prenumeration | Hanterade appar måste distribueras i kundens prenumeration och kan hanteras av en tredje part. | 
|Fakturering och mätning    |  Resurserna som ska etableras i kundens Azure-prenumeration. Betala per användning (betalning per användning) virtuella datorer ska ingå i en transaktion med kunden via Microsoft, faktureras via kundens Azure-prenumeration (betalning per användning). <br> När det gäller bring-your-own-license, medan Microsoft att debitera infrastrukturkostnader som tillkommer i kundens prenumeration, kommer du transact dina programvarulicenser avgifter för kunden direkt.        |
|Azure-kompatibel virtuell hårddisk (VHD)    |   Virtuella datorer måste vara baserade på Windows eller Linux.<ul> <ul> <li>Mer information om hur du skapar en Linux-VHD finns [Linux-distributioner på Azure-godkända](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Läs mer om hur du skapar en virtuell Windows-Hårddisk [skapa en Azure-kompatibel VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Hanterade appar måste vara distribuerbar via Marketplace. Om kunden kommunikation är ett problem kan bör sedan du kontakta intresserade kunder när du har aktiverat dela leads.  

>[!Note]
>Cloud Solution Providers (CSP) partner channel anmälan är nu tillgänglig.  Se [Cloud Solution Providers](./cloud-solution-providers.md) mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner kanaler.

## <a name="next-steps"></a>Nästa steg
Om du inte redan gjort det, 

- [Registrera](https://azuremarketplace.microsoft.com/sell) i marketplace.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på en befintlig

- [Logga in på Cloud Partner Portal](https://cloudpartner.azure.com) att skapa eller slutföra ditt erbjudande.
