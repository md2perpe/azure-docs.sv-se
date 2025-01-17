---
title: Hur du begär data i realtid i Azure Maps | Microsoft Docs
description: Begär data i realtid med hjälp av Azure Maps mobilitetstjänsten.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329660"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Begär data i realtid med hjälp av Azure Maps Mobilitetstjänsten

Den här artikeln visar hur du använder Azure Maps [Mobilitetstjänsten](https://aka.ms/AzureMapsMobilityService) att begära i realtid överföring data.

I den här artikeln du lär dig hur du:


 * Begär nästa i realtid anslutningsbegäranden för alla rader som anländer till den angivna stop
 * Begär information i realtid för en viss cykel dockningsstation.


## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill göra något anrop till den offentliga Azure Maps-överföringen API: er, behöver du en Maps-konto och nyckel. Information om att skapa ett konto och hämta en nyckel finns i [så här hanterar du ditt Azure Maps-konto och dina nycklar](how-to-manage-account-keys.md).

Den här artikeln används den [Postman-appen](https://www.getpostman.com/apps) att skapa REST-anrop. Du kan använda alla API-utvecklingsmiljö du föredrar.


## <a name="request-real-time-arrivals-for-a-stop"></a>Begäran i realtid anslutningsbegäranden för stoppar

Begär data i realtid anslutningsbegäranden för en viss offentliga överföring stoppa du ska kunna skicka en förfrågan om att den [Real-time anslutningsbegäranden API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) för Azure Maps [Mobilitetstjänsten](https://aka.ms/AzureMapsMobilityService). Du behöver den **metroID** och **stopID** att slutföra begäran. Mer information om hur du begär dessa parametrar finns i vår anvisningar som guide till [begär offentlig överföring vägar](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Nu ska vi använda ”522” som våra appar i metro-ID som är metro ID för ”Bellevue – Tacoma – Seattle, WA”, och Använd stop-ID ”2060603”, vilket är en buss Avsluta när ”Ne 24th St & 162nd ARA Ne, Bellevue WA”. Om du vill begära fem i realtid anslutningsbegäranden data för alla nästa live anslutningsbegäranden på den här stop, gör du följande:

1. Skapa en samling som ska lagra begäranden. I Postman-appen, väljer **New**. I den **Skapa ny** väljer **samling**. Ge samlingen ett namn och välj den **skapa** knappen.

2. För att skapa begäran, Välj **New** igen. I den **Skapa ny** väljer **begära**. Ange en **frågenamn** för begäran, väljer du den samling som du skapade i föregående steg som platsen där du vill spara begäran och välj sedan **spara**.

    ![Skapa en begäran i Postman](./media/how-to-request-transit-data/postman-new.png)

3. Välj Hämta HTTP-metod på fliken builder och ange följande URL för att skapa en GET-begäran.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Efter en lyckad begäran får du följande svar.  Observera att parametern-scheduleType' definierar om den uppskattade ankomsttiden baserat på data i realtid eller statisk.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Data i realtid för cykel dockningsstation

Den [hämta överföring docka Info API](https://aka.ms/AzureMapsMobilityTransitDock) mobilitetstjänsten för Azure Maps tillåter för att begära statiskt och i realtid information, till exempel tillgänglighet och lediga platsen för en viss cykel eller scooter dockningsstation. Vi gör en begäran att hämta data i realtid för en dockningsstation för cyklar.

Om du vill göra en begäran kom överföring docka Info API, behöver de **dockId** för den stationen. Du kan hämta docka-ID genom att göra en sökbegäran till den [hämta i närheten överföring API](https://aka.ms/AzureMapsMobilityNearbyTransit) och inställningen för den **objectType** parameter ”bikeDock”. Följ stegen nedan för att hämta data i realtid med en dockningsstation för cyklar.


### <a name="get-dock-id"></a>Hämta docka-ID

Att hämta **dockID**, Följ stegen nedan för att göra en begäran att hämta i närheten överföring API: et:

1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ge den namnet **Get docka ID**.

2.  På fliken Builder väljer den **hämta** HTTP-metoden, ange följande URL för begäran och klickar på **skicka**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Efter en lyckad begäran får du följande svar. Observera att vi nu har den **id** i svaret, som kan användas senare som frågeparameter i förfrågan om att hämta överföring docka Info API.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Hämta i realtid cykel docka status

Följ stegen nedan för att göra en begäran kom överföring docka Info API: et att hämta data i realtid för valda dockan.

1. I Postman, klickar du på **ny begäran** | **GET-begäran** och ge den namnet **hämta data i realtid dockan**.

2.  På fliken Builder väljer den **hämta** HTTP-metoden, ange följande URL för begäran och klickar på **skicka**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Efter en lyckad förfrågan får du ett svar med följande struktur:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Nästa steg

Lär dig hur du begär överföring data med hjälp av Mobilitetstjänsten:

> [!div class="nextstepaction"]
> [Hur du begär data för överföring](how-to-request-transit-data.md)

Utforska Azure Maps Mobility Service API-dokumentationen:

> [!div class="nextstepaction"]
> [Mobility Service API-dokumentation](https://aka.ms/AzureMapsMobilityService)
