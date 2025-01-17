---
title: Begränsa utgående trafik i Azure Kubernetes Service (AKS)
description: Lär dig vilka portar och adresser krävs för att styra utgående trafik i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: iainfou
ms.openlocfilehash: 52a9ba20b60e8ef6cdb743546cd842e4ee24b3fd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441927"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Preview - gränsen för utgående trafik för klusternoder och kontrollera åtkomst till nödvändiga portar och tjänster i Azure Kubernetes Service (AKS)

Som standard har AKS-kluster obegränsad åtkomst till internet utgående (utgående). Den här nivån av nätverksåtkomst kan noder och tjänster som du kör för att komma åt externa resurser efter behov. Om du vill begränsa utgående trafik måste ett begränsat antal portar och adresser vara tillgänglig att underhålla felfri kluster underhållsaktiviteter. Ditt kluster konfigureras sedan att bara använda grundläggande system behållaravbildningar från Microsoft Container Registry (MCR) eller Azure Container Registry (ACR), inte externa offentliga lagringsplatser. Du måste konfigurera dina önskade brandväggs- och säkerhetskonfiguration regler för att tillåta dessa portar som krävs och adresser.

Den här artikeln beskriver vilka nätverksportar och fullständigt kvalificerade domännamn (FQDN) är obligatoriska och valfria om du begränsar utgående trafik i ett AKS-kluster.  Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning, delta i. De tillhandahålls för att samla in feedback och buggar från vår community. I förhandsversionen kan är inte dessa funktioner avsedda för användning i produktion. Funktioner i offentliga förhandsversioner omfattas ”bästa prestanda” support. Hjälp från teamen för AKS-teknisk support är tillgänglig under kontorstid Pacific tidszon (Stillahavstid) endast. Mer information finns i följande supportartiklar:
>
> * [AKS supportprinciper][aks-support-policies]
> * [Vanliga frågor om Azure-Support][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.66 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Om du vill skapa ett AKS-kluster som begränsar utgående trafik du först aktivera en funktionsflagga i prenumerationen. Den här funktionsregistrering konfigurerar alla AKS-kluster som du skapar för att använda grundläggande system behållaravbildningar från MCR eller ACR. Att registrera den *AKSLockingDownEgressPreview* funktion flaggan, använda den [az funktionen registrera][az-feature-register] kommandot som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion i en prenumeration kan du inte för närvarande avregistrera den funktionen. När du aktiverar vissa funktioner i förhandsversion, kan standardinställningar användas för alla AKS-kluster som skapas i prenumerationen. Inte aktivera förhandsversionsfunktioner för produktion-prenumerationer. Använd en separat prenumeration för att testa funktioner och samla in feedback.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera registreringsstatus med hjälp av den [az funktionslistan][az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern genom att använda den [az provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Översikt för utgående trafik

Noder i ett AKS-kluster behöver åtkomst till vissa portar och fullständigt kvalificerade domännamn (FQDN) för hantering och operativa åtgärder. De här åtgärderna kan vara att kommunicera med API-servern eller för att hämta och installera kärnkomponenter Kubernetes-kluster och noden säkerhetsuppdateringar. Egress (utgående) Internettrafik begränsas inte som standard för noder i ett AKS-kluster. Klustret kan hämta grundläggande system behållaravbildningar från externa lagringsplatser.

Om du vill öka säkerheten för AKS-kluster, kan du begränsa utgående trafik. Klustret har konfigurerats för att hämta grundläggande system behållaravbildningar från MCR eller ACR. Om du låsa den utgående trafiken i det här sättet måste du definiera specifika portar och FQDN för att tillåta AKS-nodernas korrekt kommunicera med externa tjänster som krävs. AKS-noder kan inte kommunicera med API-servern eller installera kärnkomponenter utan dessa auktoriserade portar och FQDN.

Du kan använda [Azure brandvägg][azure-firewall] eller en 3 part brandväggsinstallation att skydda din utgående trafik och definiera de nödvändiga portarna och adresser. AKS skapar inte automatiskt dessa regler för dig. Följande portar och adresser är som referens när du skapar lämpliga regler i nätverkets brandvägg.

Det finns två uppsättningar med portar och adresser i AKS:

* Den [portar och adressen som krävs för AKS-kluster](#required-ports-and-addresses-for-aks-clusters) information om minimikraven för behöriga utgående trafik.
* Den [valfria rekommenderade adresser och portar för AKS-kluster](#optional-recommended-addresses-and-ports-for-aks-clusters) inte behövs för alla scenarier, men integrering med andra tjänster som Azure Monitor inte fungerar korrekt. Granska den här listan över valfria portar och FQDN och auktorisera någon av de tjänster och komponenter som används i AKS-klustret.

> [!NOTE]
> Begränsa utgående trafik fungerar bara på nytt AKS-kluster som skapas när du har aktiverat funktionen flaggan registreringen. För befintliga kluster [utföra en uppgradering klusteråtgärden][aks-upgrade] med hjälp av den `az aks upgrade` kommandot innan du begränsa den utgående trafiken.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Portar som krävs och adresser för AKS-kluster

Följande utgående portar / Nätverksregler krävs för ett AKS-kluster:

* TCP-port *443*
* TCP-port *9000* och TCP-port *22* för tunnel främre poden att kommunicera med tunnel slutet på API-servern.
    * För att få mer specifika kan se den * *.hcp.\< plats\>. azmk8s.io* och * *. tun.\< plats\>. azmk8s.io* adresser i följande tabell.

Följande FQDN / regler för program som krävs:

| FQDN                       | Port      | Användning      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Den här adressen är slutpunkten för API-server. Ersätt *\<plats\>* med den region där AKS-klustret distribueras. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Den här adressen är slutpunkten för API-server. Ersätt *\<plats\>* med den region där AKS-klustret distribueras. |
| aksrepos.azurecr.io        | HTTPS:443 | Den här adressen krävs för åtkomst-avbildningar i Azure Container Registry (ACR). |
| *.blob.core.windows.net    | HTTPS:443 | Den här adressen är en backend-butik för bilder som lagras i ACR. |
| mcr.microsoft.com          | HTTPS:443 | Den här adressen krävs för att nå bilder i Microsoft Container Registry (MCR). |
| *.cdn.mscr.io              | HTTPS:443 | Den här adressen måste anges för MCR lagringsresurs som backas upp av Azure content delivery network (CDN). |
| management.azure.com       | HTTPS:443 | Den här adressen måste anges för Kubernetes GET/PUT-åtgärder. |
| login.microsoftonline.com  | HTTPS:443 | Den här adressen krävs för Azure Active Directory-autentisering. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Den här adressen krävs för att installera snapin-paket på Linux-noder. |
| ntp.ubuntu.com             | UDP:123   | Den här adressen måste anges för tidssynkronisering med NTP på Linux-noder. |
| *.docker.io                | HTTPS:443 | Den här adressen krävs för att hämta nödvändiga behållaravbildningar för tunnel framför. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Valfritt rekommenderas adresser och portar för AKS-kluster

* UDP-port *53* för DNS

Följande FQDN / programregler rekommenderas för AKS-kluster ska fungera korrekt:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Den här adressen kan noderna i Linux ladda ned nödvändiga säkerhetsuppdateringar och uppdateringar. |
| packages.microsoft.com                  | HTTPS:443 | Den här adressen är Microsoft-paket-databasen som används för cachelagrade *apt-get* åtgärder. |
| dc.services.visualstudio.com            | HTTPS:443 | Rekommenderas för rätt mått och övervakning med hjälp av Azure Monitor. |
| *.opinsights.azure.com                  | HTTPS:443 | Rekommenderas för rätt mått och övervakning med hjälp av Azure Monitor. |
| *.monitoring.azure.com                  | HTTPS:443 | Rekommenderas för rätt mått och övervakning med hjälp av Azure Monitor. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Den här adressen används för att fungera korrekt med Azure Policy (för närvarande i förhandsversion i AKS). |
| apt.dockerproject.org                   | HTTPS:443 | Den här adressen används för rätt drivrutinsinstallation och åtgärden på GPU-baserad noder. |
| nvidia.github.io                        | HTTPS:443 | Den här adressen används för rätt drivrutinsinstallation och åtgärden på GPU-baserad noder. |

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs vilka portar och adresser så att om du begränsar utgående trafik för klustret. Du kan också definiera hur poddarna själva kan kommunicera och vilka begränsningar som finns inom klustret. Mer information finns i [skydda trafik mellan poddar med nätverksprinciper i AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
