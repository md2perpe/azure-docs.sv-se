---
title: En referensguiden för Vydesigner delar i Azure Monitor | Microsoft Docs
description: Genom att använda Vydesigner i Azure Monitor kan skapa du anpassade vyer som visas i Azure-portalen och innehåller en mängd olika visualiseringar på data i Log Analytics-arbetsytan. Den här artikeln är en referensguide i inställningarna för visualisering delarna som är tillgängliga i dina anpassade vyer.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.openlocfilehash: dead1fae9bc3287ed0fc80c6120914e965ef96dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61341931"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Referensguide för Vydesigner visualisering delar i Azure Monitor
Genom att använda Vydesigner i Azure Monitor kan skapa du en mängd olika anpassade vyer i Azure portal som kan hjälpa dig att visualisera data i Log Analytics-arbetsytan. Den här artikeln är en referensguide i inställningarna för visualisering delarna som är tillgängliga i dina anpassade vyer.

Mer information om Vydesigner finns:

* [Visa Designer](view-designer.md): Översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.
* [Panelen referens](view-designer-tiles.md): Innehåller en referens till inställningarna för varje panel som är tillgängliga i din anpassade vyer.


Tillgängliga typer av Vydesigner paneler beskrivs i följande tabell:

| Vytyp | Beskrivning |
|:--- |:--- |
| [Lista över frågor](#list-of-queries-part) |Visar en lista över loggfrågor. Du kan välja varje fråga för att visa resultaten. |
| [Antal och lista](#number-and-list-part) |Rubriken visar ett tal som visar antalet poster från en loggfråga. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Två siffror och lista](#two-numbers-and-list-part) |Rubriken visar två tal som visar antalet poster från separat logg-frågor. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Ringdiagram och lista](#donut-and-list-part) |Rubriken visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. Ringen visar grafiskt resultat för de tre översta posterna. |
| [Två tidslinjer och lista](#two-timelines-and-list-part) |Rubriken visas resultatet av två loggfrågor med tiden då stående stapeldiagram med en uppmaning som visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Information](#information-part) |Rubriken visar statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text. |
| [Linjediagram, bildtext och lista](#line-chart-callout-and-list-part) |Rubriken visar ett linjediagram med flera serier från en loggfråga över tid och en uppmaning med ett sammanfattande värde. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Linjediagram och lista](#line-chart-and-list-part) |Rubriken visar ett linjediagram med flera serier från en loggfråga över tid. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Stack för rad diagram del](#stack-of-line-charts-part) |Visar tre separata linjediagram med flera serier från en loggfråga över tid. |

I nästa avsnitt beskrivs typerna av paneler och deras egenskaper i detalj.

> [!NOTE]
> Delar i vyer som är baserade på [logga frågor](../log-query/log-query-overview.md) i Log Analytics-arbetsytan. De stöder för närvarande inte [resource frågor mellan](../log-query/cross-workspace-query.md) att hämta data från Application Insights.

## <a name="list-of-queries-part"></a>Lista över frågor del
Lista över frågor del visar en lista över loggfrågor. Du kan välja varje fråga för att visa resultaten. Vyn innehåller en enkel fråga som standard, och du kan välja **+ fråga** att lägga till ytterligare frågor.

![Lista över frågor vy](media/view-designer-parts/view-list-queries.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Titel |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Förvalda filter |En kommaavgränsad lista över egenskaper som ska ingå i den vänstra filterfönstret när du väljer en fråga. |
| Återgivningsläge |Den inledande vyn som visas när frågan har valts. Du kan välja alla vyer som är tillgängliga när de öppnar frågan. |
| **Frågor** | |
| Sökfråga |Frågan ska köras. |
| Eget namn | Ett beskrivande namn som visas. |

## <a name="number-and-list-part"></a>En del tal och lista
Rubriken visar ett tal som visar antalet poster från en loggfråga. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Lista över frågor vy](media/view-designer-parts/view-number-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik** | |
| Förklaring |Den text som visas överst i rubriken. |
| Fråga |Frågan ska köras för sidhuvudet. Antalet poster som returneras av frågan visas. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** | |
| Fråga |Frågan ska köras för listan. Första två egenskaperna för de första tio posterna i resultaten visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt som baseras på det relativa värdet för den numeriska kolumnen.<br><br>Använd den `Sort` i frågan för att sortera poster i listan. Om du vill köra frågan och returnerar alla poster, kan du välja **se alla**. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Två tal och en del lista
Rubriken har två siffror som visar antalet poster från separat logg-frågor. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Visa två siffror och lista](media/view-designer-parts/view-two-numbers-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik-navigering** | |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Rubrik** | |
| Förklaring |Den text som visas överst i rubriken. |
| Fråga |Frågan ska köras för sidhuvudet. Antalet poster som returneras av frågan visas. |
| **Lista** | |
| Fråga |Frågan ska köras för listan. Första två egenskaperna för de första tio posterna i resultaten visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt baserat på det relativa värdet för den numeriska kolumnen.<br><br>Använd den `Sort` i frågan för att sortera poster i listan. Om du vill köra frågan och returnerar alla poster, kan du välja **se alla**. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="donut-and-list-part"></a>En del ringdiagram och lista
Rubriken visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. Ringen visar grafiskt resultat för de tre översta posterna.

![Ringdiagram och lista](media/view-designer-parts/view-donut-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Header** | |
| Titel |Den text som visas överst i rubriken. |
| Underrubrik |Den text som visas under rubriken överst i rubriken. |
| **Ringdiagram** | |
| Fråga |Frågan ska köras för ringen. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Ringdiagram** |**> Center** |
| Text |Den text som visas under värdet mellan ringen. |
| Åtgärd |Åtgärd att utföra på värdeegenskapen och sammanfatta som ett enda värde.<ul><li>Sum: Lägger till värdena för alla poster.</li><li>Procent: Förhållandet mellan poster som returneras av värdena i **resultera värden som används i mittåtgärd** för totalt antal poster i frågan.</li></ul> |
| Resultatvärden som används i mittåtgärd |Du kan också klicka på plustecknet (+) att lägga till ett eller flera värden. Frågans resultat är begränsade till poster med egenskapsvärden som du anger. Om inga värden har lagts till, ingår alla poster i frågan. |
| **Ytterligare alternativ** |**> Färger** |
| Färg 1<br>Färg 2<br>Färg 3 |Välj färg för var och en av de värden som visas i ringdiagrammet. |
| **Ytterligare alternativ** |**> Avancerad färgmappning** |
| Fältvärde |Skriv namnet på ett fält att visa den som en annan färg om den ingår i ringdiagrammet. |
| Färg |Välj färg för unika fält. |
| **Lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Två tidslinjer och lista del
Rubriken visas resultatet av två loggfrågor med tiden då stående stapeldiagram med en uppmaning som visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Två och tidslinjer lista](media/view-designer-parts/view-two-timelines-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik-navigering** | |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Först diagrammets<br>andra diagram** | |
| Förklaring |Den text som visas under bildtext för den första serien. |
| Färg |Färgen som ska användas för kolumnerna i serien. |
| Fråga |Frågan ska köras under den första serien. Antalet poster under varje tidsintervall representeras av diagramkolumner. |
| Åtgärd |Åtgärd att utföra på värdeegenskapen och sammanfatta som ett enskilt värde för bildtexten.<ul><li>Sum: Summan av värdena från alla poster.</li><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Senaste exempel: Värde från det sista intervallet som ingår i diagrammet.</li><li>Det första exemplet: Värde från det första intervallet som ingår i diagrammet.</li><li>Antal: Antalet poster som returneras av frågan.</li></ul> |
| **Lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="information-part"></a>Informationsdel
Rubriken visar statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text.

![Visa information](media/view-designer-parts/view-information.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Färg |Bakgrundsfärg för rubriken. |
| **Header** | |
| Image |Bildfilen som visas i rubriken. |
| Label (Etikett) |Den text som visas i rubriken. |
| **Header** |**> Länk** |
| Label (Etikett) |Länktext. |
| Url |URL: en för länken. |
| **Informationsobjekt** | |
| Titel |Den text som visas för rubriken för varje objekt. |
| Innehåll |Den text som visas för varje objekt. |

## <a name="line-chart-callout-and-list-part"></a>Linjediagram, bildtext och lista delar
Rubriken visar ett linjediagram med flera serier från en loggfråga över tid och en uppmaning med ett sammanfattande värde. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Linjediagram, bildtext och listvy](media/view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Header** | |
| Titel |Den text som visas överst i rubriken. |
| Underrubrik |Den text som visas under rubriken överst i rubriken. |
| **Linjediagram** | |
| Fråga |Frågan ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln i diagrammet använder det här tidsintervallet. Om frågan inte innehåller den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Linjediagram** |**> Bildtext** |
| Bildtextrubrik |Den text som visas ovanför bildtextvärdet. |
| Dataseriens namn |Egenskapsvärdet för serien för värde för pratbubbla. Om inga serier anges, används alla poster från frågan. |
| Åtgärd |Åtgärd att utföra på värdeegenskapen och sammanfatta som ett enskilt värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värde från det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet mellan intervall som ingår i diagrammet.</li><li>Min: Det minsta värdet mellan intervall som ingår i diagrammet.</li><li>Sum: Summan av värdena från alla poster.</li></ul> |
| **Linjediagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa diagrammet etiketter som beskriver värdetyperna och du kan också konvertera värdena. Den *enhet* typen anger kategorin för enheten och definierar de tillgängliga *aktuella enheten* skriver värden. Om du väljer ett värde i *konvertera till*, de numeriska värdena konverteras från den *aktuella enheten* skriver till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |
| **Lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="line-chart-and-list-part"></a>En del diagrammet och listan av rad
Rubriken visar ett linjediagram med flera serier från en loggfråga över tid. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Vy för diagrammet och listan av rad](media/view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Header** | |
| Titel |Den text som visas överst i rubriken. |
| Underrubrik |Den text som visas under rubriken överst i rubriken. |
| **Linjediagram** | |
| Fråga |Frågan ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln i diagrammet använder det här tidsintervallet. Om frågan inte innehåller den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Linjediagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa diagrammet etiketter som beskriver värdetyperna och du kan också konvertera värdena. Den *enhet* typen anger kategorin för enheten och definierar de tillgängliga *aktuella enheten* skriver värden. Om du väljer ett värde i *konvertera till*, de numeriska värdena konverteras från den *aktuella enheten* skriver till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |
| **Lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stack för rad diagram del
Stack med linjediagram visar tre separata linjediagram med flera serier från en loggfråga över tid, som visas här:

![Stack med linjediagram](media/view-designer-parts/view-stack-line-charts.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| **Skapa diagram över 1<br>diagram 2<br>diagrammets 3** |**> Header** |
| Titel |Den text som visas överst i diagrammet. |
| Underrubrik |Den text som visas under rubriken överst i diagrammet. |
| **Skapa diagram över 1<br>diagram 2<br>diagrammets 3** |**Linjediagram** |
| Fråga |Frågan ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln i diagrammet använder det här tidsintervallet. Om frågan inte innehåller den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Diagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa diagrammet etiketter som beskriver värdetyperna och du kan också konvertera värdena. Den *enhet* typen anger kategorin för enheten och definierar de tillgängliga *aktuella enheten* skriver värden. Om du väljer ett värde i *konvertera till*, de numeriska värdena konverteras från den *aktuella enheten* skriver till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |

## <a name="common-settings"></a>Vanliga inställningar
I följande avsnitt beskrivs inställningar som är gemensamma för flera olika delar av visualiseringen.

### <a name="name-value-separator"></a>Namn och värde avgränsare
Avgränsaren som namn och värde är den du använder för att parsa textegenskapen från en listfråga till flera värden avgränsaren. Om du anger en avgränsare, kan du ange namn för varje fält, avgränsade med samma avgränsare i den **namn** box.

Anta exempelvis att en egenskap som kallas *plats* som finns med värden som *Redmond byggnad 41* och *Bellevue byggnad 12*. Du kan ange ett bindestreck (-) för namnet och värdet avgränsare och *Stad Byggnad* för namnet. Den här metoden Parsar varje värde i två egenskaper som kallas *Stad* och *byggnad*.

### <a name="click-through-navigation"></a>Genomklickningsnavigering
Klicka igenom navigering definierar vilken åtgärd som ska vidtas när du klickar på en huvud- eller listobjekt i en vy.  Då öppnas en fråga i antingen den [Log Analytics](../../azure-monitor/log-query/portals.md) eller starta en annan vy.

I följande tabell beskrivs inställningarna för klicka igenom navigering.

| Inställning           | Beskrivning |
|:--|:--|
| Loggsökning (automatisk) | Log-frågan ska köras när du väljer ett huvud-objekt.  Det här är samma loggfråga som objektet baseras på.
| Loggsökning        | Log-frågan ska köras när du väljer ett objekt i en lista.  Skriv frågan i den **navigeringsfråga** box.   Använd *{markerade objektet}* att inkludera syntaxen för det objekt som användaren har valt.  Exempel: Om frågan har en kolumn med namnet *datorn* och navigering frågan är *{markerade objektet}* , en fråga som *datorn = ”den här datorn”* körs när du väljer en dator. Om frågan navigering är *typ = händelse {markerade objektet}* , frågan *typ = händelse Computer = ”den här datorn”* körs. |
| Visa              | Vy som ska öppnas när du väljer ett sidhuvud eller ett objekt i en lista.  Välj namnet på en vy i din arbetsyta i den **vynamn** box. |



### <a name="sparklines"></a>Sparklines
Miniatyrdiagram är ett litet linjediagram som visar värdet för en listpost över tid. För visualisering delar med en lista, kan du välja om du vill visa ett vågrätt fält som anger det relativa värdet för en numerisk kolumn eller ett miniatyrdiagram, som anger dess värde över tid.

I följande tabell beskrivs inställningarna för miniatyrdiagram:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. |
| Åtgärd |Om miniatyrdiagram är aktiverade, är åtgärden som ska utföras på varje egenskap i listan för att beräkna värdena för miniatyrdiagrammet.<ul><li>Senaste exempel: Det sista värdet för serien över ett tidsintervall.</li><li>Max: Det maximala värdet för seriens över ett tidsintervall.</li><li>Min: Det minsta värdet för seriens över ett tidsintervall.</li><li>Sum: Summan av värdena för serie över ett tidsintervall.</li><li>Sammanfattning: Använder samma `measure` kommandot eftersom frågan i rubriken.</li></ul> |

### <a name="thresholds"></a>Tröskelvärden
Du kan visa en färgad ikon bredvid varje objekt i en lista med hjälp av tröskelvärden. Tröskelvärden ger dig en snabb visuell indikering för objekt som överskrider ett visst värde eller faller inom ett visst intervall. Du kan till exempel visa en grön ikon för artiklar med ett giltigt värde, gult om värdet är inom ett intervall som indikerar en varning och rött om det överskrider ett felvärde.

När du aktiverar tröskelvärden för en del måste du ange en eller flera tröskelvärden. Om värdet för ett objekt är större än ett tröskelvärde och lägre än tröskelvärdet för nästa, används färg för detta värde. Om objektet är större än det högsta tröskelvärdet, används en annan färg. 

Varje uppsättning tröskelvärdet har ett tröskelvärde med värdet **standard**. Det här är den färg som anges om inga andra värden har överskridits. Du kan lägga till eller ta bort tröskelvärden genom att välja den **Lägg till** (+) eller **ta bort** (x)-knappen.

I följande tabell beskrivs inställningarna för tröskelvärden:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera tröskelvärden |Välj den här länken för att visa en färg-ikonen längst till vänster på varje värde. Ikonen visar värdets hälsotillstånd i förhållande till angivna tröskelvärden. |
| Namn |Namnet på tröskelvärdet. |
| Tröskelvärde |Värdet för tröskelvärdet. Färgen för hälsotillståndet för varje listobjekt har angetts till färgen på det högsta tröskelvärdet överskrids med objektets värde. Om inget tröskelvärden överskrids, används en standardfärg. |
| Färg |Färgen som anger tröskelvärdet. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga frågor](../log-query/log-query-overview.md) för frågorna i delar av visualiseringen.
