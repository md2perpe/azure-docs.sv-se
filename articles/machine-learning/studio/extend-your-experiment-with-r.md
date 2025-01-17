---
title: Utöka experimentet med R
titleSuffix: Azure Machine Learning Studio
description: Så här utökar funktionerna i Azure Machine Learning Studio via R-språket med hjälp av modulen köra R-skript.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 8c1292d0d36874892a286d91b1e367c7336b99aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811411"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Azure Machine Learning Studio: Utöka experimentet med R 
Du kan utöka funktionerna i Azure Machine Learning Studio via R-språket med hjälp av den [kör R-skript] [ execute-r-script] modulen.

Den här modulen accepterar flera indatauppsättningar och ger en enda datauppsättning som utdata. Du kan skriva ett R-skript i den **R-skriptet** -parametern för den [kör R-skript] [ execute-r-script] modulen.

Du har åtkomst till varje indataporten för modulen med hjälp av kod som liknar följande:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Visa en lista över alla installerade paket
Ändra listan över installerade paket. En lista över installerade paket finns i [R-paket som stöds av Azure Machine Learning Studio](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Du kan också hämta fullständig, aktuell lista över installerade paket genom att ange följande kod till den [kör R-skript] [ execute-r-script] modulen:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Listan över paket skickas till utdataporten för den [kör R-skript] [ execute-r-script] modulen.
Om du vill visa paketlistan, Anslut en konvertering till modulen som [konvertera till CSV] [ convert-to-csv] till vänstra utdataporten för den [kör R-skript] [ execute-r-script] -modulen Kör experimentet, och sedan klicka på utdata från modulen konvertering och välj **hämta**. 

![Hämta utdata från modulen ”konvertera till CSV”](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importera paket
Du kan importera paket som inte redan är installerade med hjälp av följande kommandon i den [kör R-skript] [ execute-r-script] modulen:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

där den `my_favorite_package.zip` filen innehåller ditt paket.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
