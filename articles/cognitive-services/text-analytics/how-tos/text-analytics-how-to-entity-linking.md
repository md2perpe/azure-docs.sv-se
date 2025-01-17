---
title: Använda igenkänning av entiteter med API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du känner igen entiteter med hjälp av den REST API för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: ff4f9af82024e9d39ad89a39bcb2fe4130de9101
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304186"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Hur du använder med namnet Entitetsidentifiering i textanalys

Den [med namnet entitet för Talarigenkänning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) tar ostrukturerad text och för varje JSON-dokument, returnerar du en lista över skiljas åt entiteter med länkar till mer information på webben (Wikipedia och Bing). 

## <a name="entity-linking-and-named-entity-recognition"></a>Entitetslänkning och igenkänning av namngivna entiteter

Text Analytics `entities` slutpunkt stöder både namngivna entitetsidentifiering (NER) och för entitetslänkning.

### <a name="entity-linking"></a>Entity Linking
Entitetslänkning är möjligheten att identifiera och disambiguate identiteten för en entitet som påträffats i texten (till exempel bestämma om ”Mars” används som arrangemang eller latinska krigsguden). Den här processen kräver förekomsten av en knowledge base som känns igen entiteter är länkade – Wikipedia används som kunskapsbas för den `entities` endpoint textanalys.

### <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna entiteter (NER)
Med namnet entitetsidentifiering är (NER) möjligheten att identifiera olika enheter i text och kategorisera dem i fördefinierade klasser. Klasserna stöds av entiteter visas nedan.

I textanalys [Version 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634), både entitetslänkning och igenkänning av namngivna entiteter (NER) är tillgängliga för flera språk. Se den [språkstöd](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) artikeln för mer information. 

### <a name="language-support"></a>Stöd för språk

Med hjälp av entitetslänkning på olika språk kräver en motsvarande kunskapsbas på varje språk. För entitetslänkning i Text Analytics, det innebär att alla språk som stöds av den `entities` endpoint länkar till motsvarande Wikipedia-Kristi på det språket. Eftersom storleken på korpus varierar mellan olika språk, förväntas varierar även funktionens återkallande för entitetslänkning.

## <a name="supported-types-for-named-entity-recognition"></a>Typer som stöds för igenkänning av namngivna entiteter

| Typ  | Undertyp | Exempel |
|:-----------   |:------------- |:---------|
| Person        | EJ TILLÄMPLIGT\*         | "Jeff", "Bill Gates"     |
| Location      | EJ TILLÄMPLIGT\*         | ”Redmond, Washington”, ”Paris”  |
| Organisation  | EJ TILLÄMPLIGT\*         | "Microsoft"   |
| Kvantitet      | Tal        | "6", "six"     | 
| Kvantitet      | Procent    | ”50 %”, ”femtio procent”| 
| Kvantitet      | Ordningstal       | ”2:a”, ”andra”     | 
| Kvantitet      | NumberRange   | ”4 till 8”     | 
| Kvantitet      | Ålder           | ”90 dagar gamla”, ”30 år”    | 
| Kvantitet      | Valuta      | ”10,99 USD”     | 
| Kvantitet      | Dimension     | ”10 miles”, ”40 cm”     | 
| Kvantitet      | Temperatur   | ”32 grader”    |
| DateTime      | EJ TILLÄMPLIGT\*         | ”18.30 den 4 februari 2012”      | 
| DateTime      | Date          | ”2 maj 2017”, ”2017-05-02”   | 
| DateTime      | Tid          | "8am", "8:00"  | 
| DateTime      | DateRange     | ”2 maj till 5 maj”    | 
| DateTime      | TimeRange     | ”18 till 19”     | 
| DateTime      | Duration      | ”1 minut och 45 sekunder”   | 
| DateTime      | Ange           | ”varje tisdag”     | 
| DateTime      | Tidszon      |    | 
| URL           | EJ TILLÄMPLIGT\*         | "https:\//www.bing.com"    |
| E-post         | EJ TILLÄMPLIGT\*         | "support@contoso.com" |

\* Beroende på indata- och extraherade entiteter, vissa entiteter kan utelämna den `SubType`.  Alla stöds entitetstyper som visas är endast tillgängliga för de engelska, förenklad kinesiska, franska, tyska och spanska språk.



## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID, text, språk

Språk som stöds för närvarande visas i [listan](../text-analytics-supported-languages.md).

Dokumentstorleken måste vara under 5 120 tecken per dokument, och du kan ha upp till 1 000 objekt (ID:n) per samling. Samlingen skickas i begäranstexten. I följande exempel är en illustration av innehåll som du kan skicka till länkramverk entitetsänden.

```
{"documents": [{"id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
                },
               {"id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
                }
               ]
}
```    
    
## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Information om begäransdefinitionen finns i [Hur anropar man textanalys API:et](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en **POST**-begäran. Läs API-dokumentationen för denna begäran: [API för Entity Linking](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Ange HTTP-slutpunkt för entitetextrahering. Den måste innehålla `/entities`-resursen: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1/entities`

+ Ange en begäransrubrik som inkluderar åtkomstnyckeln för textanalysåtgärder. Mer information finns i [Hitta slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. Se den [databegränsningar](../overview.md#data-limits) avsnittet i översikten för information om storlek och antal begäranden som du kan skicka per minut och sekund.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.

## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-begäranden returnerar ett JSON-formaterat svar med ID:n och identifierade egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data.

Ett exempel på utdata för entitetslänkning visas nästa:

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```


## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för entitetslänkning med textanalys i Cognitive Services. Sammanfattningsvis:

+ [Entiteter API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) är tillgänglig för valda språken.
+ JSON-dokument i begärandetexten innehålla ett ID, text och språk-kod.
+ POST-begäran riktas till en `/entities`-slutpunkt med hjälp av en personligt anpassad [åtkomstnyckel och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för din prenumeration.
+ Svarsutdata som består av länkade entiteter (inklusive säker poäng, förskjutningar och webblänkar, för varje dokument ID) kan användas i alla program

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API för textanalys](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Översikt över Textanalys](../overview.md)  
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
* [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712) 
