---
title: Skapa en punkt-till-plats-anslutning till Azure med Azure Virtual WAN | Microsoft Docs
description: I den här självstudien förklarar vi hur du skapar en punkt-till-plats-VPN-anslutning till Azure med Azure Virtual WAN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alzam
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 9fe0c7f7ae0c19833421b647449f0e4100904f5b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767062"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>Självstudier: Skapa en punkt-till-plats-anslutning med Azure Virtual WAN (förhandsversion)

I den här självstudien förklarar vi hur du använder Virtual WAN för att ansluta till dina resurser i Azure via en IPsec/IKE-anslutning (IKEv2) eller en OpenVPN VPN-anslutning. Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en P2S-konfiguration
> * Skapa en hubb
> * Tillämpa P2S-konfigurationen på en hubb
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och tillämpa VPN-klientkonfigurationen
> * Visa virtuellt WAN
> * Visa information om resurshälsa
> * Övervaka en anslutning

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Registrera funktionen

Klicka på **TryIt** för att registrera funktionen enkelt med Azure Cloud Shell. Om du skulle i stället Kör PowerShell lokalt, kontrollera att du har den senaste versionen och logga in med den **Connect AzAccount** och **Välj AzSubscription** kommandon.

>[!NOTE]
>Om du inte registrerar funktionen kan du inte använda den eller se den i portalen.
>
>

När du har klickat på **TryIt** för att öppna Azure Cloud Shell kopierar du och klistrar in följande kommandon:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

När funktionen visas som registrerad, registrera prenumerationen till Microsoft.Network-namnområde.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Skapa ett virtuellt nätverk

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure-portalen (förhandsversion)](https://aka.ms/azurevirtualwanpreviewfeatures) och logga in med ditt Azure-konto.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Skapa en hubb

> [!NOTE]
> Välj inte inställningen ”Include point-to-site gateway” (Ta med gateway för punkt-till-plats) i det här steget.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. Skapa en P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Navigera till **Alla resurser**.
2. Klicka på det virtuella WAN-nätverk du skapade.
3. Under **Virtual WAN architecture** (Virtuell WAN-arkitektur) klickar du på **Point-to-site configurations** (Punkt-till-plats-konfigurationer).
4. Klicka på **+Add point-to-site config** (Lägg till punkt-till-plats-konfiguration) överst på sidan för att öppna sidan **Create new point-to-site configuration** (Skapa ny punkt-till-plats-konfiguration).
5. På sidan **Create new point-to-site configuration** (Skapa ny punkt-till-plats-konfiguration) fyller du i följande fält:

   *  **Konfigurationsnamn** – Det här är det namn som du vill använda för att referera till konfigurationen.
   *  **Tunneltyp** – Protokollet som ska användas för tunneln.
   *  **Adresspool** – Det här är den IP-adresspool som klienterna kommer att tilldelas från.
   *  **Root Certificate Name** (Rotcertifikatnamn) – Ett beskrivande namn för certifikatet.
   *  **Root Certificate Data** (Rotcertifikatdata) – Base-64-kodade X.509-certifikatdata.

6. Klicka på **Skapa** för att skapa konfigurationen.

## <a name="hub"></a>5. Redigera hubbtilldelning

1. Klicka på **Hubbar** på sidan för det virtuella WAN-nätverket.
2. Välj den hubb som du vill tilldela punkt-till-plats-konfigurationen till.
3. Klicka på **”...”** och välj **Redigera virtuell hubb**
4. Markera **Inkludera punkt-till-plats-gateway**.
5. I listrutan väljer du den **Gateway skalningsenheter**.
6. I listrutan väljer du den **punkt-till-plats-konfiguration** som du skapade.
7. Konfigurera den **-adresspoolen** för klienter.
8. Klicka på **Bekräfta**. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="vnet"></a>6. Ansluta ett virtuellt nätverk till en hubb

I det här steget skapar du peeringanslutningen mellan hubben och ett virtuellt nätverk. Upprepa de här stegen för varje virtuellt nätverk du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **Virtuella nätverksanslutningar**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** att lägga till anslutningen.

## <a name="device"></a>7. Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. Klicka på **Hubbar** på sidan för det virtuella WAN-nätverket.
2. Välj den hubb som du vill ladda ned profilen för.
3. Klicka på **”...”** och välj **Ladda ned profil**. 
4. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd profilfilen för att konfigurera punkt-till-plats-klienterna.

## <a name="device"></a>8. Konfigurera punkt-till-plats-klienter
Använd den nedladdade profilen för att konfigurera fjärråtkomstklienterna. Proceduren skiljer sig beroende på operativsystem. Var noga med att följa rätt anvisningar nedan:

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  Ladda ned och installera OpenVPN-klienten från den officiella webbplatsen.
2.  Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfigurationer i Azure-portalen eller New-AzVpnClientConfiguration i PowerShell.
3.  Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i Anteckningar.
4.  Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Här ser du hur du exporterar ett certifikat för att hämta den kodade offentliga nyckeln.
5.  Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Här ser du hur du extraherar den privata nyckeln.
6.  Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7.  Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
8.  Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

#### <a name="ikev2"></a>IKEv2

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. Välj ”VpnClientSetupAmd64”-installationspaketet för en 64-bitars processorarkitektur. Välj ”VpnClientSetupX86”-installationspaketet för en 32-bitars processorarkitektur.
2. Dubbelklicka på paketet för att installera det. Om du ser ett SmartScreen-popup-fönster klickar du på Mer information och sedan på Kör ändå.
3. På klientdatorn går du till Nätverksinställningar och klickar på VPN. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.
4. Innan du försöker ansluta kontrollerar du att du har installerat ett klientcertifikat på klientdatorn. Ett klientcertifikat krävs för autentisering när du använder den interna Azure-certifikatautentiseringstypen. Mer information om hur du genererar certifikat finns i Generera certifikat. Information om hur du installerar ett klientcertifikat finns i Installera ett klientcertifikat.

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  Ladda ned och installera en OpenVPN-klient, till exempel TunnelBlik från https://tunnelblick.net/downloads.html 
2.  Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken för punkt-till-plats-konfiguration i Azure-portalen eller New-AzVpnClientConfiguration i PowerShell.
3.  Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i Anteckningar.
4.  Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Här ser du hur du exporterar ett certifikat för att hämta den kodade offentliga nyckeln.
5.  Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Här ser du hur du extraherar den privata nyckeln.
6.  Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7.  Dubbelklicka på profilfilen för att skapa profilen i tunnelblik
8.  Starta Tunnelblik från programmappen
9.  Klicka på Tunneblik-ikonen i systemfältet och välj Anslut

#### <a name="ikev2"></a>IKEv2

Azure tillhandahåller inte mobileconfig-filen för intern Azure-certifikatautentisering. Du måste konfigurera den inbyggda IKEv2 VPN-klienten manuellt på varje Mac som ska ansluta till Azure. Mappen Generic innehåller all information som du behöver för att konfigurera den. Om du inte ser mappen Generic i nedladdningen beror det antagligen på att du inte valde IKEv2 som tunneltyp. När du har valt IKEv2 genererar du ZIP-filen igen för att hämta mappen Generic. Mappen Generic innehåller följande filer:

- VpnSettings.xml som innehåller viktiga inställningar som serveradress och tunneltyp.
- VpnServerRoot.cer som innehåller rotcertifikatet som krävs för att validera Azure VPN Gateway under P2S-anslutningskonfigurationen.

## <a name="viewwan"></a>9. Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb. För muspekaren över en punkt så visas en sammanfattning av hubbens hälsotillstånd.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="viewhealth"></a>10. Visa resurshälsa

1. Navigera till ditt WAN.
2. Öppna WAN-sidan. I avsnittet **SUPPORT + felsökning** klickar du på **Hälsa** och visar resursen.

## <a name="connectmon"></a>11. Övervaka en anslutning

Skapa en anslutning för att övervaka kommunikation mellan en virtuell Azure-dator och en fjärrplats. Information om hur du ställer in en anslutningsövervakare finns i dokumentationen om att [övervaka nätverkskommunikation](~/articles/network-watcher/connection-monitor.md). Källfältet är IP för den virtuella datorn i Azure och mål-IP är plats-IP-adressen.

## <a name="cleanup"></a>12. Rensa resurser

När du inte längre behöver dessa resurser kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) att ta bort resursgruppen och alla resurser den innehåller. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en plats
> * Skapa en hubb
> * Ansluta en hubb till en plats
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och applicera VPN-enhetskonfigurationen
> * Visa virtuellt WAN
> * Visa information om resurshälsa
> * Övervaka en anslutning

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
