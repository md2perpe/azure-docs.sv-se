---
title: Azure API Management-mall datamodelldata referens | Microsoft Docs
description: Lär dig om garantier för entiteten och typ för vanliga objekt som används i datamodellerna för utvecklarportalsmallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 3c2384b536235554fed7c1cf1a08b7c665f513a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61094530"
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management-mall referens för datamodell
Det här avsnittet beskriver entiteten och Skriv garantier för vanliga objekt som används i datamodellerna för utvecklarportalsmallar i Azure API Management.  
  
 Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

Developer-portalen är inte tillgänglig på nivån för förbrukning.

## <a name="reference"></a>Referens

-   [API](#API)  
-   [API-sammanfattning](#APISummary)  
-   [Programmet](#Application)  
-   [Bifogad fil](#Attachment)  
-   [Kodexempel](#Sample)  
-   [Kommentar](#Comment)  
-   [Filtrering](#Filtering)  
-   [Header](#Header)  
-   [HTTP-begäran](#HTTPRequest)  
-   [HTTP-svar](#HTTPResponse)  
-   [Problemet](#Issue)  
-   [Åtgärd](#Operation)  
-   [Åtgärd-menyn](#Menu)  
-   [Menyalternativet för åtgärden](#MenuItem)  
-   [Växling](#Paging)  
-   [Parametern](#Parameter)  
-   [Produkten](#Product)  
-   [Leverantör](#Provider)  
-   [Representation](#Representation)  
-   [Prenumeration](#Subscription)  
-   [Prenumerationsöversikt](#SubscriptionSummary)  
-   [Information om användarkonto](#UserAccountInfo)  
-   [Användarinloggning](#UseSignIn)  
-   [Användarregistrering](#UserSignUp)  
  
##  <a name="API"></a> API: ET  
 Den `API` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`id`|string|Resurs-ID. Identifierar API: et i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|`name`|string|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`description`|string|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|`serviceUrl`|string|Absolut URL: en för backend-tjänsten som implementerar det här API: et.|  
|`path`|string|Relativ URL som unikt identifierar den här API: et och alla dess resurssökvägar i API Management-tjänstinstans. Det läggs till API-slutpunkt bas-URL har angetts under tjänstgenereringen för instansen för att skapa en offentlig URL för detta API.|  
|`protocols`|matris med tal|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `1 - http` och `2 - https`, eller båda.|  
|`authenticationSettings`|[Autentiseringsinställningar för auktorisering server](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Samling av autentiseringsinställningar som ingår i den här API: et.|  
|`subscriptionKeyParameterNames`|objekt|Egenskapen som kan användas för att ange anpassade namn för frågan och/eller rubrik parametrar som innehåller prenumerationsnyckeln. Om den här egenskapen finns måste det innehålla minst en av följande två egenskaper.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API-sammanfattning  
 Den `API summary` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`id`|string|Resurs-ID. Identifierar API: et i den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `apis/{id}` där `{id}` är en API-identifierare. Den här egenskapen är skrivskyddad.|  
|`name`|string|Namnet på API: et. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`description`|string|Beskrivning av API: et. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
  
##  <a name="Application"></a> Programmet  
 Den `application` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|string|Den unika identifieraren för programmet.|  
|`Title`|string|Rubrik för programmet.|  
|`Description`|string|Beskrivning av programmet.|  
|`Url`|URI: N|URI för programmet.|  
|`Version`|string|Versionsinformation för programmet.|  
|`Requirements`|string|En beskrivning av kraven för programmet.|  
|`State`|nummer|Det aktuella tillståndet för programmet.<br /><br /> -0 - registrerad<br /><br /> -1 - har skickats<br /><br /> -2 - publicerade<br /><br /> -3 - avvisades<br /><br /> -4 - opublicerade|  
|`RegistrationDate`|DateTime|Datum och tid som programmet har registrerats.|  
|`CategoryId`|nummer|Kategorin för programmet (ekonomi, underhållning, osv.)|  
|`DeveloperId`|string|Den unika identifieraren för utvecklare som skickade programmet.|  
|`Attachments`|Insamling av [bilaga](#Attachment) entiteter.|Eventuella bifogade filer för programmet, till exempel skärmbilder och ikoner.|  
|`Icon`|[Bifogad fil](#Attachment)|Ikonen för den för programmet.|  
  
##  <a name="Attachment"></a> Bifogad fil  
 Den `attachment` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`UniqueId`|string|Den unika identifieraren för den bifogade filen.|  
|`Url`|string|URL till resursen.|  
|`Type`|string|Typ av bifogad fil.|  
|`ContentType`|string|Medietyp för den bifogade filen.|  
  
##  <a name="Sample"></a> Kodexempel  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`title`|string|Namnet på åtgärden.|  
|`snippet`|string|Den här egenskapen är inaktuell och ska inte användas.|  
|`brush`|string|Vilken kod syntaxfärgning mall som ska användas vid visning av kodexemplet. Tillåtna värden är `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, och `csharp`.|  
|`template`|string|Namnet på den här koden exempelmallen.|  
|`body`|string|En platshållare för den kod exempel delen av kodfragmentet.|  
|`method`|string|HTTP-metod för åtgärden.|  
|`scheme`|string|Protokollet som ska användas för begäran igen.|  
|`path`|string|Sökvägen för åtgärden.|  
|`query`|string|Exempel på sökfråga sträng med definierade parametrar.|  
|`host`|string|URL för API Management service-gatewayen för det API som innehåller den här åtgärden.|  
|`headers`|Insamling av [rubrik](#Header) entiteter.|Huvuden för den här åtgärden.|  
|`parameters`|Insamling av [parametern](#Parameter) entiteter.|Parametrar som har definierats för den här åtgärden.|  
  
##  <a name="Comment"></a> Kommentar  
 Den `API` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|nummer|Id för kommentaren.|  
|`CommentText`|string|Kommentarens text. Kan omfatta HTML.|  
|`DeveloperCompany`|string|Företagsnamnet av utvecklaren.|  
|`PostedOn`|DateTime|Datum och tidpunkt kommentaren publicerades.|  
  
##  <a name="Issue"></a> Problemet  
 Den `issue` entitet har följande egenskaper.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|string|Den unika identifieraren för problemet.|  
|`ApiID`|string|Id för API: et för vilken det här problemet har rapporterats.|  
|`Title`|string|Rubrik för problemet.|  
|`Description`|string|Beskrivning av problemet.|  
|`SubscriptionDeveloperName`|string|Förnamn i utvecklarens som rapporterade problemet.|  
|`IssueState`|string|Det aktuella tillståndet för problemet. Möjliga värden är föreslagen, öppnat, stängd.|  
|`ReportedOn`|DateTime|Datum och tid som problemet har rapporterats.|  
|`Comments`|Insamling av [kommentar](#Comment) entiteter.|Kommentarer om det här problemet.|  
|`Attachments`|Insamling av [bilaga](api-management-template-data-model-reference.md#Attachment) entiteter.|Eventuella bifogade filer på problemet.|  
|`Services`|Insamling av [API](#API) entiteter.|API: erna som prenumererar av användaren som skapade problemet.|  
  
##  <a name="Filtering"></a> Filtrering  
 Den `filtering` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Pattern`|string|Den aktuella söktermen; eller `null` om det finns inga sökord.|  
|`Placeholder`|string|Texten som visas i sökrutan när det finns inga sökord som angetts.|  
  
##  <a name="Header"></a> Rubrik  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|-----------------|----------|  
|`name`|string|Parameternamnet.|  
|`description`|string|Beskrivning av parameter.|  
|`value`|string|Huvudets värde.|  
|`typeName`|string|Datatypen för huvudets värde.|  
|`options`|string|Alternativ.|  
|`required`|boolesk|Om rubriken är nödvändig.|  
|`readOnly`|boolesk|Om rubriken är skrivskyddad.|  
  
##  <a name="HTTPRequest"></a> HTTP-begäran  
 Det här avsnittet beskrivs de `request` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`description`|string|Beskrivning för begäran för åtgärden.|  
|`headers`|matris med [rubrik](#Header) entiteter.|Rubriker i begäran.|  
|`parameters`|matris med [Parameter](#Parameter)|Samling av parametrarna som åtgärden.|  
|`representations`|matris med [Representation](#Representation)|Samling av åtgärden begäran garantier.|  
  
##  <a name="HTTPResponse"></a> HTTP-svar  
 Det här avsnittet beskrivs de `response` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`statusCode`|positivt heltal|Åtgärden Svarets statuskod.|  
|`description`|string|Beskrivning av åtgärden svar.|  
|`representations`|matris med [Representation](#Representation)|Samling av åtgärden svar garantier.|  
  
##  <a name="Operation"></a> Åtgärden  
 Den `operation` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`id`|string|Resurs-ID. Identifierar åtgärden inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `apis/{aid}/operations/{id}` där `{aid}` är en API-identifierare och `{id}` är en identifierare för åtgärden. Den här egenskapen är skrivskyddad.|  
|`name`|string|Åtgärdens namn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`description`|string|Beskrivning av åtgärden. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|`scheme`|string|Beskriver på vilket protokoll åtgärder i detta API kan anropas. Tillåtna värden är `http`, `https`, eller båda `http` och `https`.|  
|`uriTemplate`|string|Relativ URL: en mall identifierar målresursen för den här åtgärden. Kan innehålla parametrar. Exempel: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|URL för API Management-gateway som är värd för API: et.|  
|`httpMethod`|string|HTTP-åtgärdsmetod.|  
|`request`|[HTTP-begäran](#HTTPRequest)|En entitet som innehåller information om begäran.|  
|`responses`|matris med [HTTP-svar](#HTTPResponse)|Matris med åtgärden [HTTP-svar](#HTTPResponse) entiteter.|  
  
##  <a name="Menu"></a> Åtgärd-menyn  
 Den `operation menu` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`ApiId`|string|Id för den aktuella API: T.|  
|`CurrentOperationId`|string|Id för den aktuella åtgärden.|  
|`Action`|string|Menytyp.|  
|`MenuItems`|Insamling av [åtgärden menyalternativet](#MenuItem) entiteter.|Åtgärder för den aktuella API: T.|  
  
##  <a name="MenuItem"></a> Menyalternativet för åtgärden  
 Den `operation menu item` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|string|Id för åtgärden.|  
|`Title`|string|Beskrivning av åtgärden.|  
|`HttpMethod`|string|Http-metod för åtgärden.|  
  
##  <a name="Paging"></a> Växling  
 Den `paging` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Page`|nummer|Det aktuella sidnumret.|  
|`PageSize`|nummer|Maximalt antal resultat som ska visas på en enda sida.|  
|`TotalItemCount`|nummer|Antal objekt för visning.|  
|`ShowAll`|boolesk|Om du vill visa alla resultat på en enda sida.|  
|`PageCount`|nummer|Antalet sidor i resultaten.|  
  
##  <a name="Parameter"></a> Parametern  
 Det här avsnittet beskrivs de `parameter` representation.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|-----------------|----------|  
|`name`|string|Parameternamnet.|  
|`description`|string|Beskrivning av parameter.|  
|`value`|string|Parametervärdet.|  
|`options`|strängmatris|Värden som definierats för frågeparametervärden.|  
|`required`|boolesk|Anger om parametern är nödvändig eller inte.|  
|`kind`|nummer|Om den här parametern är en sökvägsparameter (1) eller en frågesträngsparameter (2).|  
|`typeName`|string|Parametertyp.|  
  
##  <a name="Product"></a> Produkten  
 Den `product` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|string|Resurs-ID. Identifierar produkten inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare. Den här egenskapen är skrivskyddad.|  
|`Title`|string|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`Description`|string|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|`Terms`|string|Produkten villkor för användning. Utvecklare som försöker prenumerera på produkten kommer visas och krävs för att acceptera villkoren innan de kan slutföra prenumerationsprocessen.|  
|`ProductState`|nummer|Anger om produkten har publicerats eller inte. Publicerade produkter kan upptäckas av utvecklare på developer-portalen. Icke-publicerade produkter visas bara för administratörer.<br /><br /> Tillåtna värden för Produkttillstånd är:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolesk|Anger om en användare kan ha flera prenumerationer för den här produkten på samma gång.|  
|`MultipleSubscriptionsCount`|nummer|Maximalt antal prenumerationer för produkten som en användare får ha på samma gång.|  
  
##  <a name="Provider"></a> Providern  
 Den `provider` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Properties`|sträng-ordlista|Egenskaper för den här autentiseringsprovider.|  
|`AuthenticationType`|string|Providertyp. (Azure Active Directory, Facebook-inloggning, Google-konto, Account, Twitter).|  
|`Caption`|string|Visningsnamnet för providern.|  
  
##  <a name="Representation"></a> Representation  
 Det här avsnittet beskrivs en `representation`.  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`contentType`|string|Anger en registrerad eller anpassade innehållstyp för den här representation, till exempel `application/xml`.|  
|`sample`|string|Ett exempel på en representation.|  
  
##  <a name="Subscription"></a> Prenumeration  
 Den `subscription` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|string|Resurs-ID. Unikt ID för prenumeration inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|`ProductId`|string|Resurs-ID för produkt för prenumererar på produkten. Värdet är en giltig relativ URL i formatet `products/{pid}` där `{pid}` är en produktidentifierare.|  
|`ProductTitle`|string|Namnet på produkten. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`ProductDescription`|string|Beskrivning av produkten. Får inte vara tomt. Kan innehålla HTML-kod. Maximal längd är 1000 tecken.|  
|`ProductDetailsUrl`|string|Relativ URL till produktinformation.|  
|`state`|string|Tillståndet för prenumerationen. Möjliga tillstånd är:<br /><br /> - `0 - suspended` – prenumerationen blockeras och prenumeranten kan inte anropa alla API: er i produkten.<br /><br /> - `1 - active` – prenumerationen är aktiv.<br /><br /> - `2 - expired` – prenumerationen har nått dess förfallodatum och inaktiverades.<br /><br /> - `3 - submitted` – prenumerationsbegäran har gjorts av utvecklaren, men har ännu inte har godkänts eller avvisats.<br /><br /> - `4 - rejected` – prenumerationsbegäran har nekats av en administratör.<br /><br /> - `5 - cancelled` – prenumerationen har avbrutits av utvecklare eller administratör.|  
|`DisplayName`|string|Visningsnamnet för prenumerationen.|  
|`CreatedDate`|Datum/tid|Det datum då prenumerationen skapades, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolesk|Om prenumerationen kan avbrytas av den aktuella användaren.|  
|`IsAwaitingApproval`|boolesk|Om prenumerationen väntar på godkännande.|  
|`StartDate`|Datum/tid|Startdatum för prenumerationen, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Datum/tid|Utgångsdatumet för prenumerationen, i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Datum/tid|Meddelande-datum för prenumerationen i ISO 8601-format: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|Primär prenumerationsnyckeln. Maximal längd är 256 tecken.|  
|`secondaryKey`|string|Sekundär prenumerationsnyckeln. Maximal längd är 256 tecken.|  
|`CanBeRenewed`|boolesk|Om prenumerationen kan förnyas av den aktuella användaren.|  
|`HasExpired`|boolesk|Om prenumerationen har gått ut.|  
|`IsRejected`|boolesk|Om prenumerationsbegäran nekades.|  
|`CancelUrl`|string|Relativ Url som du säga upp prenumerationen.|  
|`RenewUrl`|string|Relativ Url att förnya prenumerationen.|  
  
##  <a name="SubscriptionSummary"></a> Prenumerationsöversikt  
 Den `subscription summary` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Id`|string|Resurs-ID. Unikt ID för prenumeration inom den aktuella API Management-tjänstinstansen. Värdet är en giltig relativ URL i formatet `subscriptions/{sid}` där `{sid}` är ett prenumerations-ID. Den här egenskapen är skrivskyddad.|  
|`DisplayName`|string|Visningsnamnet för prenumerationen|  
  
##  <a name="UserAccountInfo"></a> Information om användarkonto  
 Den `user account info` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`FirstName`|string|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`LastName`|string|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`Email`|string|E-postadress. Får inte vara tomt och måste vara unikt inom tjänstinstansen. Maximal längd är 254 tecken.|  
|`Password`|string|Lösenordet för användarkontot.|  
|`NameIdentifier`|string|Konto-ID detsamma som användarens e-postadress.|  
|`ProviderName`|string|Providernamn för autentisering.|  
|`IsBasicAccount`|boolesk|TRUE om det här kontot har registrerats med hjälp av e-post och lösenord. FALSKT om konton som har registrerats med hjälp av en provider.|  
  
##  <a name="UseSignIn"></a> Användarinloggning  
 Den `user sign in` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Email`|string|E-postadress. Får inte vara tomt och måste vara unikt inom tjänstinstansen. Maximal längd är 254 tecken.|  
|`Password`|string|Lösenordet för användarkontot.|  
|`ReturnUrl`|string|Webbadressen till sidan där användaren har klickat på Logga in.|  
|`RememberMe`|boolesk|Om du vill spara den aktuella användarens information.|  
|`RegistrationEnabled`|boolesk|Om registrering är aktiverat.|  
|`DelegationEnabled`|boolesk|Om delegerade inloggning är aktiverat.|  
|`DelegationUrl`|string|Delegerad inloggningen url, om aktiverad.|  
|`SsoSignUpUrl`|string|URL: en enkel inloggning för användaren, om sådan finns.|  
|`AuxServiceUrl`|string|Om den aktuella användaren är administratör, är detta en länk till tjänstinstansen i Azure-portalen.|  
|`Providers`|Insamling av [Provider](#Provider) entiteter|Autentiseringsproviders för den här användaren.|  
|`UserRegistrationTerms`|string|Villkor som en användare måste godkänna innan du loggar in.|  
|`UserRegistrationTermsEnabled`|boolesk|Om villkoren är aktiverade.|  
  
##  <a name="UserSignUp"></a> Användarregistrering  
 Den `user sign up` entitet har följande egenskaper:  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolesk|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|`Password`|string|Lösenordet för användarkontot.|  
|`PasswordVerdictLevel`|nummer|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|`UserRegistrationTerms`|string|Villkor som en användare måste godkänna innan du loggar in.|  
|`UserRegistrationTermsOptions`|nummer|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|`ConsentAccepted`|boolesk|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|`Email`|string|E-postadress. Får inte vara tomt och måste vara unikt inom tjänstinstansen. Maximal längd är 254 tecken.|  
|`FirstName`|string|Förnamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`LastName`|string|Efternamn. Får inte vara tomt. Maximal längd är 100 tecken.|  
|`UserData`|string|Värdet som används av den [registrering](api-management-page-controls.md#sign-up) kontroll.|  
|`NameIdentifier`|string|Värdet som används av den [registrering](api-management-page-controls.md#sign-up)registrering kontroll.|  
|`ProviderName`|string|Providernamn för autentisering.|

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).
