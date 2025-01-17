---
title: Hur du konfigurerar OpenVPN klienter för Azure VPN Gateway | Microsoft Docs
description: Steg för att konfigurera OpenVPN klienter för Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: cherylmc
ms.openlocfilehash: b8f1626da730178d2cd9c2f31c4f9876102b3d46
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477846"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurera OpenVPN klienter för Azure VPN Gateway

Den här artikeln hjälper dig att konfigurera **OpenVPN® protokollet** klienter.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kontrollera att du har slutfört stegen för att konfigurera OpenVPN för din VPN-gateway. Mer information finns i [konfigurera OpenVPN för Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Windows-klienter

1. Hämta och installera OpenVPN klienten (version 2.4 eller senare) från officiellt [OpenVPN webbplats](https://openvpn.net/index.php/open-source/downloads.html).
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken för punkt-till-plats-konfiguration i Azure-portalen eller ”New-AzVpnClientConfiguration” i PowerShell.
3. Packa upp profilen. Därefter öppnar den *vpnconfig.ovpn* konfigurationsfilen från mappen OpenVPN med anteckningar.
4. [Exportera](vpn-gateway-certificates-point-to-site.md#clientexport) P2S klientcertifikatet du skapat och överfört till din P2S-konfiguration på gatewayen.
5. Extrahera den privata nyckeln och base64-tumavtrycket från den *.pfx*. Det finns flera sätt att göra detta på. Med hjälp av OpenSSL på din dator är ett sätt. Den *profileinfo.txt* filen innehåller den privata nyckeln och tumavtrycket för CA: N och klientcertifikatet. Glöm inte att använda tumavtrycket för klientcertifikatet.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Öppna *profileinfo.txt* i anteckningar. Om du vill hämta tumavtrycket för klientcertifikat (underordnad), markerar du texten (inklusive och mellan) ”---BEGIN CERTIFICATE---” och ”---END CERTIFICATE---” för underordnat certifikat och kopiera den. Du kan identifiera underordnade certifikatet genom att titta på ämne = / rad.
7. Växla till den *vpnconfig.ovpn* du öppna filen i anteckningar från steg 3. Leta reda på avsnittet som visas nedan och Ersätt allt mellan ”cert” och ”/ certifikat”.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna den *profileinfo.txt* i anteckningar. Om du vill ha den privata nyckeln, markerar du texten (inklusive och mellan) ”---BEGIN privata NYCKELN---” och ”---END privat nyckel---” och kopiera den.
9. Gå tillbaka till vpnconfig.ovpn-filen i anteckningar och hitta det här avsnittet. Klistra in den privata nyckeln som ersätter allt mellan och ”nyckel” och ”/ nyckel”.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
12. Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

## <a name="mac"></a>Mac-klienter

1. Ladda ned och installera en OpenVPN-klient, till exempel [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken punkt-till-plats-konfiguration i Azure-portalen eller genom att använda ”New-AzVpnClientConfiguration” i PowerShell.
3. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från mappen OpenVPN i anteckningar.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Se [exportera den offentliga nyckeln](vpn-gateway-certificates-point-to-site.md#cer) information om hur du exporterar ett certifikat för att få den kodade offentliga nyckeln.
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Se [exportera den privata nyckeln](https://openvpn.net/community-resources/how-to/#pki) information om hur man extraherar en privat nyckel.
6. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7. Dubbelklicka på profilfilen för att skapa profilen i Tunnelblick.
8. Starta Tunnelblick från mappen program.
9. Klicka på ikonen Tunnelblick i systemfältet och Välj Anslut.

> [!IMPORTANT]
>Endast iOS 11.0 och senare och MacOS 10.13 och senare stöds med OpenVPN-protokollet.
>

## <a name="linux"></a>Linux-klienter

1. Öppna en ny terminalsession. Du kan öppna en ny session genom att trycka på ”Ctrl + Alt + t” på samma gång.
2. Ange följande kommando för att installera nödvändiga komponenter:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken för punkt-till-plats-konfiguration i Azure-portalen.
4. [Exportera](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) P2S klientcertifikatet du skapat och överfört till din P2S-konfiguration på gatewayen. 
5. Extrahera den privata nyckeln och base64-tumavtrycket från PFX. Det finns flera sätt att göra detta på. Det är ett sätt att använda OpenSSL på datorn.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Den *profileinfo.txt* filen innehåller den privata nyckeln och tumavtrycket för CA: N och klientcertifikatet. Glöm inte att använda tumavtrycket för klientcertifikatet.

6. Öppna *profileinfo.txt* i en textredigerare. Om du vill hämta tumavtrycket för klientcertifikat (underordnad), markerar du texten inklusive och mellan ”---BEGIN CERTIFICATE---” och ”---END CERTIFICATE---” för underordnat certifikat och kopiera den. Du kan identifiera underordnade certifikatet genom att titta på ämne = / rad.

7. Öppna den *vpnconfig.ovpn* filen och leta reda på avsnittet som visas nedan. Ersätt allt mellan den och ”cert” och ”/ certifikat”.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna profileinfo.txt i en textredigerare. Om du vill ha den privata nyckeln, markerar du texten inklusive och mellan ”---BEGIN privata NYCKELN---” och ”---END privat nyckel---” och kopiera den.

9. Öppna filen vpnconfig.ovpn i en textredigerare och hitta det här avsnittet. Klistra in den privata nyckeln som ersätter allt mellan och ”nyckel” och ”/ nyckel”.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Om du vill ansluta med hjälp av kommandoraden, skriver du följande kommando:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. Gå till inställningar för att ansluta med hjälp av det grafiska Användargränssnittet.
13. Klicka på **+** att lägga till en ny VPN-anslutning.
14. Under **lägga till VPN-** , Välj **importera från fil...**
15. Bläddra till fil för Konfigurationsprofil och dubbelklicka på eller välj **öppna**.
16. Klicka på **Lägg till** på den **lägga till VPN-** fönster.
  
    ![Importera från fil](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Du kan ansluta genom att aktivera VPN **på** på den **nätverksinställningar** sida, eller under nätverksikonen i systemfältet.

## <a name="next-steps"></a>Nästa steg

Om du vill att VPN-klienter ska kunna komma åt resurser i ett annat virtuellt nätverk, följ instruktionerna på den [VNet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) artikeln om du vill konfigurera en vnet-till-vnet-anslutning. Se till att aktivera BGP på gateway och anslutningar, annars kommer inte trafiken.

**”OpenVPN” är ett varumärke som tillhör OpenVPN Inc.**
