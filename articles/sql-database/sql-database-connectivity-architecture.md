---
title: Azure SQL Database och SQL Data Warehouse Anslutningsarkitektur | Microsoft Docs
description: Det här dokumentet beskriver arkitekturen för Azure SQL-anslutning för databasanslutningar från Azure eller från utanför Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
manager: craigg
ms.date: 07/02/2019
ms.openlocfilehash: 8441e64981b7157e91a56124a08c0aa02a9b1db0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537927"
---
# <a name="azure-sql-connectivity-architecture"></a>Arkitektur för Azure SQL-anslutning

Den här artikeln förklarar Azure SQL Database och SQL Data Warehouse-anslutning arkitekturen samt hur de olika komponenterna fungerar därigenom dirigera trafik till din Azure SQL-instans. Dessa komponenter-funktion för anslutningen att dirigera nätverkstrafik till Azure SQL Database eller SQL Data Warehouse med klienter som ansluter från i Azure och klienter som ansluter från utanför Azure. Den här artikeln innehåller också skriptexempel för att ändra hur anslutning sker, samt den information som rör ändra standardinställningar för anslutningen.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt över arkitekturen för Azure SQL Database-anslutning.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Följande steg beskriver hur upprättas en anslutning till en Azure SQL database:

- Klienterna ansluter till den gateway som har en offentlig IP-adress och lyssnar på port 1433.
- Gatewayen, beroende på effektiva anslutningsprincip, omdirigeringar eller proxyservrar trafiken till rätt databas-klustret.
- Inne i databasen vidarebefordras kluster-trafik till lämplig Azure SQL-databasen.

## <a name="connection-policy"></a>Anslutningsprincip för

Azure SQL Database stöder följande tre alternativ för den här inställningen för anslutning av en SQL Database-server:

- **Omdirigering (rekommenderas):** Klienter ansluta direkt till den nod som värd för databasen. Om du vill aktivera anslutningen klienter måste tillåta utgående brandväggsregler till alla Azure-IP-adresser i regionen med Nätverkssäkerhetsgrupper (NSG) med [tjänsttaggar](../virtual-network/security-overview.md#service-tags)) för portar 11000 till 11999, inte bara Azure SQL Database gateway-IP adresser på port 1433. Eftersom paket går direkt till databasen, har svarstid och dataflöde bättre prestanda.
- **Proxy:** I det här läget är alla anslutningar via proxy via Azure SQL Database-gatewayer. Om du vill aktivera anslutning, måste klienten ha utgående brandväggsregler som tillåter endast Azure SQL Database-gateway IP-adresser (vanligtvis två IP-adresser per region). Om du väljer det här läget kan resultera i högre svarstider och lägre dataflöde, beroende på typen av arbetsbelastning. Vi rekommenderar starkt att den `Redirect` anslutningsprincip över den `Proxy` anslutningsprincip för lägsta svarstid och högsta dataflöde.
- **standard:** Detta tillämpas principen på alla servrar när du har skapat, såvida inte du uttryckligen ändrar principen till antingen `Proxy` eller `Redirect`. Principen som beror på om anslutningar kommer från i Azure (`Redirect`) eller utanför Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Anslutningen från i Azure

Om du ansluter från inom Azure dina anslutningar har en princip för `Redirect` som standard. En princip av `Redirect` innebär att när TCP-sessionen har upprättats till Azure SQL-databasen, klientsessionen sedan omdirigeras till rätt databas-kluster med en ändring av den virtuella mål-IP från som Azure SQL Database-gateway med den kluster. Alla efterföljande paket som flödar därefter direkt till klustret, vilket kringgår Azure SQL Database-gateway. Följande diagram illustrerar det här flödet i nätverkstrafiken.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutningen från utanför Azure

Om du ansluter från platser utanför Azure, dina anslutningar har en princip för `Proxy` som standard. En princip av `Proxy` innebär att TCP-sessionen har upprättats via gatewayen för Azure SQL-databasen och alla efterföljande paket som flödar via gatewayen. Följande diagram illustrerar det här flödet i nätverkstrafiken.

![Översikt över arkitekturen](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database gateway IP-adresser

Tabellen nedan visar en lista över IP-adresser för gateway per region. Om du vill ansluta till en Azure SQL Database, måste du tillåta nätverkstrafik till och från **alla** gatewayer för regionen.

Framöver kommer vi lägga till flera Gateways i varje region och dra tillbaka gatewayer i kolumnen ur Gateway-IP-adress i tabellen nedan. Mer information om hur du inaktiverar processen som anges i följande artikel: [Azure SQL Database trafik migrering till nyare Gateways](sql-database-gateway-migration.md)


| Regionsnamn          | Gatewayens IP-adress | Inaktiverade Gateway </br> IP-adress| Anmärkningar om inaktivera | 
| --- | --- | --- | --- |
| Östra Australien       | 13.75.149.87, 40.79.161.1 | | |
| Sydöstra Australien | 191.239.192.109, 13.73.109.251 | | |
| Södra Brasilien         | 104.41.11.5        |                 | |
| Centrala Kanada       | 40.85.224.249      |                 | |
| Östra Kanada          | 40.86.226.166      |                 | |
| Centrala USA           | 13.67.215.62, 52.182.137.15 | 23.99.160.139 | Inga anslutningar efter den 1 September 2019 |
| Östra Kina 1         | 139.219.130.35     |                 | |
| Kina, östra 2         | 40.73.82.1         |                 | |
| Norra Kina 1        | 139.219.15.17      |                 | |
| Kina, norra 2        | 40.73.50.0         |                 | |
| Östasien            | 191.234.2.139, 52.175.33.150 |       | |
| Östra USA 1            | 40.121.158.30, 40.79.153.12 | 191.238.6.43 | Inga anslutningar efter den 1 September 2019 |
| USA, östra 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0 | 191.239.224.107    | Inga anslutningar efter den 1 September 2019 |
| Frankrike, centrala       | 40.79.137.0, 40.79.129.1 |           | |
| Centrala Tyskland      | 51.4.144.100       |                 | |
| Nordöstra Tyskland   | 51.5.144.179       |                 | |
| Centrala Indien        | 104.211.96.159     |                 | |
| Södra Indien          | 104.211.224.146    |                 | |
| Västra Indien           | 104.211.160.80     |                 | |
| Östra Japan           | 13.78.61.196, 40.79.184.8, 13.78.106.224 | 191.237.240.43 | Inga anslutningar efter den 1 September 2019 |
| Västra Japan           | 104.214.148.156, 40.74.100.192 | 191.238.68.11 | Inga anslutningar efter den 1 September 2019 |
| Sydkorea, centrala        | 52.231.32.42       |                 | |
| Sydkorea, södra          | 52.231.200.86      |                 | |
| Norra centrala USA     | 23.96.178.199      | 23.98.55.75     | Inga anslutningar efter den 1 September 2019 |
| Norra Europa         | 40.113.93.91       | 191.235.193.75  | Inga anslutningar efter den 1 September 2019 |
| Södra centrala USA     | 13.66.62.124       | 23.98.162.75    | Inga anslutningar efter den 1 September 2019 |
| Sydostasien      | 104.43.15.0        | 23.100.117.95   | Inga anslutningar efter den 1 September 2019 |
| Storbritannien, södra             | 51.140.184.11      |                 | |
| Storbritannien, västra              | 51.141.8.11        |                 | |
| Västra centrala USA      | 13.78.145.25       |                 | |
| Västra Europa          | 191.237.232.75, 40.68.37.158 |       | |
| Västra USA 1            | 23.99.34.75, 104.42.238.205 |        | |
| Västra USA 2            | 13.66.226.202      |                 | |
|                      |                    |                 | |

## <a name="change-azure-sql-database-connection-policy"></a>Ändra principen för Azure SQL Database-anslutning

Du kan ändra principen för Azure SQL Database för en Azure SQL Database-server med den [an-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) kommando.

- Om din princip har angetts till `Proxy`, alla nätverksanslutningar paket flow via Azure SQL Database-gateway. Du måste tillåta utgående trafik till endast Azure SQL Database gateway-IP för den här inställningen. Med hjälp av en inställning av `Proxy` har flera svarstider än inställningen `Redirect`.
- Om din anslutning principinställningen `Redirect`, alla nätverksanslutningar paket flow direkt till databasen-klustret. Du måste tillåta utgående trafik till flera IP-adresser för den här inställningen.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript för att ändra anslutningsinställningar via PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska. Följande skript kräver den [Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du ändrar principen.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript för att ändra anslutningsinställningar via Azure CLI

> [!IMPORTANT]
> Det här skriptet kräver den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i bash-gränssnittet

> [!IMPORTANT]
> Det här skriptet kräver den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Följande CLI-skript visar hur du ändrar principen i ett bash-gränssnitt.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI från Kommandotolken för Windows

> [!IMPORTANT]
> Det här skriptet kräver den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Följande CLI-skript visar hur du ändrar principen från en Windows kommandotolk (med Azure CLI installerat).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar principen för Azure SQL Database för en Azure SQL Database-server finns i [an-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Information om Azure SQL Database-anslutningsbeteendet för klienter som använder ADO.NET 4.5 eller senare finns i [portar utöver 1433 för ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Allmän utveckling översiktlig information finns i [översikt över SQL Database Application Development](sql-database-develop-overview.md).