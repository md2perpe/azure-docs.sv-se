---
title: Visualisera data med Azure Data Explorer-kopplingen för Power BI
description: 'I den här artikeln får du lära dig hur du använder en av de tre alternativen för att visualisera data i Power BI: anslutningsprogrammet för Power BI för Datautforskaren i Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 0bfacc968a04f8ef9e39a31cff5e81cf4e04c6fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494629"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Visualisera data med Azure Data Explorer-kopplingen för Power BI

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Power BI är en lösning för företagsanalys där du kan visualisera dina data och dela resultaten i hela organisationen.

Azure Data Explorer ger tre alternativ för att ansluta till data i Power BI: använda den inbyggda anslutningsappen, importera en fråga från Azure Data Explorer eller använda en SQL-fråga. Den här artikeln visar hur du använder den inbyggda anslutningen för att hämta data och visualisera den i en Power BI-rapport.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att slutföra den här artikeln:

* En organisations e-postkonto som är medlem i Azure Active directory, så att du kan ansluta till [Azure Data Explorer-hjälpklustret](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (välj **DOWNLOAD FREE**) (Ladda ned kostnadsfritt)

## <a name="get-data-from-azure-data-explorer"></a>Hämta data från Azure Data Explorer

Först ansluter du till Azure Data Explorer-hjälpklustret, och sedan hämtar du in en delmängd av data från tabellen *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. I Power BI Desktop går du till fliken **Start**, väljer **Hämta data** och sedan **Mer**.

    ![Hämta data](media/power-bi-connector/get-data-more.png)

1. Sök efter *Azure Data Explorer*väljer **Azure Data Explorer** sedan **Connect**.

    ![Sök efter och hämta data](media/power-bi-connector/search-get-data.png)

1. På den **Azure Data Explorer (Kusto)** skärmen, Fyll i formuläret med följande information.

    ![Kluster-, databas-, tabellalternativ](media/power-bi-connector/cluster-database-table.png)

    **Inställning** | **Värde** | **Fältbeskrivning**
    |---|---|---|
    | Kluster | *https://help.kusto.windows.net* | URL för hjälpklustret. För andra kluster är URL:en i formatet *https://\<Klusternamn\>.\<Region\>.kusto.windows.net*. |
    | Databas | Lämna tomt | En databas som finns på det kluster som du ansluter till. Vi väljer detta i ett senare steg. |
    | Tabellnamn | Lämna tomt | En av tabellerna i databasen, eller en fråga som <code>StormEvents \| take 1000</code>. Vi väljer detta i ett senare steg. |
    | Avancerade alternativ | Lämna tomt | Alternativ för dina frågor, till exempel storlek på resultatuppsättning. |
    | Dataanslutningsläge | *DirectQuery* | Anger huruvida Power BI importerar data eller ansluter direkt till datakällan. Du kan använda endera alternativ med den här anslutningsappen. |
    | | | |

1. Om du inte redan har en anslutning till hjälpklustret loggar du in. Logga in med ett organisationskonto och välj sedan **Anslut**.

    ![Logga in](media/power-bi-connector/sign-in.png)

1. På skärmen **Navigatör** expanderar du databasen **Exempel**, väljer **StormEvents** och väljer sedan **Redigera**.

    ![Välj tabell](media/power-bi-connector/select-table.png)

    Tabellen öppnas i Power Query Editor, där du kan redigera rader och kolumner innan du importerar data.

1. I Power Query Editor väljer du pilen intill kolumnen **DamageCrops** och sedan **Sortera fallande**.

    ![Sortera DamageCrops fallande](media/power-bi-connector/sort-descending.png)

1. På fliken **Start** väljer du **Behåll rader** och sedan **Behåll de översta raderna**. Ange värdet *1000* för att hämta de översta 1000 raderna i den sorterade tabellen.

    ![Behåll de översta raderna](media/power-bi-connector/keep-top-rows.png)

1. På fliken **Start** väljer du **Stäng och tillämpa**.

    ![Stäng och tillämpa](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualisera data i en rapport

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver den rapport som du skapade i den här artikeln kan du ta bort filen Power BI Desktop (.pbix).

## <a name="next-steps"></a>Nästa steg

[Visualisera data med hjälp av en fråga för importerade i Power BI](power-bi-imported-query.md)
