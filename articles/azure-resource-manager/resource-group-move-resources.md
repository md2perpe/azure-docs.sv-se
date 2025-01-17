---
title: Flytta Azure-resurser till ny prenumeration eller resursgrupp grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: tomfitz
ms.openlocfilehash: 6cb2f49113a67a8dc6cea70ae58bd440f420a1d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442798"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Flytta resurser till ny resursgrupp eller prenumeration

Den här artikeln visar hur du flyttar Azure-resurser till en annan Azure-prenumeration eller en annan resursgrupp i samma prenumeration. Du kan använda Azure-portalen, Azure PowerShell, Azure CLI eller REST API för att flytta resurser.

Både källgruppen och målgruppen är låsta vid flytt. Skriva och ta bort blockeras på resursgrupper tills flyttningen är klar. Låset innebär att du kan inte lägga till, uppdatera eller ta bort resurser i resursgrupper, men det innebär inte att resurserna som är låsta. Om du flyttar en SQL Server och dess databas till en ny resursgrupp, inträffar ett program som använder databasen utan avbrott. Det kan fortfarande läsa och skriva till databasen.

En resurs flyttas bara flyttar det till en ny resursgrupp. Flyttåtgärden kan inte ändra resursens plats. Den nya resursgruppen kan ha en annan plats, men som ändra inte platsen för resursen.

> [!NOTE]
> Den här artikeln beskriver hur du flytta resurser mellan befintliga Azure-prenumerationer. Om du verkligen vill uppgradera din Azure-prenumeration (till exempel byta från kostnadsfritt till betala per användning) måste konvertera din prenumeration.
> * Om du vill uppgradera en kostnadsfri utvärderingsversion, [uppgradera din kostnadsfria utvärderingsversion eller en Microsoft Imagine Azure-prenumeration till betala per användning](..//billing/billing-upgrade-azure-subscription.md).
> * Om du vill ändra en betala per användning-konto, se [ändra din betala per användning för Azure-prenumeration till ett annat erbjudande](../billing/billing-how-to-switch-azure-offer.md).
> * Om du inte kan konvertera prenumerationen, [skapa en Azure-supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj **prenumerationshantering** för typ av ärende.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="when-to-call-azure-support"></a>När anropet Azure-support

Du kan flytta de flesta resurser via självbetjäning åtgärder som visas i den här artikeln. Använd självbetjäningsåtgärder till:

* Flytta resurshanterarens resurser.
* Flytta klassiska resurser enligt den [begränsningar för klassisk distribution](#classic-deployment-limitations).

Kontakta [stöder](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) när du behöver:

* Flytta dina resurser till en ny Azure-konto (och Azure Active Directory-klient) och du behöver hjälp med anvisningarna i föregående avsnitt.
* Flytta klassiska resurser, men har problem med begränsningar.

## <a name="services-that-can-be-moved"></a>Tjänster som kan flyttas

Följande lista innehåller en allmän översikt över Azure-tjänster som kan flyttas till en ny resursgrupp och prenumeration. En lista över vilken resurs som typer stöd för att flytta finns i [flytta åtgärden stöd för resurser](move-support-resources.md).

* Analysis Services
* API Management
* App Service-appar (webbappar) – Se [begränsningar för App Service](#app-service-limitations)
* App Service-certifikat – Se [begränsningar för App Service Certificate](#app-service-certificate-limitations)
* App Service-domän
* Automation - Runbooks måste finnas i samma resursgrupp som Automation-kontot.
* Azure Active Directory B2C
* Azure Cache för Redis - om Azure Cache för Redis-instans som är konfigurerad med ett virtuellt nätverk, instansen kan inte flyttas till en annan prenumeration. Se [virtuella nätverk begränsningar](#virtual-networks-limitations).
* Azure Cosmos DB
* Azure-datautforskaren
* Azure-databas för MariaDB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure DevOps - Följ stegen för att [ändra Azure-prenumerationen används för fakturering](/azure/devops/organizations/billing/change-azure-subscription?view=azure-devops).
* Azure Maps
* Azure Monitor-loggar
* Azure Relay
* Azure Stack - registreringar
* Batch
* BizTalk Services
* Robottjänst
* CDN
* Molntjänster – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)
* Cognitive Services
* Container Registry
* Content Moderator
* Cost Management
* Customer Insights
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* HDInsight-kluster – Se [HDInsight begränsningar](#hdinsight-limitations)
* IoT Central
* IoT-hubbar
* Key Vault - Nyckelvalv som används för diskkryptering kan inte flyttas till resursgrupper i samma prenumeration eller mellan prenumerationer.
* Belastningsutjämnare – grundläggande SKU-belastningsutjämnare kan flyttas. Standard-SKU-belastningsutjämnare kan inte flyttas.
* Logic Apps
* Maskininlärning – Machine Learning Studio-webbtjänster kan flyttas till en resursgrupp i samma prenumeration, men inte en annan prenumeration. Andra Machine Learning-resurser kan flyttas mellan prenumerationer.
* Managed Disks - hanterade diskar i Availability Zones kan inte flyttas till en annan prenumeration
* Media Services
* Övervaka – Se till att flytta till ny prenumeration inte överstiger [prenumerationskvoter](../azure-subscription-service-limits.md#azure-monitor-limits)
* Notification Hubs
* Operational Insights
* Operations Management
* Portalen instrumentpaneler
* Powerbi – både Power BI Embedded och Power BI-Arbetsytesamling
* Offentliga IP - Basic SKU offentlig IP-adress kan flyttas. Standard-SKU offentlig IP-adress kan inte flyttas.
* Recovery Services-valv – Se [begränsningar](#recovery-services-limitations).
* SAP HANA på Azure
* Scheduler
* Sök - du kan inte flytta flera Sök efter resurser i olika regioner i en enda åtgärd. I stället flyttar du dem i separata åtgärder.
* Service Bus
* Service Fabric
* Service Fabric Mesh
* SignalR Service
* Storage
* Storage (klassisk) – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)
* Storage Sync-tjänsten
* Stream Analytics - tillstånd för Stream Analytics-jobb inte kan flyttas när du kör i.
* SQL Database-server - databas och server måste vara i samma resursgrupp. Om du flyttar en SQLServer, flyttas även alla dess databaser. Det här beteendet gäller för Azure SQL Database och Azure SQL Data Warehouse-databaser.
* Time Series Insights
* Traffic Manager
* Virtual Machines – Se [begränsningar för virtuella datorer](#virtual-machines-limitations)
* Virtuella datorer (klassiska) – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)
* VM Scale Sets – Se [begränsningar för virtuella datorer](#virtual-machines-limitations)
* Virtuella nätverk - finns i [begränsningar för virtuella nätverk](#virtual-networks-limitations)
* VPN Gateway

### <a name="services-that-cannot-be-moved"></a>Tjänster som inte kan flyttas

Följande lista innehåller en allmän översikt över Azure-tjänster som inte kan flyttas till en ny resursgrupp och prenumeration. Läs mer detaljerat, [flytta åtgärden stöd för resurser](move-support-resources.md).

* AD DS
* Hybrid AD-tjänsten för hälsotillstånd
* Application Gateway
* Azure Database Migration
* Azure Databricks
* Azure Firewall
* Azure Kubernetes Service (AKS)
* Azure Migrate
* Azure NetApp Files
* Certifikat - App Service-certifikat kan flyttas, men uppladdade certifikat har [begränsningar](#app-service-limitations).
* Klassiska program
* Container Instances
* Container Service
* Data Box
* Dev blanksteg
* Dynamics LCS
* ExpressRoute
* Ytterdörren
* Lab Services – klassrum Labs kan inte flyttas till en ny resursgrupp eller prenumeration. DevTest Labs kan flyttas till en ny resursgrupp i samma prenumeration, men inte mellan prenumerationer.
* Managed Applications
* Hanterad identitet - användartilldelade
* Microsoft Genomics
* Säkerhet
* Site Recovery
* StorSimple Device Manager
* Virtuella nätverk (klassisk) – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)

## <a name="limitations"></a>Begränsningar

Avsnittet innehåller beskrivningar av hur du hanterar komplicerade scenarier för att flytta resurser. Begränsningarna är:

* [Begränsningar för virtuella datorer](#virtual-machines-limitations)
* [Begränsningar för virtuellt nätverk](#virtual-networks-limitations)
* [Begränsningar för App Service](#app-service-limitations)
* [Begränsningar för App Service Certificate](#app-service-certificate-limitations)
* [Begränsningar för klassisk distribution](#classic-deployment-limitations)
* [Recovery Services-begränsningar](#recovery-services-limitations)
* [HDInsight-begränsningar](#hdinsight-limitations)

### <a name="virtual-machines-limitations"></a>Begränsningar för virtuella datorer

Du kan flytta virtuella datorer med hanterade diskar, hanterade bilder, hanterade ögonblicksbilder och tillgänglighetsuppsättningar med virtuella datorer som använder hanterade diskar. Hanterade diskar i Availability Zones kan inte flyttas till en annan prenumeration.

Följande scenarier stöds inte ännu:

* Virtuella datorer med certifikat som lagras i Key Vault kan flyttas till en ny resursgrupp i samma prenumeration, men inte mellan prenumerationer.
* Virtual Machine Scale Sets med Standard-SKU-belastningsutjämnare eller SKU offentlig IP kan inte flyttas.
* Virtuella datorer som skapats från Marketplace-resurser med anslutna-planer kan inte flyttas mellan resursgrupper eller prenumerationer. Avetablera den virtuella datorn i den aktuella prenumerationen och distribuera igen i den nya prenumerationen.
* Virtuella datorer i ett befintligt virtuellt nätverk där användaren inte har för avsikt att flytta alla resurser i det virtuella nätverket.

Om du vill flytta virtuella datorer som konfigurerats med Azure Backup, använder du följande lösning:

* Hitta platsen för den virtuella datorn.
* Hitta en resursgrupp med följande namngivningsmönstret: `AzureBackupRG_<location of your VM>_1` exempelvis AzureBackupRG_westus2_1
* Azure-portalen och sedan kontrollera om ”Visa dolda typer”
* Om du är i PowerShell, använder de `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Om du är i CLI, använder den `az resource list -g AzureBackupRG_<location of your VM>_1`
* Hitta resursen med typen `Microsoft.Compute/restorePointCollections` som har namngivningsmönstret `AzureBackup_<name of your VM that you're trying to move>_###########`
* Ta bort den här resursen. Den här åtgärden tar bort bara de direkta återställningspunkterna, inte säkerhetskopierade data i valvet.
* När delete är klar kommer du att kunna flytta den virtuella datorn. Du kan flytta valv och virtuell dator till målprenumerationen. Du kan fortsätta säkerhetskopieringar utan att förlora data efter överflyttningen.
* Information om hur du flyttar Recovery Service-valv för säkerhetskopiering finns i [begränsningar för Recovery Services](#recovery-services-limitations).

### <a name="virtual-networks-limitations"></a>Begränsningar för virtuellt nätverk

När du flyttar ett virtuellt nätverk, måste du även flytta beroende resurser. För VPN-gatewayer måste du flytta IP-adresser, virtuella nätverksgatewayer och alla associerade anslutningsresurser. Lokala nätverksgatewayer kan finnas i en annan resursgrupp.

Om du vill flytta en virtuell dator med ett nätverkskort måste du flytta alla beroende resurser. Du måste flytta virtuellt nätverk för nätverkskortet, alla andra nätverkskort för det virtuella nätverket och VPN-gatewayer.

Om du vill flytta en peer-kopplade virtuella nätverket måste du först inaktivera virtuell nätverkspeering. Du kan flytta det virtuella nätverket när inaktiverat. Återaktivera virtuell nätverkspeering efter överflyttningen.

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med resursnavigeringslänkar. Om Azure Cache för Redis-resursen har distribuerats i ett undernät har till exempel en resursnavigeringslänken i det undernätet.

### <a name="app-service-limitations"></a>Begränsningar för App Service

Begränsningar för att flytta App Service-resurser variera beroende på om du flyttar resurser inom en prenumeration eller till en ny prenumeration. Om din webbapp använder ett App Service Certificate kan se [begränsningar för App Service Certificate](#app-service-certificate-limitations)

#### <a name="moving-within-the-same-subscription"></a>Flytta inom samma prenumeration

När du flyttar en Webbapp _inom samma prenumeration_, du kan inte flytta från tredje part SSL-certifikat. Men du kan flytta en Webbapp till den nya resursgruppen utan att flytta dess certifikat från tredje part och appens SSL fortfarande fungerar.

Om du vill flytta SSL-certifikat med Webbappen gör du följande:

1. Ta bort certifikatet från tredje part från Web App, men behålla en kopia av certifikatet
2. Flytta Webbappen.
3. Ladda upp certifikatet från tredje part till flyttade Webbappen.

#### <a name="moving-across-subscriptions"></a>Flytta mellan prenumerationer

När du flyttar en Webbapp _mellan prenumerationer_, gäller följande begränsningar:

- Målresursgruppen får inte ha några befintliga App Service-resurser. App Service-resurser är:
    - Web Apps
    - App Service-planer
    - Överförda eller importerade SSL-certifikat
    - Apptjänstmiljöer
- Alla App Service-resurser i resursgruppen måste flyttas tillsammans.
- App Service-resurser kan bara flyttas från resursgruppen där de skapades. Om en App Service-resursen är inte längre i dess ursprungliga resursgruppen, den måste flyttas tillbaka till den ursprungliga resursgruppen först och sedan de kan flyttas mellan prenumerationer.

Om du inte kommer ihåg ursprungliga resursgruppen, kan du hitta den diagnostiken. Din webbapp, Välj **diagnostisera och lösa problem**. Välj **konfiguration och hantering av**.

![Välj diagnostik](./media/resource-group-move-resources/select-diagnostics.png)

Välj **migreringsalternativ**.

![Välj migreringsalternativ](./media/resource-group-move-resources/select-migration.png)

Välj alternativet för rekommenderade steg för att flytta webbappen.

![Välj rekommenderade åtgärder](./media/resource-group-move-resources/recommended-steps.png)

Du ser de rekommenderade åtgärderna som ska vidtas innan du flyttar resurser. Informationen omfattar ursprungliga resursgruppen för webbappen.

![Rekommendationer](./media/resource-group-move-resources/recommendations.png)

### <a name="app-service-certificate-limitations"></a>Begränsningar för App Service Certificate

Du kan flytta din App Service-certifikat till en ny resursgrupp eller prenumeration. Om din App Service-certifikat är bundet till en webbapp, måste du vidta vissa åtgärder innan du flyttar resurser till en ny prenumeration. Ta bort SSL-bindning och privata certifikat från webbapp innan du flyttar resurser. App Service Certificate behöver inte tas bort, bara privata certifikat i webbapp.

### <a name="classic-deployment-limitations"></a>Begränsningar för klassisk distribution

Alternativ för att flytta resurser som har distribuerats via den klassiska modellen variera beroende på om du flyttar resurser inom en prenumeration eller till en ny prenumeration.

#### <a name="same-subscription"></a>Samma prenumeration

När du flyttar resurser från en resursgrupp till en annan resursgrupp i samma prenumeration, gäller följande begränsningar:

* Virtuella nätverk (klassisk) kan inte flyttas.
* Virtuella datorer (klassiska) måste flyttas med Molntjänsten.
* Molntjänsten kan bara flyttas när flytten omfattar alla virtuella datorer.
* Endast en molntjänst kan flyttas åt gången.
* Endast en storage-konto (klassisk) kan flyttas åt gången.
* Storage-konto (klassisk) kan inte flyttas på samma gång med en virtuell dator eller en tjänst i molnet.

Flytta klassiska resurser till en ny resursgrupp i samma prenumeration genom att använda standard flyttåtgärder via den [portal](#use-portal), Azure PowerShell, Azure CLI eller REST API. Du kan använda samma åtgärder som du använder för att flytta Resurshanterarens resurser.

#### <a name="new-subscription"></a>Ny prenumeration

När du flyttar resurser till en ny prenumeration, gäller följande begränsningar:

* Alla klassiska resurser i prenumerationen måste flyttas på samma gång.
* Målprenumerationen får inte ha andra klassiska resurser.
* Flytten kan bara begäras via en separat REST-API för klassiska flyttar. Standardkommandon för Resource Manager-flytta fungerar inte när du flyttar klassiska resurser till en ny prenumeration.

Flytta klassiska resurser till en ny prenumeration genom att använda REST-åtgärder som är specifika för klassiska resurser. Om du vill använda REST, gör du följande:

1. Kontrollera om käll-prenumeration kan delta i en flytt mellan prenumerationer. Använd följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     I begärandetexten, inkluderar du:

   ```json
   {
    "role": "source"
   }
   ```

     Svaret för valideringen har följande format:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

2. Kontrollera om målprenumerationen kan delta i en flytt mellan prenumerationer. Använd följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     I begärandetexten, inkluderar du:

   ```json
   {
    "role": "target"
   }
   ```

     Svaret är i samma format som datakälla prenumerationen verifieringen.
3. Om båda prenumerationerna passerar valideringen flytta alla klassiska resurser från en prenumeration till en annan prenumeration med följande åtgärd:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    I begärandetexten, inkluderar du:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

Åtgärden kan ta flera minuter.

### <a name="recovery-services-limitations"></a>Recovery Services-begränsningar

 Följ dessa steg om du vill flytta ett Recovery Services-valv: [Flytta resurser till ny resursgrupp eller prenumeration](../backup/backup-azure-move-recovery-services-vault.md).

För närvarande kan du flytta ett Recovery Services-valv per region i taget. Du kan inte flytta valv som säkerhetskopiera Azure Files eller Azure File Sync SQL i IaaS-datorer.

Om en virtuell dator inte flyttas med valvet, förblir aktuella återställningspunkterna för virtuell dator i valvet tills de upphör att gälla. Om den virtuella datorn flyttas med valvet eller inte, kan du återställa den virtuella datorn från historik för säkerhetskopiering i valvet.

Recovery Services-valv stöder inte över prenumerationer säkerhetskopieringar. Om du flyttar ett valv med säkerhetskopierade data för virtuell dator mellan prenumerationer måste du flytta dina virtuella datorer till samma prenumeration och Använd samma målresursgruppen för att fortsätta säkerhetskopieringar.

Principer för säkerhetskopiering som definierats för valvet hålls när valvet har flyttats. Rapportering och övervakning måste ställas in igen för valvet efter flytten.

Flytta en virtuell dator till en ny prenumeration utan att flytta Recovery Services-valvet:

 1. Tillfälligt stoppa säkerhetskopiering
 1. [Ta bort återställningspunkten](#virtual-machines-limitations). Den här åtgärden tar bort bara de direkta återställningspunkterna, inte säkerhetskopierade data i valvet.
 1. Flytta de virtuella datorerna till den nya prenumerationen
 1. Skydda den igen under ett nytt valv i prenumerationen

Flytta är inte aktiverad för lagring, nätverk och beräkning av de resurser som används för att konfigurera haveriberedskap med Azure Site Recovery. Anta exempelvis att du har konfigurerat replikeringen av dina lokala datorer till ett lagringskonto (Storage1) och vill att den skyddade datorn för att få fram efter en redundansväxling till Azure som en virtuell dator (VM1) kopplade till ett virtuellt nätverk (Network1). Du kan inte flytta någon av dessa Azure-resurser – Storage1 VM1 och Network1 - mellan resursgrupper i samma prenumeration eller mellan prenumerationer.

### <a name="hdinsight-limitations"></a>HDInsight-begränsningar

Du kan flytta HDInsight-kluster till en ny prenumeration eller resursgrupp. Men kan inte du flytta mellan prenumerationer som nätverksresurser som är länkad till HDInsight-klustret (till exempel virtuella nätverk, nätverkskort eller belastningsutjämnare). Dessutom kan flytta du inte till en ny resursgrupp ett nätverkskort som är kopplad till en virtuell dator för klustret.

När du flyttar ett HDInsight-kluster till en ny prenumeration först flytta andra resurser (t.ex. storage-konto). Flytta sedan HDInsight-klustret ensamt.

## <a name="checklist-before-moving-resources"></a>Checklistan innan du flyttar resurser

Några viktiga steg måste utföras innan en resurs flyttas. Du kan undvika fel genom att verifiera dessa villkor.

1. Käll- och målprenumerationer måste vara aktiv. Om du har problem med att aktivera ett konto som har inaktiverats, [skapa en Azure-supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj **prenumerationshantering** för typ av ärende.

1. Käll- och målprenumerationer måste finnas inom samma [Azure Active Directory-klient](../active-directory/develop/quickstart-create-new-tenant.md). Om du vill kontrollera att båda prenumerationerna har samma klient-ID, använder du Azure PowerShell eller Azure CLI.

   Använd för Azure PowerShell:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Om du använder Azure CLI använder du:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Om klient-ID: N för käll- och målprenumerationer inte är samma, kan du använda följande metoder för att stämma av klient-ID: N:

   * [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md)
   * [Så här associerar du eller lägger till en prenumeration i din Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Målprenumerationen måste vara registrerad för resursprovidern för den resurs som flyttas. Om inte, du får ett felmeddelande om att den **prenumerationen har inte registrerats för en resurstyp**. Du kan se det här felet när du flyttar en resurs till en ny prenumeration, men att prenumerationen aldrig har använts med den resurstypen.

   Använd följande kommandon för att hämta registreringsstatus PowerShell:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Om du vill registrera en resursleverantör, använder du:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   För Azure CLI, använder du följande kommandon för att hämta registreringsstatus:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Om du vill registrera en resursleverantör, använder du:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Det konto som flyttar resurser måste ha minst följande behörigheter:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** på resursgrupp för källa.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** på målresursgruppen.

1. Kontrollera prenumerationskvoter för den prenumeration som du flyttar resurser till innan du flyttar resurser. Om du flytta resurserna innebär prenumerationen kommer att överskrida gränsen, måste du granska om du kan begära en ökning av kvoten. En lista över begränsningar och hur du begär en ökning, se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).

1. När det är möjligt, flyttar break stora till separata flyttåtgärder. Resource Manager returnerar omedelbart ett fel när det finns fler än 800 resurser i en enda åtgärd. Men kan flyttar resurser som är mindre än 800 också misslyckas av tiden går ut.

1. Tjänsten måste göra det möjligt att flytta resurser. Att fastställa om flytten kommer att lyckas [verifiera din begäran om att flytta](#validate-move). I avsnitten nedan i den här artikeln som [tjänster kan flytta resurser](#services-that-can-be-moved) och vilka [tjänster inte kan flytta resurser](#services-that-cannot-be-moved).

## <a name="validate-move"></a>Verifiera flytt

Den [verifiera flyttåtgärden](/rest/api/resources/resources/validatemoveresources) kan du testa ditt move-scenario utan att faktiskt flytta resurserna. Använd den här åtgärden för att avgöra om flytten lyckas. Om du vill köra den här åtgärden, måste den:

* namn på resursgrupp för källa
* resurs-ID för målresursgruppen
* resurs-ID för varje resurs att flytta
* den [åtkomsttoken](/rest/api/azure/#acquire-an-access-token) för ditt konto

Skicka följande begäran:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

Med en brödtext i begäran:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om begäran har formaterats korrekt, returnerar åtgärden:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 statuskoden anger verifieringsförfrågan togs emot, men den inte har fastställt om flyttåtgärden lyckas. Den `location` värdet innehåller en URL som används för att kontrollera statusen för långvarig åtgärd.  

Om du vill kontrollera statusen genom att skicka följande begäran:

```
GET <location-url>
Authorization: Bearer <access-token>
```

Medan åtgärden körs, fortsätta att ta emot 202 statuskoden. Vänta antalet sekunder som anges i den `retry-after` värdet innan du försöker igen. Om åtgärden för att flytta verifieras felmeddelandet 204 statuskoden. Om flytten valideringen misslyckas visas ett felmeddelande som:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="move-resources"></a>Flytta resurser

### <a name="a-nameuse-portal-by-using-azure-portal"></a><a name="use-portal" />Med hjälp av Azure-portalen

Välj resursgruppen med dessa resurser för att flytta resurser, och välj sedan den **flytta** knappen.

![Flytta resurser](./media/resource-group-move-resources/select-move.png)

Välj om du flyttar resurser till en ny resursgrupp eller en ny prenumeration.

Välj resurser att flytta och målresursgruppen. Bekräfta att du behöver uppdatera skript för dessa resurser och välj **OK**. Om du har valt prenumerationen redigeringsikonen i föregående steg, måste du också välja målprenumerationen.

![Välj mål](./media/resource-group-move-resources/select-destination.png)

I **meddelanden**, du ser att flyttåtgärden körs.

![Visa flytta status](./media/resource-group-move-resources/show-status.png)

När den har slutförts meddelas du om resultatet.

![Visa flytta resultat](./media/resource-group-move-resources/show-result.png)

### <a name="by-using-azure-powershell"></a>Med hjälp av Azure PowerShell

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [flytta AzResource](/powershell/module/az.resources/move-azresource) kommando. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Om du vill flytta till en ny prenumeration kan innehålla ett värde för den `DestinationSubscriptionId` parametern.

### <a name="by-using-azure-cli"></a>Med hjälp av Azure CLI

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [az resursflytt](/cli/azure/resource?view=azure-cli-latest#az-resource-move) kommando. Ange resurs-ID resurser att flytta. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp. I den `--ids` parameter, ange en blankstegsavgränsad lista med resurs-ID för att flytta.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Om du vill flytta till en ny prenumeration, ange den `--destination-subscription-id` parametern.

### <a name="by-using-rest-api"></a>Med hjälp av REST API

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att köra:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

I begärandetexten anger du målresursgruppen och resurser för att flytta. Mer information om åtgärden för att flytta REST finns i [flytta resurser](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Nästa steg

* Läs om PowerShell-cmdletar för att hantera resurser i [med hjälp av Azure PowerShell med Resource Manager](manage-resources-powershell.md).
* Läs om Azure CLI-kommandon för att hantera resurser i [med hjälp av Azure CLI med resurshanteraren](manage-resources-cli.md).
* Läs om portalen funktioner för att hantera din prenumeration i [hantera resurser med hjälp av Azure portal](resource-group-portal.md).
* Läs om hur du tillämpar en logisk struktur till resurser i [med taggar för att organisera dina resurser](resource-group-using-tags.md).
