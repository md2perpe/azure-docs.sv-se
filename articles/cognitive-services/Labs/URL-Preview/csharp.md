---
title: 'Snabbstart: Förhandsversion av projekt-URL, C#'
titlesuffix: Azure Cognitive Services
description: Kom igång med förhandsgranskning av projekt-URL med C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 2cb3f1de803b8549a88866da1042f4f32609a601
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827153"
---
# <a name="quickstart-url-preview-query-in-c"></a>Snabbstart: URL-förhandsgranskningsfråga i C#

Följande C#-exempel skapar en URL-förhandsgranskning för webbplatsen SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/) att köra den här koden på Windows. (Den kostnadsfria Community Edition fungerar.)

Få en åtkomstnyckel för den kostnadsfria utvärderingsversionen av [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Kodscenario

Följande C#-kod skapar en URL-förhandsgranskning för webbplatsen SwiftKey: https://swiftkey.com/en. 

Den implementeras i följande steg:
1. Deklarera variabler för att ange slutpunkten och en fråge-URL att förhandsgranska.  
2. Skapa begäran.
3. Lägg till sidhuvudet *Ocp-Apim-Subscription-Key*. 
4. Kör webbegäran asynkront. 
5. Läs svaret.
6. Skriv ut sidhuvuden och JSON-resultaten till konsolen.

**Källkod**

```
using System;
using System.IO;
using System.Net;
using System.Text;

namespace UrlPrevCshp
{
    class Program
    {
        static void Main(string[] args)
        {
            String uriBase = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search";
            String searchQuery = "https://swiftkey.com/en"; 
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery);

            // Do the Web request and get response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR-SUBSCRIPTION-KEY";
            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            Console.WriteLine("\nHTTP Headers:\n");
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    Console.WriteLine(header + ": " + response.Headers[header]);
            }

            Console.WriteLine(JsonPrettyPrint(json));
            

            Console.ReadKey();
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }

    }
}

```
## <a name="running-the-application"></a>Köra programmet

Så här kör du programmet:

1. Skapa en ny konsollösning i Visual Studio.
2. Ersätt `Program.cs` med den angivna koden.
3. Ersätt värdet `YOUR-ACCESS-KEY` med en giltig åtkomstnyckel för din prenumeration.
4. Kör programmet.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för JavaScript](javascript.md)
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)
