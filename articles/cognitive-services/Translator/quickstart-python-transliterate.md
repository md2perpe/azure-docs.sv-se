---
title: 'Snabbstart: Transkribera text, Python – Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig hur du transkriberar (konverterar) text från ett skript till ett annat med hjälp av Python och Translator Text REST API. I det här exemplet translittereras japanska till det latinska alfabetet.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: c6c6bb526c78ce87b9ee2acca05f478fee0ae5ff
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704362"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-python"></a>Snabbstart: Använda Translator Text API till att transkribera text med hjälp av Python

I den här snabbstarten lär du dig hur du transkriberar (konverterar) text från ett skript till ett annat med hjälp av Python och Translator Text REST API. I det angivna exemplet translittereras japanska till det latinska alfabetet.

För den här snabbstarten krävs ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med en Translator Text-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

>[!TIP]
> Om du vill att all kod på samma gång källkoden för det här exemplet är tillgänglig i [GitHub]().

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Python 2.7.x eller 3.x
* En Azure-prenumerationsnyckel för Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt projekt med hjälp av din favorit-IDE eller redigerare eller en ny mapp med en fil med namnet `transliterate-text.py` på skrivbordet. Kopiera sedan det här kodfragmentet till din projektfilen:

```python
# -*- coding: utf-8 -*-
import os
import requests
import uuid
import json
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests uuid`.

Den första kommentaren instruerar Python-tolken att använda UTF-8-kodning. Sedan importeras de moduler som krävs för att läsa prenumerationsnyckeln från en miljövariabel, skapa HTTP-begäran, skapa en unik identifierare samt hantera det JSON-svar som returneras av Translator Text API.

## <a name="set-the-subscription-key-base-url-and-path"></a>Ange prenumerationsnyckeln, bas-URL och sökvägen

Det här exemplet kommer att försöka läsa din Translator Text-prenumerationsnyckel från miljövariabeln `TRANSLATOR_TEXT_KEY`. Om du inte känner till miljövariabler kan du ange `subscriptionKey` som en sträng och kommentera bort den villkorliga instruktionen.

Kopiera den här koden till projektet:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

Den globala slutpunkten för Translator Text anges som `base_url`. `path` anger `transliterate`-vägen och identifierar att vi vill nå version 3 av API:et.

`params` används för att ange indataspråk och indata- och utdataskript. I det här exemplet translittereras japanska med det latinska alfabetet.

>[!NOTE]
> Mer information om slutpunkter, vägar och att begära parametrar finns i [Translator Text API 3.0: Transkribera](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Lägga till sidhuvuden

Det enklaste sättet att autentisera en begäran är att skicka din prenumerationsnyckel som ett `Ocp-Apim-Subscription-Key`-sidhuvud, vilket är det vi använder i det här exemplet. Alternativt kan du byta din prenumerationsnyckel mot en åtkomsttoken och skicka vidare åtkomsttoken som ett `Authorization`-sidhuvud för att verifiera din begäran. Mer information finns i [Autentisering](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Kopiera det här kodavsnittet till projektet:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Om du använder en flera tjänster Cognitive Services-prenumeration, måste du också inkludera den `Ocp-Apim-Subscription-Region` i dina begäranparametrar. [Mer information om autentisering med flera tjänster prenumerationen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-transliterate-text"></a>Skapa en begäran för att transkribera text

Definiera den sträng (eller de strängar) som du vill transkribera:

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
}]
```

Nu skapar vi en POST-begäran med hjälp av modulen `requests`. Den tar tre argument: den sammanfogade URL:en, begärandesidhuvudena samt begärandetexten:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Skriva ut svaret

Det sista steget är att skriva ut resultatet. Det här kodavsnittet gör resultatet prydligare genom att sortera nycklarna, konfigurera indrag samt deklarera objekt- och nyckelseparatorer.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Färdigställa allt

Det var allt – du har skapat ett enkelt program som anropar Translator Text API och returnerar en JSON-svar. Nu är det dags att köra programmet:

```console
python transliterate-text.py
```

Om du vill jämföra din kod med vår finns det fullständiga exemplet på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Exempelsvar

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har hårdkodat din prenumerationsnyckel i programmet ser du till att ta bort prenumerationsnyckeln när du är klar med den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

Ta en titt på API-referens för att förstå allt du kan göra med Translator Text API.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Se också

Lär dig att använda Translator Text API för att:

* [Översätta text](quickstart-python-translate.md)
* [Identifiera språket efter indata](quickstart-python-detect.md)
* [Hämta alternativa översättningar](quickstart-python-dictionary.md)
* [Hämta en lista över språk som stöds](quickstart-python-languages.md)
* [Fastställa meningslängd utifrån indata](quickstart-python-sentences.md)
