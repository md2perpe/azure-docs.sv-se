---
title: 'Självstudier: Skapa en översättningsapp med WPF, C# – Translator Text-API'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en WPF-app (Windows Presentation Foundation) som använder Cognitive Service-API:er för textöversättning, språkidentifiering och stavningskontroll med en enda prenumerationsnyckel. Den här övningen visar hur du använder funktioner från Translator Text-API:et och API:et för stavningskontroll i Bing.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 36d85e11133e7197212ae1b37609628689b68a13
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657929"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>Självstudier: Skapa en översättningsapp med WPF

I den här självstudien skapar du en [WPF-app (Windows Presentation Foundation)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2017) som använder Azure Cognitive Service för textöversättning, språkidentifiering och stavningskontroll med en enda prenumerationsnyckel. Specifikt kommer appen att anropa API:er från Translator Text och [Stavningskontroll i Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/).

Vad är WPF? Det är ett användargränssnittsramverk som skapar appar för skrivbordsklienter. WPF-utvecklingsplattformen har stöd för många olika funktioner för apputveckling, däribland en appmodell, resurser, kontroller, grafik, layout, databindning, dokument och säkerhet. Det är en delmängd av .NET Framework, så om du tidigare har skapat appar med .NET Framework med hjälp av ASP.NET eller Windows Forms bör programmeringen kännas bekant. WPF använder XAML (Extensible Application Markup Language) för att tillhandahålla en deklarativ modell för programmering av appar, vilket vi går igenom i kommande avsnitt.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * skapa ett WPF-projekt i Visual Studio
> * lägga till sammansättningar och NuGet-paket i projektet
> * skapa appens användargränssnitt med XAML
> * använda Translator Text-API:et för att hämta språk, översätta text och identifiera källspråket
> * använda API:et för stavningskontroll i Bing för att verifiera dina indata och öka noggrannheten för översättning
> * köra WPF-appen.

### <a name="cognitive-services-used-in-this-tutorial"></a>Cognitive Services som används i den här självstudien

Den här listan innehåller de Cognitive Services som används i den här självstudien. Följ länken för att bläddra i API-referensen för varje funktion.

| Tjänst | Funktion | Beskrivning |
|---------|---------|-------------|
| Translator Text | [Hämta språk](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | Hämta en fullständig lista över språk som stöds för textöversättning. |
| Translator Text | [Översätta](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | Översätt text till fler än 60 språk. |
| Translator Text | [Identifiera](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | Identifiera språket i indatatexten. Innehåller förtroendepoäng för identifiering. |
| Stavningskontroll i Bing | [Stavningskontroll](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | Förbättra översättningens noggrannhet genom att rätta stavfel. |

## <a name="prerequisites"></a>Förutsättningar

Innan vi fortsätter behöver du följande:

* En Azure Cognitive Services-prenumeration. [Hämta en Cognitive Services-nyckel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#multi-service-subscription).
* En Windows-dator
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) – Community eller Enterprise

> [!NOTE]
> Vi rekommenderar att du skapar prenumerationen i regionen USA, västra för den här självstudien. I annat fall behöver du ändra slutpunkter och regioner i koden allt eftersom du går igenom den här övningen.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Skapa en WPF-app i Visual Studio

Det första vi behöver göra är att konfigurera projektet i Visual Studio.

1. Öppna Visual Studio. Välj sedan **Arkiv > Nytt > Projekt**.
2. I den vänstra panelen letar du upp och väljer **Visual C#** . Välj sedan **WPF-App (.NET Framework)** i mittenpanelen.
   ![Skapa en WPF-app i Visual Studio](media/create-wpf-project-visual-studio.png)
3. Namnge projektet `MSTranslatorTextDemo`, inställd framework-version **.NET Framework 4.5.2 eller senare**, klicka sedan på **OK**.
4. Projektet har skapats. Lägg märke till att det finns två flikar öppna: `MainWindow.xaml` och `MainWindow.xaml.cs`. I den här självstudien lägger vi till kod i de här två filerna. Den första är till för appens användargränssnitt, och den andra är till för anropen till Translator Text och Stavningskontroll i Bing.
   ![Granska din miljö](media/blank-wpf-project.png)

I nästa avsnitt lägger vi till sammansättningar och ett NuGet-paket i projektet för ytterligare funktioner såsom JSON-parsning.

## <a name="add-references-and-nuget-packages-to-your-project"></a>Lägga till referenser och NuGet-paket i projektet

Projektet kräver ett antal .NET Framework-sammansättningar och NewtonSoft.Json som vi installerar med hjälp av NuGet-pakethanteraren.

### <a name="add-net-framework-assemblies"></a>Lägga till .NET Framework-sammansättningar

Vi lägger till sammansättningar i projektet för att serialisera och deserialisera objekt och för att hantera HTTP-begäranden och -svar.

1. Leta upp projektet i Solution Explorer i Visual Studio (högra panelen). Högerklicka på projektet och välj sedan **Lägg till > Referens...** , vilket öppnar **Reference Manager**.
   ![Lägga till sammansättningsreferenser](media/add-assemblies-sample.png)
2. Fliken med sammansättningar visar alla .NET Framework-sammansättningar som är tillgängliga för referens. Använd sökfältet längst upp till höger på skärmen för att söka efter dessa referenser och lägg till dem i projektet:
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Web.Extensions](https://docs.microsoft.com/dotnet/api/system.web)
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
3. När du har lagt till dessa referenser i projektet kan du klicka på **OK** för att stänga **Reference Manager**.

> [!NOTE]
> Mer information om sammansättningsreferenser finns i [How to: Add or remove reference using the Reference Manager](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2017) (Anvisningar: Lägga till eller ta bort referens med hjälp av Reference Manager).

### <a name="install-newtonsoftjson"></a>Installera NewtonSoft.Json

Appen använder NewtonSoft.Json för att deserialisera JSON-objekt. Följ dessa instruktioner för att installera paketet.

1. Leta upp projektet i Solution Explorer i Visual Studio och högerklicka på projektet. Välj **Hantera NuGet-paket...**
2. Leta upp och välj fliken **Bläddra**.
3. Skriv [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) i sökfältet.
   ![Leta upp och installera Newtonsoft.Json](media/add-nuget-packages.png)
4. Välj paketet och klicka på **Installera**.
5. När installationen är klar stänger du fliken.

## <a name="create-a-wpf-form-using-xaml"></a>Skapa ett WPF-formulär med hjälp av XAML

För att kunna använda appen behöver du ett användargränssnitt. Vi använder XAML för att skapa ett formulär där användarna kan välja språk för indata och översättning, ange text för översättning och visa resultatet av översättning.

Vi tar en titt på vad vi skapar.

![WPF XAML-användargränssnitt](media/translator-text-csharp-xaml.png)

Användargränssnittet innehåller följande komponenter:

| Namn | Typ | Beskrivning |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox (Kombinationsruta) | Visar en lista över de språk som stöds av Microsoft Translator för textöversättning. Användaren väljer det språk som översättningen görs från. |
| `ToLanguageComboBox` | ComboBox (Kombinationsruta) | Visar samma lista över språk som `FromComboBox` men används för att välja det språk som användaren översätter till. |
| `TextToTranslate` | TextBox | Gör att användaren kan ange text för översättning. |
| `TranslateButton` | Button (Knapp) | Använd den här knappen för att översätta text. |
| `TranslatedTextLabel` | Label (Etikett) | Visar översättningen. |
| `DetectedLanguageLabel` | Label (Etikett) | Visar det identifierade språket i den text som ska översättas (`TextToTranslate`). |

> [!NOTE]
> Vi skapar det här formuläret med hjälp av XAML-källkoden, men du kan skapa formuläret med redigeringsprogrammet i Visual Studio.

Vi tar och lägger till koden i projektet.

1. I Visual Studio väljer du fliken för `MainWindow.xaml`.
2. Kopiera den här koden till projektet och spara.
   ```xaml
   <Window x:Class="MSTranslatorTextDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorTextDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
3. Du bör nu se en förhandsgranskning av appens användargränssnitt i Visual Studio. Det bör se ut ungefär som på bilden ovan.

Det var allt – formuläret är klart. Nu ska vi skriva kod för att använda textöversättning och Stavningskontroll i Bing.

> [!NOTE]
> Du kan justera det här formuläret eller skapa ett eget.

## <a name="create-your-app"></a>Skapa appen

`MainWindow.xaml.cs` innehåller den kod som styr appen. I kommande avsnitt lägger vi till kod för att fylla i de nedrullningsbara menyerna och anropa några API:er som görs tillgängliga av Translator Text och Stavningskontroll i Bing.

* När programmet startar och `MainWindow` instansieras anropas metoden `Languages` för Translator Text-API:et för att hämta och fylla i de nedrullningsbara menyerna med språkval. Det här sker en gång i början av varje session.
* När knappen **Översätt** klickas hämtas användarens val av språk och text, stavningskontroll utförs på indata och översättningen samt det identifierade språket visas för användaren.
  * Metoden `Translate` för Translator Text-API:et anropas för att översätta text från `TextToTranslate`. Det här anropet innehåller även de `to`- och `from`-språk som valts med hjälp av de nedrullningsbara menyerna.
  * Metoden `Detect` för Translator Text-API:et anropas för att bestämma textspråket för `TextToTranslate`.
  * Stavningskontroll i Bing används för att verifiera `TextToTranslate` och rätta felstavningar.

Hela projektet är inkapslat i klassen `MainWindow : Window`. Vi börjar med att lägga till kod för att ange prenumerationsnyckeln, deklarera slutpunkter för Translator Text och stavningskontroll i Bing samt för att initiera appen.

1. I Visual Studio väljer du fliken för `MainWindow.xaml.cs`.
2. Ersätt de förifyllda `using`-instruktionerna med följande.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
3. Leta upp klassen `MainWindow : Window` och ersätt den med den här koden:
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator Text and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
   1. Lägg till din Cognitive Services-prenumerationsnyckel och spara.

I det här kodblocket har vi deklarerat två medlemsvariabler som innehåller information om tillgängliga språk för översättning:

| Variabel | Type | Beskrivning |
|----------|------|-------------|
|`languageCodes` | Matris med strängar |Cachelagrar språkkoderna. Translator-tjänsten använder korta koder som `en` för engelska, för att identifiera språk. |
|`languageCodesAndTitles` | Sorterad ordlista | Mappar ”egna” namn i användargränssnittet tillbaka till de korta koderna som används i API:et. Sorteras alfabetiskt utan hänsyn till skiftläge. |

I `MainWindow`-konstruktorn har vi sedan lagt till felhantering med `HandleExceptions`. Det här säkerställer att en avisering ges om ett undantag inte hanteras. Sedan körs en kontroll för att bekräfta att den prenumerationsnyckel som har angetts har en längt på 32 tecken. Ett fel utlöses om nyckeln är mindre/större än 32 tecken.

Om det finns nycklar som åtminstone har rätt längd sätter `InitializeComponent()`-anropet igång användargränssnittet genom att hitta, läsa in och instansiera XAML-beskrivningen av appens huvudfönster.

Slutligen har vi lagt till kod för att anropa metoder för att hämta språk för översättning och fylla i de nedrullningsbara menyerna för appens användargränssnitt. Vi går snart igenom koden bakom de här anropen.

## <a name="get-supported-languages"></a>Hämta språk som stöds

Translator Text-API:et stöder för närvarande fler än 60 språk. Eftersom stöd för nya språk läggs över tid rekommenderar vi att den Language-resurs (Språk) som görs tillgänglig av Translator Text anropas i stället för att språklistan i appen hårdkodas.

I det här avsnittet skapar vi en `GET`-begäran till Language-resursen som anger att vi vill ha en lista över språk som är tillgängliga för översättning.

> [!NOTE]
> Med Language-resursen kan du filtrera språkstöd med följande frågeparametrar: translitteration, ordlista och översättning. Mer information finns i [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

Innan vi går vidare tittar vi på exempelutdata för ett anrop till Language-resursen:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

Från dessa utdata kan vi extrahera språkkoden och `name` för ett specifikt språk. Appen använder NewtonSoft.Json för att deserialisera JSON-objektet ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)).

Vi fortsätter där vi slutade i det förra avsnittet genom att lägga till en metod för att hämta språk som stöds till appen.

1. I Visual Studio öppnar du fliken för `MainWindow.xaml.cs`.
2. Lägg till den här koden i projektet:
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Metoden `GetLanguagesForTranslate()` skapar en HTTP GET-begäran och använder frågesträngsparametern `scope=translation` för att begränsa omfånget för begäran till språk som stöds för översättning. Huvudet `Accept-Language` med värdet `en` läggs till så att språken som stöds returneras på engelska.

JSON-svaret parsas och konverteras till en ordlista. Sedan läggs språkkoderna till i medlemsvariabeln `languageCodes`. Nyckel/värde-paren som innehåller språkkoderna och de egna språknamnen loppas igenom och läggs till i medlemsvariabeln `languageCodesAndTitles`. De nedrullningsbara menyerna i formuläret visar de egna namnen, men koderna behövs för att översättningen ska kunna begäras.

## <a name="populate-language-drop-down-menus"></a>Fylla i de nedrullningsbara menyerna med språk

Användargränssnittet definieras med hjälp av XAML, så du behöver inte göra mycket för att konfigurera det utöver att anropa `InitializeComponent()`. Det enda du behöver göra är att lägga till de egna namnen för språk i de nedrullningsbara menyerna **Translate from** (Översätt från) och **Translate to** (Översätt till). Det görs med metoden `PopulateLanguageMenus()`.

1. I Visual Studio öppnar du fliken för `MainWindow.xaml.cs`.
2. Lägg till den här koden i projektet nedanför metoden `GetLanguagesForTranslate()`:
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Den här metoden itererar genom ordlistan `languageCodesAndTitles` och lägger till varje nyckel i båda menyerna. När menyerna har fyllts i anges de standardmässiga från- och till-språken till **Detect** (Identifiera) respektive **English** (engelska).

> [!TIP]
> Utan ett standardval för menyerna kan användaren klicka på **Translate** (Översätt) utan att först välja ett till- eller från-språk. Med standardinställningarna behöver du inte hantera det här problemet.

Nu när `MainWindow` har initierats och användargränssnittet har skapats körs inte koden förrän knappen **Translate** (Översätt) klickas.

## <a name="detect-language-of-source-text"></a>Identifiera språk i källtext

Nu skapar vi en metod för att identifiera språket i källtexten (text som anges i textområdet) med hjälp av Translator Text-API:et. Det värde som returneras av den här begäran används i vår översättningsbegäran senare.

1. I Visual Studio öppnar du fliken för `MainWindow.xaml.cs`.
2. Lägg till den här koden i projektet nedanför metoden `PopulateLanguageMenus()`:
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator Text
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

Den här metoden skapar en HTTP `POST`-begäran till Detect-resursen (Identifiera). Den tar ett argument, `text`, som skickas vidare som brödtext i begäran. När vi senare skapar översättningsbegäran skickas text som anges i användargränssnittet till den här metoden för språkidentifiering.

Dessutom utvärderar den här metoden förtroendepoäng i svaret. Om poängen är större än `0.5` visas det identifierade språket i användargränssnittet.

## <a name="spell-check-the-source-text"></a>Stavningskontrollera källtexten

Nu skapar vi en metod för att stavningskontrollera källtexten med hjälp av API:et för stavningskontroll i Bing. Det här säkerställer att vi får tillbaka korrekta översättningar från Translator Text-API:et. Eventuella ändringar i källtexten skickas vidare i översättningsbegäran när knappen **Translate** (Översätt) klickas.

1. I Visual Studio öppnar du fliken för `MainWindow.xaml.cs`.
2. Lägg till den här koden i projektet nedanför metoden `DetectLanguage()`:

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>Översätt text med klick

Det sista vi behöver göra är att skapa en metod som anropas när knappen **Översätt** klickas i användargränssnittet.

1. I Visual Studio öppnar du fliken för `MainWindow.xaml.cs`.
2. Lägg till den här koden i projektet nedanför metoden `CorrectSpelling()` och spara:  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "app/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

Det första steget är att hämta ”from-” och ”to”-språken samt den text som användaren angav i formuläret. Om källspråket är inställt på **Detect** (Identifiera) anropas `DetectLanguage()` för att fastställa språket i källtexten. Texten kan vara på ett språk som Translator-API:et inte har stöd för. I så fall ska ett meddelande visas för att informera användaren, och programmet ska återgå utan att översätta.

Om källspråket är engelska (oavsett om det anges eller identifieras) kontrollerar du stavningen i texten med `CorrectSpelling()` och rättar eventuella fel. Den korrigerade texten läggs till i textområdet igen så att användaren ser att en korrigering har gjorts.

Koden för att översätta text bör se bekant ut: skapa URI, skapa en begäran, skicka den och parsa svaret. JSON-matrisen kan innehålla fler än ett objekt för översättning, men vår app kräver bara ett.

Efter en lyckad begäran ersätts `TranslatedTextLabel.Content` med `translation`, vilket uppdaterar användargränssnittet för att visa den översatta texten.

## <a name="run-your-wpf-app"></a>köra WPF-appen.

Det var allt – du har en fungerande översättningsapp som skapats med hjälp av WPF. Du kan köra appen genom att klicka på knappen **Starta** i Visual Studio.

## <a name="source-code"></a>Källkod

Källkoden för det här projektet finns på GitHub.

* [Utforska källkoden](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för Microsoft Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
