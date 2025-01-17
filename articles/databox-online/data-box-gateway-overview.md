---
title: Översikt över Microsoft Azure Data Box Gateway | Microsoft Docs
description: Beskriver Azure Data Box Gateway, en lagringslösning med virtuell installation som gör det möjligt att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 04/08/2019
ms.author: alkohli
ms.openlocfilehash: 2905cae6d5d97062d5a2fc1da41d55290d89fb4a
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924502"
---
# <a name="what-is-azure-data-box-gateway"></a>Vad är Azure Data Box-Gateway?

Azure Data Box Gateway är en lagringslösning som gör att du smidigt kan skicka data till Azure. Den här artikeln ger dig en översikt över Azure Data Box Gateway-lösningen, dess fördelar, viktiga funktioner och scenarier där du kan distribuera den här enheten.

Data Box Gateway är en virtuell enhet baserad på en virtuell dator som etablerats i din virtualiserade miljö eller i hypervisor-programmet. Den virtuella enheten finns lokalt hos dig och du skriver data till den med hjälp av NFS- och SMB-protokoll. Enheten överför sedan dina data till Azure blockblob, sidblob eller Azure Files.

## <a name="use-cases"></a>Användningsfall

Data Box Gateway kan utnyttjas för att överföra data till molnet, som molnarkivering, haveriberedskap, eller om det är nödvändigt att bearbeta dina data i molnskala. Här följer olika scenarier där Data Box Gateway kan användas för dataöverföring.

- **Molnarkivering** – Kopiera hundratals TB data till Azure Storage med hjälp av Data Box Gateway på ett säkert och effektivt sätt. Data kan matas in en gång eller kontinuerligt för arkiveringsscenarier.

- **Kontinuerlig datainmatning** -kontinuerligt mata in data i enheten för att kopiera till molnet, oavsett storleken på data. När data skrivs till gateway-enheten, överför data till Azure Storage med hjälp av enheten.  

- **Inledande bulk överföring följt av inkrementella överföringen** -Använd Data Box för stora dataöverföringar i en offline-läge (inledande seed) och Data Box-Gateway för inkrementella överföringar (pågående matning) över nätverket.

Mer information går du till [användningsfall för Azure Data Box Gateway](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Fördelar

Data Box Gateway har följande fördelar:

- **Enkel dataöverföring** – Gör det enkelt att flytta data till och från Azure Storage, lika enkelt som att arbeta med en lokal nätverksresurs.  
- **Höga prestanda** – Tar bort problemen med nätverksdatatransport med överföringar med höga prestanda till och från Azure.
- **Snabb åtkomst och höga datainmatningsfrekvenser under kontorstid** – Data Box Gateway har en lokal cache som du definierar som lokal kapacitet när den virtuella enheten etableras. Datadiskstorleken ska anges enligt [minimikraven för virtuella enheter](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). Den lokala cachen ger följande fördelar:
    - Den lokala cachen möjliggör datainmatning med hög hastighet. När stora mängder data matas in vid tider med hög belastning kan cachen förvara data och överför dem till molnet.
    - Den lokala cachen möjliggör snabb läsåtkomst upp till ett visst tröskelvärde. Tills enheten är 50–60 % full används alla läsningar från enheten från cachen, vilket gör att de blir snabbare. När det använda utrymmet på enheten går över det här tröskelvärdet, startar enheten att ta bort lokala filer.
 
- **Begränsad bandbreddsanvändning** – data kan skrivas till Azure även om nätverket begränsar användningen vid tider med hög belastning.  

## <a name="key-capabilities"></a>De viktigaste funktionerna

Data Box Gateway har följande funktioner:

|Funktion |Beskrivning  |
|---------|---------|
|Hastighet     | Helt automatiserad och optimerad dataöverföring och bandbredd.|
|Protokoll som stöds     | Stöd för SMB- och NFS-standardprotokoll för datainmatning. <br> Mer information om versioner som stöds finns i [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).|
|Dataåtkomst     | När de data som skickas av enheten finns i molnet, kan det ytterligare ändras genom att gå direkt till molnet API: er.|
|Snabb åtkomst     | Lokal cache på enheten för snabb åtkomst till de senast använda filerna.|
|Offlineuppladdning     | Frånkopplat läge stöder scenarier med offlineuppladdning.|
|Datauppdatering     | Möjlighet att uppdatera lokala filer med det senaste från molnet.|
|Kryptering    | BitLocker-stöd för lokal kryptering av data och skydda dataöverföring till molnet via *https*       |
|Återhämtning     | Inbyggd nätverksåterhämtning        |


## <a name="specifications"></a>Specifikationer

Den virtuella Data Box-Gateway-enheten har följande specifikationer:

| Specifikationer                                          | Beskrivning              |
|---------------------------------------------------------|--------------------------|
| Virtuella processorer (kärnor)   | Minst 4 |
| Minne  |Minst 8 GB|
| Tillgänglighet|Enkel nod|
| Diskar|OS-disk: 250 GB <br> Datadisk: minst 2 TB, tunt allokerad och måste backas upp av SSD-enheter|
| Nätverksgränssnitt |Minst 1 virtuellt nätverksgränssnitt|
| Interna fildelningsprotokoll|SMB och NFS  |
| Säkerhet|Autentisering för att låsa upp åtkomst till enhet och data <br> Data-in-flight-krypterad med AES-256-bitarskryptering|
| Hantering|Lokalt webbgränssnitt – inledande konfiguration, diagnostik och energisparfunktioner för enheten <br> Azure-portalen – daglig hantering av Data Box Gateway-enheter       |

## <a name="components"></a>Komponenter

Data Box Gateway-lösningen består av Data Box Gateway-resurs, virtuell Data Box Gateway-enhet och ett lokalt webbgränssnitt.

- **Virtuell Data Box Gateway-enhet** – En enhet baserad på en virtuell dator som har etablerats i din virtualiserade miljö eller i hypervisor-programmet och gör att du kan skicka data till Azure.
    
- **Data Box Gateway-resurs** – En resurs i Azure-portalen där du kan hantera en Data Box Gateway-enhet via ett webbgränssnitt som du kommer åt på olika geografiska platser. Använd Data Box Gateway-resursen för att visa och hantera enheter, resurser, användare och aviseringar. Läs mer om hur du [hantera med Azure portal](data-box-gateway-manage-shares.md).

- **Data Box lokala webbgränssnittet** – Använd det lokala webbgränssnittet för att köra diagnostik, stänga och starta om enheten, skapa ett supportpaket eller kontakta Microsoft Support om du vill skicka en serviceförfrågan. Läs mer om hur du [hantera med hjälp av lokala webbgränssnittet](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Regional tillgänglighet

Fysisk enhet för data Box-Gateway, Azure-resurs och mål-lagringskontot som du överför data behöver inte alla finnas i samma region.

- **Resurstillgänglighet** -den här versionen är Data Box Gateway-resursen är tillgänglig i följande regioner som har stöd för offentliga moln:
    - **Förenta staternas** – USA, östra
    - **EU** – Europa, västra
    - **Asien och Stillahavsområdet** -Sydostasien

    Data Box-Gateway kan också distribueras i Azure Government-molnet. Mer information finns i [vad är Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Destinationslagringskonton** – de lagringskonton som lagrar data som är tillgängliga i alla Azure-regioner.

    För optimala prestanda bör de regioner där lagringskonton lagrar Data Box-data ligga nära den plats där enheten finns. Ett lagringskonto som är långt från enheten leder till långa svarstider och långsammare prestanda.


## <a name="next-steps"></a>Nästa steg

- Se [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).
- Förstå [begränsningarna för Data Box Gateway](data-box-gateway-limits.md).
- Distribuera [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i Azure-portalen.

