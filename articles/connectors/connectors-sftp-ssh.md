---
title: Ansluta till SFTP-server med SSH - Azure Logic Apps
description: Automatisera uppgifter som att övervaka, skapa, hantera, skicka och ta emot filer för en SFTP-server med hjälp av SSH- och Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 7479be6a14c7d1ace5d60defad0eda51d2aa814b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296571"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av SSH- och Azure Logic Apps

Att automatisera uppgifter som att övervaka, skapa, skicka och ta emot filer på en [SFTP Secure File Transfer Protocol ()](https://www.ssh.com/ssh/sftp/) servern med hjälp av den [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) -protokollet, du kan skapa och automatisera integration arbetsflöden med hjälp av Azure Logic Apps och den SFTP-SSH-anslutningen. SFTP är ett nätverksprotokoll som tillhandahåller filåtkomst, filöverföring och filhantering via valfri tillförlitlig dataström. Här följer några exempel på uppgifter du kan automatisera:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, byta namn på, uppdatera, lista, och ta bort filer.
* Skapa mappar.
* Hämta filinnehåll och metadata.
* Extrahera Arkiv till mappar.

Du kan använda utlösare som övervakar händelser på din SFTP-server och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder som utför olika uppgifter på din SFTP-server. Du kan också ha andra åtgärder i din logikapp använda utdata från SFTP-åtgärder. Om du regelbundet hämta filer från din SFTP-server, kan du exempelvis skicka e-postaviseringar om filerna och sitt innehåll med hjälp av anslutningsappen Office 365 Outlook eller Outlook.com-anslutning. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Skillnader mellan den SFTP-SSH-anslutningen och SFTP-anslutningsappen, granska de [jämför SFTP-SSH jämfört med SFTP](#comparison) senare i det här avsnittet.

## <a name="limits"></a>Limits

* Som standard SFTP-SSH åtgärder kan läsa eller skriva filer som är *1 GB eller mindre* men endast i *15 MB* segment i taget. Att hantera filer som är större än 15 MB, SFTP-SSH åtgärder support [meddelande storlekar](../logic-apps/logic-apps-handle-large-messages.md), förutom kopieringsfil-åtgärden, som kan hantera endast 15 MB-filer. Den **hämta filinnehåll** åtgärden använder implicit meddelande storlekar. 

* SFTP-SSH utlösare stöder inte storlekar. När du begär innehåll, utlösare Markera bara de filer som är 15 MB eller mindre. Följ det här mönstret i stället för att hämta filer större än 15 MB:

  * Använda en SFTP-SSH-utlösare som returnerar filegenskaper, till exempel **när en fil läggs till eller ändras (enbart egenskaper)** .

  * Följ utlösare med den SFTP-SSH **hämta filinnehåll** åtgärd, som läser den fullständiga filen och implicit använder meddelande storlekar.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Jämför SFTP-SSH jämfört med SFTP

Här följer andra viktiga skillnader mellan den SFTP-SSH-anslutningen och SFTP-anslutningsappen där den SFTP-SSH-anslutningen har dessa funktioner:

* Använder den [SSH.NET biblioteket](https://github.com/sshnet/SSH.NET), vilket är ett bibliotek med öppen källkod Secure Shell (SSH) som har stöd för .NET.

  > [!NOTE]
  >
  > SFTP-SSH anslutningen-stöder *endast* dessa privata nycklar, format, algoritmer och fingeravtryck:
  >
  > * **Privat nyckel format**: RSA (Rivest Shamir-Adleman) och DSA (Digital Signature Algorithm) nycklar i både OpenSSH och ssh.com format
  > * **Krypteringsalgoritmer**: DES-EDE3-CBC, DES-EDE3-CFB DES-CBC, AES-128-CBC, CBC-AES-192 och AES-256-CBC
  > * **Fingeravtryck**: MD5

* Som standard SFTP-SSH åtgärder kan läsa eller skriva filer som är *1 GB eller mindre* men endast i *15 MB* segment i taget. Att hantera filer som är större än 15 MB, SFTP-SSH åtgärder kan använda [meddelande storlekar](../logic-apps/logic-apps-handle-large-messages.md). Kopieringsfil-åtgärden stöder dock endast 15 MB filer eftersom åtgärden inte stöder meddelande storlekar. SFTP-SSH utlösare stöder inte storlekar.

* Innehåller den **skapa mapp** som skapar en mapp på den angivna sökvägen på SFTP-server.

* Innehåller den **Byt namn på filen** åtgärden, byter namn på en fil på SFTP-servern.

* Cachelagrar anslutningen till SFTP-server *för upp till 1 timme*, vilket förbättrar prestanda och minskar antalet försök att ansluta till servern. Om du vill ange varaktigheten för detta beteende för cachelagring, redigera den [ **ClientAliveInterval** ](https://man.openbsd.org/sshd_config#ClientAliveInterval) -egenskapen i SSH-konfigurationen på din SFTP-server.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Din SFTP-adress och konto autentiseringsuppgifter, som låter din logikapp åtkomst till ditt SFTP-konto. Du måste också åtkomst till en SSH-privata nyckel och SSH-privata nyckel lösenordet.

  > [!IMPORTANT]
  >
  > SFTP-SSH anslutningen-stöder *endast* dessa privata nyckel format, algoritmer och fingeravtryck:
  >
  > * **Privat nyckel format**: RSA (Rivest Shamir-Adleman) och DSA (Digital Signature Algorithm) nycklar i både OpenSSH och ssh.com format
  > * **Krypteringsalgoritmer**: DES-EDE3-CBC, DES-EDE3-CFB DES-CBC, AES-128-CBC, CBC-AES-192 och AES-256-CBC
  > * **Fingeravtryck**: MD5
  >
  > När du skapar din logikapp när du lägger till SFTP-SSH utlösaren eller åtgärden som du vill kan behöver du ange anslutningsinformation för din SFTP-server. 
  > Om du använder en SSH-privata nyckel, se till att du ***kopia*** nyckeln från din SSH fil för privat nyckel och ***klistra in*** nyckeln till anslutningsinformationen, ***inte manuellt ange eller redigera nyckeln***, vilket kan orsaka anslutning att misslyckas. 
  > Mer information finns i senare steg i den här artikeln.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SFTP-konto. Du kommer igång med en SFTP-SSH-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SFTP-SSH-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="how-sftp-ssh-triggers-work"></a>Så här fungerar SFTP-SSH-utlösare

SFTP-SSH utlösare fungerar genom att avsöka SFTP-filsystemet och söker efter alla filer som har ändrats sedan den senaste avsökningen. Vissa verktyg kan du bevara tidsstämpel när filerna som ändras. I dessa fall kan behöva du inaktivera den här funktionen så att utlösaren kan arbeta. Här följer några vanliga inställningar:

| SFTP-klienten | Åtgärd |
|-------------|--------|
| Winscp | Gå till **alternativ** > **inställningar** > **överföra** > **redigera**  >  **Bevara tidsstämpel** > **inaktivera** |
| FileZilla | Gå till **överföra** > **bevara tidsstämplar av överförda filer** > **inaktivera** |
|||

Om en ny fil upptäcks under en utlösare kan utlösaren söker du efter att den nya filen är komplett och inte delvis skriftliga. En fil kan till exempel ha ändringar pågår när utlösaren kontrollerar filservern. För att undvika att returnera en delvis skriftliga fil, noterar utlösaren tidsstämpel för den fil som har de senaste ändringarna, men inte direkt returnerar filen. Utlösaren returnerar filen bara när en avsökning görs servern igen. Det här beteendet kan ibland orsaka en fördröjning som upp till två gånger utlösarens avsökningsintervall.

## <a name="connect-to-sftp-with-ssh"></a>Ansluta till SFTP med SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Tom logic apps i sökrutan anger ”sftp ssh” som filter. Välj utlösaren som du vill under listan över utlösare.

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd, Välj **nytt steg**. I sökrutan anger du ”sftp ssh” som filter. Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. Välj plustecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange informationen som krävs för anslutningen.

   > [!IMPORTANT]
   >
   > När du anger din privata SSH-nyckeln i den **privata SSH-nyckeln** egenskapen, följer du dessa ytterligare steg, som hjälper dig att göra att du anger värdet för fullständig och korrekt för den här egenskapen. 
   > En ogiltig nyckel gör anslutningen misslyckas.

   Men du kan använda valfri textredigerare, är här exempel som visar hur du kopiera och klistra in din nyckel genom att använda Notepad.exe som ett exempel på rätt sätt.

   1. Öppna din SSH-privata nyckelfilen i en textredigerare. De här stegen kan du använda anteckningar som exempel.

   1. På anteckningar **redigera** menyn och välj **Markera alla**.

   1. Välj **redigera** > **kopiera**.

   1. Klistra in i SFTP-SSH-utlösaren eller åtgärden som du har lagt till, den *fullständig* nyckeln du kopierade till den **privata SSH-nyckeln** egenskapen, som har stöd för flera rader.  ***Kontrollera att du klistra in*** nyckeln. ***Inte manuellt ange eller redigera nyckeln***.

1. När du är klar väljer du att ange anslutningsinformationen **skapa**.

1. Nu ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="examples"></a>Exempel

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH utlösa: När en fil läggs till eller ändras

Den här utlösaren startar en logikappens arbetsflöde när en fil läggs till eller ändras på en SFTP-server. Du kan till exempel lägga till ett villkor som kontrollerar dess innehåll och hur du hämtar innehållet baserat på om innehållet uppfyller ett angivet villkor. Du kan sedan lägga till en åtgärd som hämtar filens innehåll och placerar innehållet i en mapp på SFTP-server.

**Enterprise exempel**: Du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-åtgärd som **hämta filinnehåll** så att du hämta orderns innehåll för vidare bearbetning och lagra den ordningen i en order-databas.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH åtgärd: Hämta innehåll med hjälp av sökvägen

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Till exempel kan du lägga till utlösaren från föregående exempel och ett villkor som måste uppfylla dess innehåll. Om villkoret är sant, köra den åtgärd som hämtar innehållet.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/sftpconnector/).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
