---
title: Fältet för studier entitetsattribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med fältet fallstudien om entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61339593"
---
# <a name="field-of-study-entity"></a>Fältet för studier entitet

<sub> * Följande attribut är specifika för fältet för studier entitet. (Ty = '6') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitets-ID                              |Int64      |Lika med
FN      |Fältet för studier normaliserade namn         |String     |Lika med
DFN     |Fältet för studier visningsnamn            |String     |Ingen
CC      |Fältet för antalet för studien total källhänvisningar    |Int32      |Ingen  
ECC     |Fältet för antalet totala uppskattade källhänvisningar|Int32      |Ingen
FL      |Nivå i fälten för studier hierarki     |Int32      |Är lika med, <br/>IsBetween
FP.FN   |Huvudfält för studier namn             |String     |Lika med
FP.FId  |Överordnade fält av studie-ID               |Int64      |Lika med
FC.FN   |Underordnade fält av studie namn              |String     |Lika med
FC.FId  |Underordnade fält av studie-ID                |Int64      |Lika med
