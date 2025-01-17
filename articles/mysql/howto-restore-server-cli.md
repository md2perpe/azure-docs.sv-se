---
title: Säkerhetskopiera och återställa en server i Azure Database for MySQL
description: Lär dig hur du säkerhetskopierar och återställer en server i Azure Database för MySQL med hjälp av Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/01/2018
ms.openlocfilehash: f3850623f5918ea9405131edb1821b941019ac34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160447"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Hur du säkerhetskopierar och återställer en server i Azure Database for MySQL med Azure CLI

## <a name="backup-happens-automatically"></a>Säkerhetskopieringen sker automatiskt
Azure Database for MySQL-servrar som säkerhetskopieras regelbundet för att aktivera funktioner för återställning. Med den här funktionen kan du återställa servern och dess databaser till en tidigare point-in-time, på en ny server.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for MySQL-server och databas](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Den här guiden kräver att du använder Azure CLI version 2.0 eller senare. För att bekräfta versionen på Azure CLI-kommandotolk och ange `az --version`. Om du vill installera eller uppgradera, se [installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Konfiguration av säkerhetskopiering

Du gör valet mellan att konfigurera servern för lokalt redundant säkerhetskopieringar eller geografiskt redundanta säkerhetskopieringar på servern har skapats. 

> [!NOTE]
> När en server har skapats kan typ av redundans som den har kan geografiskt redundant eller lokalt redundant inte växlas.
>

När du skapar en server via den `az mysql server create` kommandot, den `--geo-redundant-backup` parametern avgör din redundansalternativ för säkerhetskopiering. Om `Enabled`, geo-redundanta säkerhetskopieringar tas. Eller om `Disabled` lokalt redundant säkerhetskopior tas. 

Kvarhållningsperioden för säkerhetskopior anges av parametern `--backup-retention`. 

Mer information om hur du anger dessa värden under skapa finns i den [Azure Database for MySQL-server CLI Snabbstart](quickstart-create-mysql-server-database-using-azure-cli.md).

Kvarhållningsperiod för en server kan ändras enligt följande:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

I föregående exempel ändras kvarhållningsperioden för säkerhetskopior av mydemoserver till 10 dagar.

Kvarhållningsperioden för säkerhetskopior styr hur långt tillbaka i tiden som en point-in-time-återställning kan hämtas, eftersom den är baserad på säkerhetskopior som är tillgängliga. Point-in-time-återställning är beskrivs mer i nästa avsnitt.

## <a name="server-point-in-time-restore"></a>Server point-in-time-återställning
Du kan återställa servern till en tidigare tidpunkt. Återställda data har kopierats till en ny server och den befintliga servern lämnas skick. Till exempel kan en tabell ignoreras av misstag kl. tolv idag, du återställa tiden innan middagstid. Du kan sedan hämta saknas tabellen och data från den återställda kopian av servern. 

Använd Azure CLI för att återställa servern [az mysql server restore](/cli/azure/mysql/server#az-mysql-server-restore) kommando.

### <a name="run-the-restore-command"></a>Kör kommandot restore

Ange följande kommando om du vill återställa servern, Kommandotolken Azure CLI:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Den `az mysql server restore` kommandot kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där källservern finns.  |
| name | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Markera en punkt i tiden att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd ISO8601-format för datum och tid. Exempelvis kan du använda din egen lokala tidszon som `2018-03-13T05:59:00-08:00`. Du kan också använda UTC Zulu-formatet, till exempel `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

När du återställer en server till en tidigare tidpunkt, skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkt kopieras till den nya servern.

Plats och prisnivåvärden för den återställda servern behåller den ursprungliga servern. 

När återställningen är klar, leta upp den nya servern och kontrollera att data har återställts som förväntat.

## <a name="geo-restore"></a>GEO-återställning
Om du har konfigurerat din server som geografiskt redundanta säkerhetskopieringar, kan en ny server skapas från en säkerhetskopia av den befintliga servern. Den här nya servern kan skapas i valfri region som Azure Database for MySQL är tillgänglig.  

Använda Azure CLI för att skapa en server med en geo-redundant säkerhetskopia, `az mysql server georestore` kommando.

> [!NOTE]
> När en server skapas kanske det inte omedelbart tillgängliga för geo-återställning. Det kan ta några timmar innan den nödvändiga metadata fyllas i.
>

Ange följande kommando för att geo-återställa servern, Kommandotolken Azure CLI:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
Det här kommandot skapar en ny server som kallas *mydemoserver georestored* i USA, östra som hör till *myresourcegroup*. Det är en generell användning, generation 5-server med 8 vCores. Servern skapas från geo-redundant säkerhetskopia av *mydemoserver*, som också är resursgruppen *myresourcegroup*

Om du vill skapa den nya servern i en annan resursgrupp än den befintliga servern sedan i den `--source-server` parametern du kvalificerar servernamnet som i följande exempel:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

Den `az mysql server georestore` kommandot kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
|resource-group| myresourcegroup | Namnet på resursgruppen som den nya servern ska tillhöra.|
|name | mydemoserver georestored | Namnet på den nya servern. |
|source-server | mydemoserver | Namnet på den befintliga servern vars geo-redundanta säkerhetskopieringar används. |
|location | usaöstra | Platsen för den nya servern. |
|sku-name| GP_Gen5_8 | Den här parametern anger prisnivå nivå, compute-generering och antal virtuella kärnor för den nya servern. GP_Gen5_8 mappas till en generell användning, generation 5-server med 8 vCores.|


>[!Important]
>När du skapar en ny server med en geo-återställning, ärver samma lagringsstorlek och prisnivå som källservern. Dessa värden kan inte ändras när du skapar. När den nya servern har skapats kan kan lagringsstorleken skalas upp.

När återställningen är klar, leta upp den nya servern och kontrollera att data har återställts som förväntat.

## <a name="next-steps"></a>Nästa steg
- Mer information om tjänstens [säkerhetskopior](concepts-backup.md).
- Läs mer om [affärskontinuitet](concepts-business-continuity.md) alternativ.
