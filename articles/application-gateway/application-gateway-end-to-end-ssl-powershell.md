---
title: Konfigurera SSL för slutpunkt till slutpunkt med Azure Application Gateway
description: Den här artikeln beskrivs hur du konfigurerar SSL för slutpunkt till slutpunkt med Azure Application Gateway med hjälp av PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: d9851f6b3e32d0c7ab0d7774458ba5bc4d9ba823
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66729681"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurera SSL från slutpunkt till slutpunkt med hjälp av Application Gateway med PowerShell

## <a name="overview"></a>Översikt

Azure Application Gateway stöder slutpunkt till slutpunkt-kryptering av trafik. Application Gateway avbryter SSL-anslutningen vid application gateway. Gatewayen sedan tillämper routingregler till trafiken, återkrypterar paketet och vidarebefordrar paketet till rätt backend-server baserat på de routningsregler som definierats. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren.

Application Gateway stöder definiera anpassade SSL-alternativ. Det stöder också inaktivera följande protokollversioner: **TLSv1.0**, **TLSv1.1**, och **TLSv1.2**och definiera vilka krypteringssviter som du använder och i prioritetsordning efter. Om du vill veta mer om konfigurerbara alternativ som SSL kan se den [översikt över SSL-princip](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 och SSL 3.0 är inaktiverade som standard och kan inte aktiveras. De anses vara osäkra och kan inte användas med Programgatewayen.

![scenario-bild][scenario]

## <a name="scenario"></a>Scenario

I det här scenariot kan du lära dig hur du skapar en Programgateway med hjälp av SSL för slutpunkt till slutpunkt med PowerShell.

Det här scenariot kommer att:

* Skapa en resursgrupp med namnet **appgw-rg**.
* Skapa ett virtuellt nätverk med namnet **appgwvnet** med ett adressutrymme för **10.0.0.0/16**.
* Skapa två undernät som kallas **appgwsubnet** och **appsubnet**.
* Skapa ett litet program gateway stödjande slutpunkt till slutpunkt SSL-kryptering som gränser SSL-protokollsversioner och krypteringssviter.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill konfigurera SSL för slutpunkt till slutpunkt med application gateway, krävs ett certifikat för gateway och certifikat krävs för backend servrarna. Gateway-certifikatet används för att härleda en symmetrisk nyckel enligt specifikationen för SSL-protokollet. Den symmetriska nyckeln används sedan kryptera och dekryptera trafiken som skickas till gatewayen. Gateway-certifikatet måste ha formatet Personal Information Exchange (PFX). Det här filformatet kan du exportera den privata nyckeln som krävs av application gateway för att utföra kryptering och dekryptering av trafik.

För slutpunkt till slutpunkt SSL-kryptering måste på serversidan uttryckligen tillåts av application gateway. Ladda upp det offentliga certifikatet backend-servrar till application gateway. Lägger till certifikatet säkerställer att den application gatewayen kommunicerar bara med kända serverdelsinstanser. Detta skyddar ytterligare slutpunkt till slutpunkt-kommunikation.

Konfigurationsprocessen beskrivs i följande avsnitt.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Det här avsnittet beskriver hur du skapar en resursgrupp som innehåller application gateway.

1. Logga in på ditt Azure-konto.

   ```powershell
   Connect-AzAccount
   ```

2. Välj prenumerationen som ska användas för det här scenariot.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Skapa en resursgrupp. (Hoppa över det här steget om du använder en befintlig resursgrupp.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

I följande exempel skapas ett virtuellt nätverk och två undernät. Ett undernät används för application gateway. Andra undernätet används för de servrar som är värdar för webbprogrammet.

1. Tilldela ett adressintervall för undernätet som ska användas för application gateway.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Undernät som konfigurerats för en Programgateway bör ändras korrekt. En application gateway kan konfigureras för upp till 10 instanser. Varje instans använder en IP-adress från undernätet. För liten för ett undernät som kan påverkas negativt och skala ut en Programgateway.
   >

2. Tilldela ett adressintervall som ska användas för backend-adresspoolen.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Skapa ett virtuellt nätverk med undernät som definierats i föregående steg.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Hämta resurs för virtuella nätverk och undernätverksresurser som ska användas i stegen nedan.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs som ska användas för application gateway. Den här offentliga IP-adressen används i någon av de steg som följer.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway stöder inte användning av en offentlig IP-adress som skapats med en definierad domänetiketten. Endast en offentlig IP-adress med en dynamiskt skapade domänetiketten stöds. Om du behöver ett eget DNS-namn för application gateway, rekommenderar vi du använder en CNAME-post som ett alias.

## <a name="create-an-application-gateway-configuration-object"></a>Skapa ett konfigurationsobjekt för programgatewayen

Alla konfigurationsobjekt anges innan du skapar programgatewayen. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

1. Skapa en IP-konfiguration för programgatewayen. Den här inställningen konfigurerar som undernät som application gateway använder. När application gateway startar hämtar den en IP-adress från det konfigurera undernätet och skickar nätverkstrafik till IP-adresserna i backend-IP-adresspool. Tänk på att varje instans använder en IP-adress.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Skapa en frontend-IP-konfigurationen. Den här inställningen motsvarar klientdelen för application gateway en privat eller offentlig IP-adress. Följande steg associerar offentliga IP-adress i föregående steg med IP-konfigurationen.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Konfigurera backend-IP-adresspool med IP-adresserna i backend-webbservrar. De här IP-adreserna är de IP-adresser som tar emot den nätverkstrafik som kommer från frontend-IP-slutpunkten. Ersätt IP-adresserna i exemplet med ditt eget programs IP-adresslutpunkter.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Ett fullständigt kvalificerat domännamn (FQDN) är också ett giltigt värde ska användas i stället för en IP-adress för backend servrarna. Du aktiverar det genom att använda den **- BackendFqdns** växla. 

4. Konfigurera frontend IP-porten för den offentliga IP-slutpunkten. Den här porten är den port som användarna ansluta till.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurera certifikat för application gateway. Det här certifikatet används för att dekryptera och omkryptera trafik på application gateway.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Det här exemplet konfigureras certifikatet som används för SSL-anslutningen. Certifikatet måste vara i PFX-format och lösenordet måste innehålla 4 och 12 tecken.

6. Skapa HTTP-lyssnare för programgatewayen. Tilldela den frontend IP-konfiguration, port och SSL-certifikat som ska användas.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Ladda upp certifikat som ska användas för de resurser som SSL-aktiverad backend-poolen.

   > [!NOTE]
   > Standard-avsökningen hämtar den offentliga nyckeln från den *standard* SSL-bindning på backend-server IP-adress och jämför offentliga nyckelvärde som den tar emot till värdet för offentliga nyckeln du anger här. 
   > 
   > Om du använder värdhuvuden och (Servernamnindikator) på serverdelen, kanske inte platsen till vilken trafikflöden i den offentliga nyckeln hämtades. Om du är osäker, besök https://127.0.0.1/ på backend-servrarna och bekräfta vilket certifikat som används för den *standard* SSL-bindning. Använd den offentliga nyckeln från denna förfrågan i det här avsnittet. Om du använder värdhuvuden och SNI på HTTPS-bindningar och du inte får ett svar och certifikat från en manuell webbläsarbegäran till https://127.0.0.1/ på backend-servrar, måste du ställa in en standard-SSL-bindning med aktiviteterna. Om du inte gör det, avsökningar misslyckas och backend-servern är inte vitlistat.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Certifikatet som angavs i föregående steg ska vara den offentliga nyckeln för .pfx-certifikat finns på serverdelen. Exportera certifikat (inte rotcertifikatet) installerat på backend-servern i anspråk, bevis och skäl till (CER)-format och använda den i det här steget. Det här steget vitlistor serverdelen med application gateway.

   Om du använder Application Gateway v2-SKU, skapar du ett betrott rotcertifikat i stället för ett certifikat för serverautentisering. Mer information finns i [översikt över slutpunkt till slutpunkt-SSL med Programgateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurera HTTP-inställningar för application gateway för backend-servern. Tilldela certifikatet laddades upp i det föregående steget för att HTTP-inställningarna.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Använd följande kommando för SKU: N för Application Gateway v2:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Skapa en hanteringsregel för belastningsutjämnaren som konfigurerar belastningsutjämnarens beteende. I det här exemplet skapas en grundläggande resursallokeringsregel.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurera programgatewayens instansstorlek. Tillgängliga storlekar är **Standard\_små**, **Standard\_medel**, och **Standard\_stor**.  För kapacitet, tillgängliga värden är **1** via **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Du kan välja ett instansantal på 1 för testning. Det är viktigt att veta att alla instansantalet under två instanser omfattas inte av serviceavtalet och rekommenderas därför inte. Det är små gateways som ska användas för utveckling, testning och inte för produktion.

11. Konfigurera SSL-princip som ska användas på application gateway. Application Gateway stöder möjligheten att ange en lägsta version för SSL-protokollsversioner.

    Följande värden är en lista över protokollversioner som kan definieras:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    I följande exempel anger den minsta Protokollversionen till **TLSv1_2** och möjliggör **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, och **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** endast.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Med alla steg ovan kan skapa programgatewayen. Skapandet av gatewayen är en process som tar lång tid att köra.

```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Tillämpa ett nytt certifikat om backend-certifikatet har upphört att gälla

Använd den här proceduren för att tillämpa ett nytt certifikat om backend-certifikatet har upphört att gälla.

1. Hämta application-gateway för att uppdatera.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Lägg till den nya certifikatresursen från den .cer-fil som innehåller den offentliga nyckeln för certifikatet och kan också vara samma certifikat läggs till lyssnaren för SSL-avslutning på application gateway.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Hämta det nya certifikat-objektet för autentisering i en variabel (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Tilldela det nya certifikatet till den **BackendHttp** inställningen och referera till variabeln $AuthCert. (Ange namn för HTTP-inställning som du vill ändra.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Spara ändringen i application gateway och skicka den nya konfigurationen som ingår i $out-variabel.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Ta bort ett oanvända utgånget certifikat från HTTP-inställningar

Ta bort ett oanvända utgånget certifikat från HTTP-inställningar med hjälp av den här proceduren.

1. Hämta application-gateway för att uppdatera.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Ange namnen på certifikat för serverautentisering som du vill ta bort.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Ta bort certifikat för serverautentisering från en Programgateway.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Bekräfta ändringen.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Begränsa SSL-protokollsversioner på en befintlig Programgateway

Föregående steg tog du skapar ett program med slutpunkt-till-slutpunkt SSL och inaktiverar vissa SSL-protokollsversioner. I följande exempel inaktiverar vissa SSL-principer på en befintlig application gateway.

1. Hämta application-gateway för att uppdatera.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definiera en SSL-princip. I följande exempel **TLSv1.0** och **TLSv1.1** är inaktiverad och de krypteringssviter som **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, och **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** är den enda de som tillåts.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Slutligen uppdaterar du gatewayen. Det sista steget är en tidskrävande uppgift. När den är klar konfigureras slutpunkt till slutpunkt SSL på application gateway.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett application gateway DNS-namn

När gatewayen har skapats är nästa steg att konfigurera klientprogrammet för kommunikation. Application Gateway kräver ett dynamiskt tilldelat DNS-namn när du använder en offentlig IP-adress som inte är användarvänligt. För att säkerställa att slutanvändare kan nå programgatewayen, kan du använda en CNAME-post så att den pekar på den offentliga slutpunkten för programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn för i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Om du vill konfigurera ett alias, hämta information om programgatewayen och dess associerade IP/DNS-namn med hjälp av den **PublicIPAddress** elementet kopplat till programgatewayen. Använda DNS-namn för application gateway för att skapa en CNAME-post som pekar på två webbapparna till detta DNS-namn. Vi inte rekommenderar användning av A-poster, eftersom VIP kan ändra på omstart av programgatewayen.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Nästa steg

Mer information om Härdning av säkerheten för dina webbprogram med Brandvägg för webbaserade program via Application Gateway finns i den [översikt över Web application firewall](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
