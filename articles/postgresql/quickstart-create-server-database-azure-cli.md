---
title: Snabbstart – skapa en Azure Database för PostgreSQL – enskild Server med Azure CLI
description: Snabbstartsguide för att skapa och hantera Azure Database för PostgreSQL – enskild Server med Azure CLI (kommandoradsgränssnitt).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc
ms.openlocfilehash: d8e5ddf0820c789150f264aa4f7d6bd291adb3af
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443135"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Snabbstart: Skapa en Azure Database för PostgreSQL – enskild Server med Azure CLI

> [!TIP]
> Överväg att använda den enklare [az postgres upp](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-kommando (för närvarande i förhandsversion). Prova att använda den [snabbstarten](./quickstart-create-server-up-azure-cli.md).

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL-server i en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) med hjälp av Azure-CLI:n.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

Om du kör CLI lokalt, måste du logga in på ditt konto med hjälp av den [az-inloggning](/cli/azure/authenticate-azure-cli?view=interactive-log-in) kommando. Obs den **ID** egenskap från kommandoutdata för det motsvarande prenumerationsnamnet.
```azurecli-interactive
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt den **ID** egenskap från den **az-inloggning** utdata för din prenumeration i platshållaren för prenumerations-ID: T.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [az group create](/cli/azure/group). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Skapa en [Azure Database för PostgreSQL-server](overview.md) med kommandot [az postgres server create](/cli/azure/postgres/server). En server kan innehålla flera databaser.


**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Välj ett unikt namn för Azure Database för PostgreSQL-server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Se under tabellen för mer information om parametern sku-name.
backup-retention | 7 | Hur länge en säkerhetskopia ska behållas. Enheten är dagar. Intervallet är 7–35. 
geo-redundant-backup | Inaktiverad | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte. Tillåtna värden: Aktiverad, inaktiverad.
location | westus | Azure-platsen för servern.
ssl-enforcement | Enabled | Om ssl ska aktiveras eller inte för den här servern. Tillåtna värden: Aktiverad, inaktiverad.
storage-size | 51200 | Serverns lagringskapacitet (enheten är megabyte). En giltig storage-size är minst 5 120 MB och ökar i steg om 1 024 MB. Se dokumentet med [prisnivåer](./concepts-pricing-tiers.md) för mer information om storleksgränser för lagring. 
version | 9.6 | Huvudversion för PostgreSQL.
admin-user | myadmin | Användarnamnet för administratörsinloggning. Det kan inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.
admin-password | *säkert lösenord* | Lösenordet för administratörsanvändaren. Det måste innehålla mellan 8 och 128 tecken. Ditt lösenord måste innehålla tecken från tre av följande kategorier: Engelska versaler, engelska gemener, siffror och icke-alfanumeriska tecken.


Parametervärdet för sku-namn följer namngivningskonventionen {prisnivå}\_{compute-generering}\_{vCores} som i exemplen nedan:
+ `--sku-name B_Gen5_1` mappar till Basic, Gen 5 och 1 virtuell kärna. Det här alternativet är minsta tillgängliga SKU.
+ `--sku-name GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
+ `--sku-name MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 vCores.

Se dokumentationen om [prisnivåer](./concepts-pricing-tiers.md) för mer information om giltiga värden per region och nivå.

I följande exempel skapas en Postgre SQL 9.6-server i västra USA som heter `mydemoserver` i din resursgrupp `myresourcegroup` med serveradministratörsinloggningen `myadmin`. Det här är **4:e generationens** server för **generell användning** med 2 **virtuella kärnor**. Ersätt `<server_admin_password>` med ditt eget värde.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!NOTE]
> Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapas på prisnivån Basic inte senare kan skalas till Generell användning eller Minnesoptimerad. Mer information finns på [sidan med prissättning](https://azure.microsoft.com/pricing/details/postgresql/).
> 

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en brandväggsregel på Azure PostgreSQL-servernivå med kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). En brandväggsregel på servernivå låter ett externt program som en [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) eller en [PgAdmin](https://www.pgadmin.org/) ansluta till din server via Azure PostgreSQL-tjänstens brandvägg. 

Du kan ställa in en brandväggsregel som omfattar ett IP-intervall för att kunna ansluta från ditt nätverk. Följande exempel använder [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) för att skapa en brandväggsregel `AllowMyIP` för en enkel IP-adress.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure PostgreSQL-servern kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be din IT-avdelning öppna port 5432 för att ansluta till din Azure PostgreSQL-server.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **administratorLogin** och **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Anslut till PostgreSQL-databasen med psql

Om din klientdator har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/current/static/app-psql.html) för att ansluta till en Azure PostgreSQL-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database för PostgreSQL-server
   ```bash
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Följande kommando ansluter till exempel till standarddatabasen som heter **postgres** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Om du vill använda en URL-sökväg för att ansluta till Postgres URL koda den @-tecknet användarnamn med `%40`. Exempel är anslutningssträngen för psql,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


2. När du är ansluten till servern, skapar du en blank databas i prompten.
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. I prompten, kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Ansluta till PostgreSQL-servern med hjälp av pgAdmin

pgAdmin är ett verktyg med öppen källkod som används med PostgreSQL. Du kan installera pgAdmin från [pgAdmin-webbplatsen](https://www.pgadmin.org/). Den pgAdmin-version som du använder kan skilja sig från vad som används i den här snabbstarten. Läs dokumentationen för pgAdmin om du behöver ytterligare hjälp.

1. Öppna pgAdmin-programmet på klientdatorn.

2. Från verktygsfältet går du till **Objekt**, hovrar över **Skapa** och väljer **Server**.

3. I dialogrutan **Skapa – Server** på fliken **Allmänt** anger du ett unikt eget namn för servern, t.ex. **mydemoserver**.

   ![Fliken ”Allmänt”](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. Fyll i inställningstabellen i dialogrutan **Skapa – Server** på fliken **Anslutning**.

   ![Fliken ”Anslutning”](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    pgAdmin-parameter |Värde|Beskrivning
    ---|---|---
    Värdnamn/-adress | servernamn | Det värde för servernamn som du använde tidigare när du skapade Azure Database för PostgreSQL-server. Vår exempelserver är **mydemoserver.postgres.database.azure.com.** Använd det fullständiga domännamnet ( **\*.postgres.database.azure.com**) som i det här exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. 
    Port | 5432 | Porten som ska användas när du ansluter till Azure Database för PostgreSQL-servern. 
    Underhållsdatabas | *postgres* | Systemgenererat standardnamn för databasen.
    Användarnamn | Inloggningsnamn för serveradministratör | Ange det användarnamn för serveradministratörsinloggning som du angav tidigare när du skapade Azure Database för PostgreSQL-server. Om du inte kommer ihåg användarnamnet följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. Formatet är *användarnamn\@servername*.
    Lösenord | Ditt administratörslösenord | Det lösenord du angav när du skapade servern tidigare i den här snabbstarten.
    Role | Lämna tomt | Du behöver inte ange ett rollnamn nu. Lämna fältet tomt.
    SSL-läge | *Kräv* | Du kan ställa in SSL-läge på SSL-fliken i pgAdmin. Som standard skapas alla Azure Database for PostgreSQL-servrar med tvingande SSL aktiverat. Om du vill inaktivera tvingande SSL kan läsa informationen om [tvingande SSL](./concepts-ssl-connection-security.md).
    
5. Välj **Spara**.

6. I fönstret **Webbläsare** till vänster expanderar du noden **Servrar**. Välj din server, till exempel **mydemoserver**. Klicka för att ansluta till den.

7. Expandera servernoden och expandera sedan **Databaser** under den. Listan bör innehålla din befintliga *postgres*-databas och eventuella andra databaser som du har skapat. Du kan skapa flera databaser per server med Azure Database for PostgreSQL.

8. Högerklicka på **Databaser**, välj menyn **Skapa** och välj sedan **Databas**.

9. Ange valfritt databasnamn i fältet **Databas**, till exempel **mypgsqldb2**.

10. Välj **Ägare** för databasen från den nedrullningsbara listan. Välj ditt inloggningsnamn som serveradministratör, förslagsvis som i vårt exempel: **my admin**.

    ![Skapa en databas i pgAdmin](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Välj **Spara** för att skapa en tom databas.

12. I fönstret **Webbläsare** ser du den databas som du skapade i listan med databaser under servernamnet.




## <a name="clean-up-resources"></a>Rensa resurser

Rensa upp alla resurser du skapade i snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande snabbstarter ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta, kan du använda följande steg för att ta bort alla resurser som skapades av den här snabbstarten i Azure-CLI:n.

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du endast vill ta bort den nyss skapade servern kan du köra kommandot [az postgres server delete](/cli/azure/postgres/server).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)

