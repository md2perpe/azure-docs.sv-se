---
title: Utföra bildåtgärder – Python
titlesuffix: Azure Cognitive Services
description: Lär dig att använda API för visuellt innehåll med Python med hjälp av Jupyter-anteckningsböcker. Visualisera dina resultat med hjälp av populära bibliotek.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604161"
---
# <a name="computer-vision-api-jupyter-notebook"></a>Datorn Vision API Jupyter-anteckningsbok

Den här guiden visar hur du använder den API för visuellt innehåll i Python och hur du visualisera dina resultat med hjälp av populära bibliotek. Du använder Jupyter om du vill köra självstudien. Information om hur du kommer igång med interaktiva Jupyter-anteckningsböcker finns i [Jupyter-dokumentationen](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Förutsättningar

- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget
- [Jupyter Notebook](https://jupyter.org/install) installerat

## <a name="open-the-notebook-in-jupyter"></a>Öppna anteckningsboken i Jupyter 

1. Gå till GitHub-lagringsplatsen [Cognitive Vision Python](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Klicka på den gröna knappen för att klona eller hämta lagringsplatsen. 
3. Öppna en kommandotolk och gå till mappen **Cognitive-Vision-Python\Jupyter Notebook**.
1. Kontrollera att du har alla bibliotek som krävs genom att köra kommandot `pip install requests opencv-python numpy matplotlib` från Kommandotolken.
1. Starta Jupyter genom att köra kommandot `jupyter notebook` från Kommandotolken.
1. I fönstret Jupyter klickar du på _API för visuellt innehåll example.ipynb_ för att öppna anteckningsboken för självstudien.

## <a name="run-the-notebook"></a>Köra anteckningsboken

Om du vill använda den här anteckningsboken behöver du en prenumerationsnyckel för API:et för visuellt innehåll. Besök [prenumerationssidan](https://azure.microsoft.com/try/cognitive-services/) för att registrera dig. På **inloggningssidan** använder du ditt Microsoft-konto för inloggning och du kommer att kunna prenumerera och få kostnadsfria nycklar. När du har slutfört registreringsprocessen klistrar du in nyckeln i avsnittet `Variables` i anteckningsboken (återges nedan). Den primära eller sekundära nyckeln fungerar. Se till att ange nyckeln inom citattecken så att den blir en sträng.

Du måste också kontrollera att fältet `_region` matchar den region som motsvarar din prenumeration.

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

När du kör självstudien kommer du att kunna lägga till bilder att analysera, både från en URL och lokal lagring. Skriptet visar bilderna och analysinformationen i anteckningsboken.
