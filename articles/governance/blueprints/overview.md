---
title: Översikt över Azure Blueprint
description: Förstå hur tjänsten Azure skisser kan du skapa, definiera och distribuera artefakter i Azure-miljön.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/08/2019
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 5f403ee7d2bf4a0730ceaa9b5a95b3e7b11f3695
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428778"
---
# <a name="overview-of-the-azure-blueprints-service"></a>Översikt över tjänsten Azure skisser

Precis som en skiss tillåter en tekniker eller arkitekt att skissa designparametrarna för ett projekt kan Azure Blueprint göra det möjligt för molnarkitekter och centrala IT-grupper att definiera en upprepningsbar uppsättning med Azure-resurser som implementerar och tillämpar en organisations standarder, mönster och krav. Med Azure Blueprint kan utvecklingsteam snabbt skapa nya miljöer med vetskapen om att de är skapade med organisatorisk efterlevnad och innehåller en uppsättning inbyggda komponenter – som nätverk – för att påskynda utveckling och leverans.

Skisser är en deklarativ metod för att dirigera distribution av flera resursmallar och andra artefakter som:

- Rolltilldelningar
- Principtilldelningar
- Azure Resource Manager-mallar
- Resursgrupper

Azure Blueprints-tjänsten backas av det globalt distribuerade [Azure Cosmos DB](../../cosmos-db/introduction.md).
Skissobjekt replikeras till flera Azure-regioner. Replikeringen ger låg svarstid, hög tillgänglighet och konsekvent åtkomst till dina skissobjekt, oavsett vilken region Blueprints distribuerar dina resurser till.

## <a name="how-its-different-from-resource-manager-templates"></a>Så här skiljer de sig från Resource Manager-mallar

Tjänsten är utformad för att hjälpa till med att _konfigurera miljön_. Den här konfigurationen består ofta av en uppsättning resursgrupper, principer, rolltilldelningar och Resource Manager-malldistributioner. En skiss är ett paket som ska sammanföra var och en av dessa _artefakttyper_ och gör att du kan skriva och skapa en version för paketet – inklusive via en CI/CD-pipeline. Slutligen tilldelas varje skiss till en prenumeration i en enda åtgärd som kan granskas och spåras.

Nästan allt du vill inkludera för distribution i skisser kan åstadkommas med en Resource Manager-mall. Men en Resource Manager-mall är ett dokument som inte finns inbyggt i Azure – var och en är lagrad lokalt eller i källkontrollen. Mallen används för distribution av en eller flera Azure-resurser, men när resurserna har distribuerats finns det ingen aktiv anslutning eller relation till mallen.

Med skisser finns relationen mellan skissdefinitionen (vad _ska_ distribuera) och skisstilldelningen (_vad har_ distribuerat) kvar. Den här anslutningen har stöd för förbättrad spårning och granskning av distributioner. Skisser kan också uppgradera flera prenumerationer på samma gång som regleras av samma skiss.

Du behöver inte välja mellan en Resource Manager-mall och en skiss. Varje skiss kan bestå av noll eller flera Resource Manager-mall-_artefakter_. Det innebär att tidigare arbete med att utveckla och underhålla ett bibliotek med Resource Manager-mallar kan användas igen i Skisser.

## <a name="how-its-different-from-azure-policy"></a>Hur det skiljer sig från Azure Policy

En skiss är ett paket eller en container för att skapa fokus-specifika uppsättningar standarder, mönster och krav för implementering av Azure-molntjänster, säkerhet och design som kan återanvändas för att säkerställa konsekvens och efterlevnad.

En [princip](../policy/overview.md) är en standard för att tillåta och explicit neka system som fokuserar på resursegenskaper under distributionen och för redan befintliga resurser. Det stöder molnstyrning genom att validera att resurser inom en prenumeration följer krav och standarder.

När en princip inkluderas i en skiss kan du skapa rätt mönster eller design under tilldelning av skissen. Principinkluderingen ser till att endast godkända eller förväntade ändringar kan göras i miljön för att skydda fortlöpande efterlevnad med skissens intention.

En princip kan ingå som en av flera _artefakter_ i en definition av skisser. Med skisser kan du även använda parametrar med principer och initiativ.

## <a name="blueprint-definition"></a>Definition av skisser

En skiss består av _artefakter_. Skisser stöder för närvarande följande resurser som artefakter:

|Resource  | Hierarkialternativ| Beskrivning  |
|---------|---------|---------|
|Resursgrupper | Prenumeration | Skapa en ny resursgrupp för användning av andra artefakter i skissen.  Med de här platshållarresursgrupperna kan du organisera resurser precis som du vill att de struktureras, och de ger en omfångsbegränsning för inkluderade princip- och rolltilldelningsartefakter samt Azure Resource Manager-mallar. |
|Azure Resource Manager-mall | Prenumeration, resursgrupp | Mallar används till att skapa komplexa miljöer. Exempelmiljöer: en SharePoint-servergrupp, Azure Automation State Configuration eller en Log Analytics-arbetsyta. |
|Principtilldelning | Prenumeration, resursgrupp | Tillåter tilldelning av en princip eller ett initiativ till den prenumeration som skissen är tilldelad till. Principen eller initiativet måste vara inom omfånget för skissdefinitionens plats. Om principen eller initiativet har parametrar kan de parametrarna tilldelas vid skapandet av skissen eller under skisstilldelningen. |
|Rolltilldelning | Prenumeration, resursgrupp | Lägg till en befintlig användare eller grupp till en inbyggd roll för att se till att rätt personer alltid har rätt åtkomst till dina resurser. Rolltilldelningar kan definieras för hela prenumerationen eller kapslade till en specifik resursgrupp som ingår i skissen. |

### <a name="blueprint-definition-locations"></a>Skissdefinitionsplatser

När du skapar en skissdefinition ska du definiera var skissen har sparats. Skisser kan sparas till en [hanteringsgrupp](../management-groups/overview.md) som eller en prenumeration som du har **deltagaråtkomst** till. Om platsen är en hanteringsgrupp kan skissen tilldelas till valfri underordnad prenumeration för den hanteringsgruppen.

### <a name="blueprint-parameters"></a>Skissparametrar

Skisser kan skicka parametrar till antingen en princip/ett initiativ eller en Azure Resource Manager-mall.
När en _artefakt_ läggs till i en skiss kan författaren besluta sig för att tillhandahålla ett angivet värde för varje skisstilldelning eller tillåta att varje skisstilldelning anger ett värde för tilldelningstiden. Tack vare den här flexibiliteten får du möjlighet att definiera ett förinställt värde för alla användare av skissen eller göra det möjligt för beslutet att göras vid tidpunkten för tilldelningen.

> [!NOTE]
> En skiss kan ha sina egna parametrar, men de kan för närvarande bara skapas om en skiss genereras från REST API:et i stället för via portalen.

Mer information finns [Skissparametrar](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publicering av skiss

När en skiss först skapas betraktas den som att den är i **utkastläge**. När den är redo att tilldelas måste den vara **publicerad**. Publicering kräver att du definierar en **versionssträng** (bokstäver, siffror och bindestreck med en maximal längd på 20 tecken) tillsammans med valfria **Ändra anteckningar**. **Versionen** särskiljer den från framtida ändringar i samma skiss och gör att varje version ska tilldelas. Den här versionshanteringen innebär också att olika **versioner** i samma skiss kan tilldelas till samma prenumeration. När ytterligare ändringar görs i skissen finns fortfarande den **publicerade** **versionen**, utöver **Opublicerade ändringar**. När ändringarna har slutförts är den uppdaterade skissen **publicerad** med en ny och unik **version** och kan nu också tilldelas.

## <a name="blueprint-assignment"></a>Skisstilldelning

Varje **publicerad** **version** av en skiss kan tilldelas till en befintlig prenumeration. I portalen får skissen som standard den **version** som **publicerades** senast. Om det finns artefaktparametrar (eller skissparametrar) definieras sedan parametrarna under tilldelningen.

## <a name="permissions-in-azure-blueprints"></a>Behörigheter i Azure Blueprint

Om du vill använda skisser måste du ha beviljade behörigheter via [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC). Om du vill skapa skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprints/write` – Skapa en skissdefinition
- `Microsoft.Blueprint/blueprints/artifacts/write` – Skapa artefakter på en skissdefinition
- `Microsoft.Blueprint/blueprints/versions/write` – Publicera en skiss

Om du vill ta bort skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Behörigheter för skissdefinition måste beviljas eller ärvas i det omfång för hanteringsgrupp eller prenumeration där den sparas.

Om du vill tilldela eller ta bort tilldelningar av skisser måste ditt konto ha följande behörigheter:

- `Microsoft.Blueprint/blueprintAssignments/write` – Tilldela en skiss
- `Microsoft.Blueprint/blueprintAssignments/delete` – Ta bort tilldelning av en skiss

> [!NOTE]
> Tilldelningar för skissen skapas på en prenumeration. Behörigheterna för tilldelning och för att ta bort tilldelning av behörigheter måste beviljas för eller ärvas till en prenumerationsomfattning.

Alla behörigheter ovan ingår i rollen **Ägare**. Rollen **Deltagare** har behörighet att skapa skisser och ta bort skisser men har inte behörighet att tilldela skisser. Om dessa inbyggda roller inte motsvarar dina behov kan du skapa en [anpassad roll](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Om med en automatiskt genererad hanterade identitet, tjänstens huvudnamn för Azure skisser kräver den **ägare** -rollen på den tilldelade prenumerationen för att aktivera distribution. Om du använder portalen är den här rollen automatiskt beviljad och återkallad för distributionen. Om du använder REST API måste den här rollen måste beviljas manuellt, men återkallas fortfarande automatiskt när distributionen är klar. Om du använder en Användartilldelad hanterad identitet, bara den användare som skapar skisstilldelningen måste **ägare** behörigheter.

## <a name="video-overview"></a>Videoöversikt

Följande översikt över Azure Blueprints kommer från Azure Fridays. En nedladdningsbar video finns på [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) (Azure Fridays – En översikt över Azure Blueprints) på Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Nästa steg

- [Skapa en skiss – Portal](create-blueprint-portal.md)
- [Skapa en skiss – REST API](create-blueprint-rest-api.md)
