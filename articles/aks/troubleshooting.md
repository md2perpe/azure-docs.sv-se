---
title: Felsöka vanliga problem med Azure Kubernetes Service
description: Lär dig att felsöka och lösa vanliga problem när du använder Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: f0b0ff3ff4ac742a7e850798c736eb31098f66e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65966391"
---
# <a name="aks-troubleshooting"></a>AKS felsökning

När du skapar eller hantera kluster i Azure Kubernetes Service (AKS), kan det ibland uppstå problem. Den här artikeln beskriver några vanliga problem och felsökning.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>I allmänhet var hittar jag information om hur du felsöker problem med Kubernetes?

Prova den [officiella guiden för att felsöka Kubernetes-kluster](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Det finns också en [felsökningsguide](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)publicerade av en Microsoft-tekniker för att felsöka poddar, noder, kluster och andra funktioner.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Jag får ett ”kvoten har överskridits”-fel under generering och uppgradering. Vad ska jag göra? 

Du behöver [begär kärnor](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Vad är den maximala poddar per nod-inställningen för AKS?

Den maximala poddar per nod-inställningen är 30 som standard om du distribuerar ett AKS-kluster i Azure-portalen.
Den maximala poddar per nod-inställningen är 110 som standard om du distribuerar ett AKS-kluster i Azure CLI. (Kontrollera att du använder den senaste versionen av Azure CLI). Den här standardinställningen kan ändras med hjälp av den `–-max-pods` flagga i den `az aks create` kommando.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Jag får ett insufficientSubnetSize-fel när du distribuerar ett AKS-kluster med avancerade nätverk. Vad ska jag göra?

Om du använder Azure CNI (Avancerat nätverk), AKS preallocates IP-adresserna baserat på ”max-poddarna” per nod som har konfigurerats. Antalet noder i ett AKS-kluster kan finnas var som helst mellan 1 och 110. Baserat på de konfigurerade maximala poddarna per nod, ska storleken på undernätet vara större än ”produkten av antalet noder och max pod per nod”. Följande grundläggande beräkning beskrivs detta:

Undernätets storlek > Antal noder i klustret (ta hänsyn till kraven för framtida skalning) * max poddar per nod.

Mer information finns i [Planera IP-adresser för ditt kluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Min pod har fastnat i CrashLoopBackOff läge. Vad ska jag göra?

Det kan finnas olika anledningar för pod som fastnat i detta läge. Du kan se ut till:

* Pod, med hjälp av `kubectl describe pod <pod-name>`.
* Loggar med hjälp av `kubectl log <pod-name>`.

Läs mer om hur du felsöker problem med pod [felsöka program](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Jag försöker aktivera RBAC på ett befintligt kluster. Hur gör jag för att?

Tyvärr stöds aktiverar rollbaserad åtkomstkontroll (RBAC) på befintliga kluster inte just nu. Du måste uttryckligen skapa nya kluster. Om du använder CLI är RBAC aktiverad som standard. Om du använder AKS-portalen, är en växlingsknapp för att aktivera RBAC tillgänglig i skapa arbetsflödet.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Jag har skapat ett kluster med RBAC aktiverat med hjälp av Azure-CLI med standardinställningarna eller Azure portal och nu många varningar visas på Kubernetes-instrumentpanelen. Instrumentpanelen som används för att fungera utan alla varningar. Vad ska jag göra?

Orsaken till varningar på instrumentpanelen är att klustret har nu aktiverats med RBAC och åtkomst till den har inaktiverats som standard. I allmänhet är den här metoden bra eftersom standard exponering av instrumentpanelen för alla användare av klustret kan leda till säkerhetshot. Om du vill aktivera instrumentpanelen följer du stegen i [det här blogginlägget](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Jag kan inte ansluta till instrumentpanelen. Vad ska jag göra?

Det enklaste sättet att komma åt tjänsten utanför klustret är att köra `kubectl proxy`, vilka proxyservrar förfrågningar som skickas till din localhost port 8001 till Kubernetes API-servern. Därifrån kan API-servern kan proxy till din tjänst: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Om du inte ser Kubernetes-instrumentpanelen, kontrollera om den `kube-proxy` pod körs i den `kube-system` namnområde. Om den inte körs, ta bort en pod och kommer att startas om.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Jag kan inte hämta loggarna genom att använda kubectl loggar eller jag kan inte ansluta till API-servern. Jag får ”fel från servern: fel uppringning backend: Ring tcp...”. Vad ska jag göra?

Se till att nätverkssäkerhetsgruppen som standard inte ändrats och att port 22 är öppen för anslutning till API-servern. Kontrollera om den `tunnelfront` pod körs i den *kube system* namnområde med hjälp av den `kubectl get pods --namespace kube-system` kommando. Framtvinga borttagning av en pod och den startas om det inte finns.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Jag försöker uppgradera eller skala och får en ”meddelande: Är inte tillåtet att ändra egenskapen 'imageReference' ”fel. Hur kan jag åtgärda det här problemet?

Du kanske får felet eftersom du har ändrat taggar i agentnoder i AKS-klustret. Ändra och ta bort taggar och andra egenskaper för resurser i resursgruppen MC_ * kan leda till oväntade resultat. Ändra resurser under den MC_ *-gruppen i AKS delar kluster mål för servicenivå (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Jag får fel som mitt kluster är i felaktigt tillstånd och uppgradera eller skalning fungerar inte förrän den har lösts

*Den här hjälp med felsökning dirigeras från https://aka.ms/aks-cluster-failed*

Det här felet uppstår när kluster anger ett felaktigt tillstånd av flera skäl. Följ stegen nedan för att lösa dina misslyckades klustertillstånd innan du försöker tidigare misslyckade igen:

1. Tills klustret har inget `failed` tillstånd, `upgrade` och `scale` åtgärder inte lyckas. Vanliga rotorsaker problem och lösningar omfattar:
    * Skala med **otillräcklig beräkningskvot (CRP)** . För att lösa måste du först skala ditt kluster tillbaka till en stabil målstatusen inom kvoten. Följ dessa [steg för att begära en compute-kvoten öka](../azure-supportability/resource-manager-core-quotas-request.md) innan du försöker att skala upp igen utanför som inledande kvotgränser.
    * Skala ett kluster med avancerade nätverk och **otillräcklig (nätverk) undernätverksresurser**. För att lösa måste du först skala ditt kluster tillbaka till en stabil målstatusen inom kvoten. Följ [dessa steg för att begära en resurskvot öka](../azure-resource-manager/resource-manager-quota-errors.md#solution) innan du försöker att skala upp igen utanför som inledande kvotgränser.
2. När den underliggande orsaken till uppgraderingen skulle misslyckas har lösts måste klustret vara i slutfört läge. När ett lyckat tillstånd har verifierats, gör om den ursprungliga åtgärden.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Jag får fel när du försöker uppgradera och skala som tillstånd mitt kluster håller för närvarande att uppgraderas eller uppgraderingen misslyckades

*Den här hjälp med felsökning dirigeras från https://aka.ms/aks-pending-upgrade*

Klusteråtgärder begränsas när aktiva åtgärder för uppgradering sker eller en uppgradering har försökt, men senare misslyckades. Diagnostisera problemet kör `az aks show -g myResourceGroup -n myAKSCluster -o table` att hämta detaljerad status för ditt kluster. Baserat på resultatet:

* Om klustret aktivt uppgraderar, vänta tills åtgärden avslutas. Om den lyckades försök misslyckats tidigare igen.
* Om klustret har misslyckad uppgradering, följer du stegen som beskrivs ovan

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan jag flytta mitt kluster till en annan prenumeration eller min prenumeration med mitt kluster till en ny klient?

Om du har flyttat AKS-klustret till en annan prenumeration eller klustret som äger prenumerationen till en ny klient, förlorar klustret funktionalitet på grund av att förlora rolltilldelningar och tjänstens huvudnamn rättigheter. **AKS har inte stöd för glidande kluster över prenumerationer eller klienter** på grund av den här begränsningen.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Jag får fel som försöker använda funktioner som kräver VM-skalningsuppsättningar

*Den här hjälp med felsökning dirigeras från aka.ms/aks-vmss-aktivering*

Du får fel som indikerar AKS-klustret inte finns på en skalningsuppsättning för virtuell dator, till exempel i följande exempel:

**AgentPool 'agentpool' har ställt in automatisk skalning som aktiverad men finns inte på Virtual Machine Scale Sets**

Använda funktioner, till exempel autoskalningen kluster eller flera noden pooler, AKS-kluster måste skapas med VM-skalningsuppsättningar. Fel returneras om du försöker använda funktioner som är beroende av VM-skalningsuppsättningar och du rikta en regelbundna, icke-VM scale set AKS-kluster. Stöd virtuella datorn är för närvarande i förhandsversion i AKS.

Följ den *innan du börjar* stegen i lämplig webbplatsen för att registrera korrekt för VM-skalningsuppsättningen funktionen Förhandsgranska och skapa ett AKS-kluster:

* [Använda kluster autoskalningen](cluster-autoscaler.md)
* [Skapa och använda flera nodpooler](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Vilka namngivningsbegränsningar upprätthålls för AKS-resurser och parametrar?

*Den här hjälp med felsökning dirigeras från aka.ms/aks-namngivning-regler*

Namngivningsbegränsningar implementeras av både Azure-plattformen och AKS. Om ett Resursnamn eller parametern skadar något av dessa begränsningar, returneras ett fel som ombeds du ange en annan indata. Följande vanliga riktlinjerna för namngivning gäller:

* AKS *MC_* resursgruppens namn kombinerar resursgruppens namn och resursnamn. Den automatisk genererade syntaxen för `MC_resourceGroupName_resourceName_AzureRegion` får inte vara större än 80 tecken. Om det behövs kan du minska längden på din resursgruppens namn eller ett AKS-klusternamnet.
* Den *dnsPrefix* måste börja och sluta med alfanumeriska värden. Giltiga tecken är alfanumeriska värden och bindestreck (-). Den *dnsPrefix* får inte innehålla specialtecken, till exempel en punkt (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Jag får fel vid försök att skapa, uppdatera, skala, ta bort eller uppgradera kluster som åtgärden inte är tillåten eftersom en annan åtgärd pågår.

*Den här hjälp med felsökning dirigeras från aka.ms/aks-väntande-åtgärd*

Klusteråtgärder begränsas när en tidigare åtgärd pågår fortfarande. Använd för att hämta detaljerad statusinformation för klustret, den `az aks show -g myResourceGroup -n myAKSCluster -o table` kommando. Använda en egen resursgrupp och AKS-klusternamnet vid behov.

Baserat på resultatet av klusterstatusen:

* Om klustret har alla Etableringsstatus än *lyckades* eller *misslyckades*, vänta tills åtgärden (*uppgradera / uppdatera / skapa / skalning / ta bort / migrerar*) avslutas. När den föregående åtgärden har slutförts, försök igen din senaste klusteråtgärden.

* Om klustret har en misslyckad uppgradering, följer du stegen som [jag får fel som mitt kluster är i felaktigt tillstånd och uppgraderar eller skalning fungerar inte förrän den har lösts](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).