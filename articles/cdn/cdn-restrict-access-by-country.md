---
title: Begränsa Azure CDN-innehåll efter land/region | Microsoft Docs
description: Lär dig mer om att begränsa åtkomst efter land/region till ditt Azure CDN-innehåll med hjälp av funktionen geo-filtrering.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 083d8f66a73471548c812e27325e1ec69ad5c45c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869587"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Begränsa Azure CDN-innehåll efter land/region

## <a name="overview"></a>Översikt
När en användare begär innehåll, som standard, hanteras innehållet oavsett var de användare som skickar begäran. Men i vissa fall kan du begränsa åtkomsten till innehållet efter land/region. Med den *geofiltreringen* funktion, kan du skapa regler för vissa sökvägar på CDN-slutpunkten att tillåta eller blockera innehåll i valda länder/regioner.

> [!IMPORTANT]
> **Azure CDN Standard från Microsoft** profiler stöder inte sökvägsbaserad geo-filtrering.
> 

## <a name="standard-profiles"></a>Standard-profiler
Procedurerna i det här avsnittet avser **Azure CDN Standard från Akamai** och **Azure CDN Standard från Verizon** profiler endast. 

För **Azure CDN Premium från Verizon** profiler, måste du använda den **hantera** portalen för att aktivera geo-filtrering. Mer information finns i [Azure CDN Premium från Verizon profiler](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definiera katalogsökvägen
För att komma åt funktionen geo-filtrering, Välj din CDN-slutpunkt i portalen och välj sedan **geofiltreringen** under inställningar i den vänstra menyn. 

![Standard GEO-filtrering](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Från den **sökväg** anger den relativa sökvägen till den plats som användarna ska tillåtas eller nekas åtkomst. 

Du kan använda geo-filtrering för alla filer med en vanlig snedstreck (/) eller Välj specifika mappar genom att ange katalogsökvägar (till exempel */bilder/* ). Du kan också använda geo-filtrering för en enda fil (till exempel */pictures/city.png*). Flera regler tillåts; När du har angett en regel, visas en tom rad där du kan ange nästa regel.

Till exempel är alla följande directory sökväg filter giltiga:   
*/*                                 
*/Photos/*      
*/Photos/Strasbourg /*      
*/Photos/Strasbourg/City.PNG*

### <a name="define-the-type-of-action"></a>Definiera typ av åtgärd

Från den **åtgärd** väljer **Tillåt** eller **blockera**: 

- **Tillåt**: Endast användare från de angivna länder/regionerna får åtkomst till resurser som begärs från den rekursiva sökvägen.

- **Blockera**: Användare från de angivna länder/regionerna som nekas åtkomst till resurser som begärs från den rekursiva sökvägen. Om inga andra land/region filtreringsalternativ har konfigurerats för den platsen, kommer sedan alla andra användare att ges tillgång.

Till exempel en geo-filtrering regel för blockering av sökvägen */foton/Strasbourg/* filtrerar följande filer:     
*http:\//\<endpoint >.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint >.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definiera de länder/regionerna
Från den **LANDSKODER** väljer du de länder/regioner som du vill blockera eller tillåta för sökvägen. 

När du har valt de länder/regionerna väljer **spara** att aktivera den nya regeln för geo-filtrering. 

![Geofiltreringsregler](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort en regel, markerar du den i listan på den **geofiltreringen** sidan och välj sedan **ta bort**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium från Verizon-profiler
För **för Azure CDN Premium från Verizon** användarprofiler, användargränssnittet för att skapa en regel för geo-filtrering skiljer sig:

1. Från menyn högst upp i din Azure CDN-profil väljer **hantera**.

2. Verizon-portalen väljer du **HTTP stora**och välj sedan **Landsfiltrering**.

    ![Standard GEO-filtrering](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Välj **Lägg till Filter för land**.

    Den **steg ett:** visas.

4. Ange katalogsökvägen, Välj **blockera** eller **Lägg till**och välj sedan **nästa**.

    Den **steg två:** visas. 

5. Välj en eller flera länder/regioner i listan och välj sedan **Slutför** att aktivera regeln. 
    
    Den nya regeln visas i tabellen på den **Landsfiltrering** sidan.

    ![Geofiltreringsregler](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Rensa resurser
I landet/regionen filtrering tabellen, väljer du ikonen Ta bort bredvid en regel för att ta bort den eller redigeringsikonen för att ändra den.

## <a name="considerations"></a>Överväganden
* Ändringar i konfigurationen av geo-filtrering gälla inte omedelbart:
   * För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
   * För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
   * För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
 
* Den här funktionen har inte stöd för jokertecken (till exempel *).

* Konfigurationen för geo-filtrering som är associerade med den relativa sökvägen är tillämpas rekursivt till denna sökväg.

* Endast en regel kan tillämpas på samma relativa sökväg. Det vill säga att du inte skapa flera filter för land/region som pekar på samma relativa sökväg. Eftersom land/region filter är rekursiv, kan en mapp ha flera filter för land/region. Med andra ord en undermapp till en tidigare konfigurerade mapp som kan tilldelas ett annat land/region-filter.

* Funktionen för geo-filtrering använder landskoder för att definiera de länder/regioner som en begäran tillåts eller blockeras för en skyddad katalog. Akamai och Verizon-profiler stöder de flesta av samma landskoder, finns men det några skillnader. Mer information finns i [Azure CDN landskoder](/previous-versions/azure/mt761717(v=azure.100)). 

