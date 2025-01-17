---
title: Begrepp - nätverk i Azure Kubernetes-tjänster (AKS)
description: Läs mer om nätverk i Azure Kubernetes Service (AKS), inklusive kubenet och Azure CNI nätverk, ingående domänkontrollanter, belastningsutjämnare och statiska IP-adresser.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: afb7acda67eb5818ace8169dc4e98fb86bdbeaa7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442009"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Nätverkskoncept för program i Azure Kubernetes Service (AKS)

I en behållarbaserad mikrotjänster metod för utveckling av måste programkomponenter samarbeta för att bearbeta sina uppgifter. Kubernetes tillhandahåller olika resurser som gör det här programmets kommunikation. Du kan ansluta till och exponera program internt eller externt. Om du vill skapa program med hög tillgänglighet kan du läsa in balansera dina program. Mer komplexa program kan kräva konfiguration av inkommande trafik för SSL/TLS-avslutning eller Routning av flera komponenter. Av säkerhetsskäl kan du också behöva begränsa flödet av nätverkstrafik till eller mellan poddar och noder.

Den här artikeln innehåller grundläggande begrepp som tillhandahåller nätverk till dina program i AKS:

- [Tjänster](#services)
- [Azure-nätverk](#azure-virtual-networks)
- [Ingress-styrenheter](#ingress-controllers)
- [Nätverksprinciper](#network-policies)

## <a name="kubernetes-basics"></a>Grundläggande om Kubernetes

För att tillåta åtkomst till dina program eller om programkomponenter som ska kommunicera med varandra, tillhandahåller Kubernetes ett Abstraktionslager för virtuella nätverk. Kubernetes-noderna är anslutna till ett virtuellt nätverk och kan tillhandahålla inkommande och utgående anslutningar till poddar. Den *kube-proxy* komponenten körs på alla noder att tillhandahålla dessa nätverks-funktioner.

I Kubernetes, *Services* gruppera poddarna för direkt åtkomst via en IP-adress eller DNS-namn och på en viss port. Du kan även distribuera trafik med hjälp av en *belastningsutjämnare*. Mer komplex routning av programtrafik kan även uppnås med *Ingress-styrenheter*. Säkerhet och filtrering av nätverkstrafik för poddar som är möjligt med Kubernetes *nätverksprinciper* (i förhandsversion i AKS).

Azure-plattformen hjälper också till att förenkla virtuella nätverk för AKS-kluster. När du skapar en belastningsutjämnare för Kubernetes är underliggande Azure belastningsutjämningsresursen skapas och konfigureras. När du öppnar nätverksportar till poddar konfigureras de motsvarande Azure reglerna för nätverkssäkerhetsgrupper. För HTTP-routning för program, Azure kan också konfigurera *externa DNS* som ny inkommande vägar har konfigurerats.

## <a name="services"></a>Tjänster

För att förenkla nätverkskonfiguration för programarbetsbelastningar Kubernetes använder *Services* logiskt kan gruppera en uppsättning poddar och ange nätverksanslutning. Följande typer av tjänsten är tillgängliga:

- **Kluster-IP** -skapar en intern IP-adress för användning i AKS-klustret. Bra för interna program som har stöd för andra arbetsbelastningar i klustret.

    ![Diagram över klustrets IP-trafikflödet i ett AKS-kluster][aks-clusterip]

- **NodePort** -skapar en portmappning på den underliggande nod som gör att program som kan nås direkt med noden IP-adress och port.

    ![Diagram över NodePort trafikflödet i ett AKS-kluster][aks-nodeport]

- **LoadBalancer** – skapar en Azure load balancer-resurs, konfigurerar en extern IP-adress och ansluter de begärda poddarna till belastningsutjämnarens serverdelspool. Om du vill tillåta för kunder att skapas räckvidd programmet, regler för belastningsutjämning på önskad portar. 

    ![Diagram över belastningsutjämnare trafikflödet i ett AKS-kluster][aks-loadbalancer]

    För ytterligare kontroll och routning av inkommande trafik, kan du i stället använda en [Ingress-kontrollant](#ingress-controllers).

- **ExternalName** -skapar en specifik DNS-post för enklare programåtkomst.

IP-adressen för belastningsutjämnare och tjänster kan tilldelas dynamiskt eller ange en befintlig statisk IP-adress som du använder. Både interna och externa statiska IP-adresser kan tilldelas. Den här befintlig statisk IP-adress är ofta kopplat till en DNS-post.

Båda *interna* och *externa* belastningsutjämnare kan skapas. Interna belastningsutjämnare endast har tilldelats en privat IP-adress, så kan inte nås från Internet.

## <a name="azure-virtual-networks"></a>Azures virtuella nätverk

I AKS, kan du distribuera ett kluster som använder någon av följande två modeller:

- *Kubenet* nätverket - nätverket resurser skapas och konfigureras enligt AKS-klustret distribueras normalt.
- *Azure Container nätverk gränssnitt (CNI)* nätverk – The AKS-kluster är ansluten till befintliga virtuella nätverksresurser och konfigurationer.

### <a name="kubenet-basic-networking"></a>Kubenet (grundläggande) nätverk

Den *kubenet* nätverk alternativet är standardkonfigurationen för att skapa för AKS-kluster. Med *kubenet*, noder hämta en IP-adress från det virtuella Azure-undernätet. Poddar ta emot en IP-adress från en logiskt olika adressutrymmen till undernätet för Azure-nätverk av noderna. Network adress translation (NAT) konfigureras sedan så att poddarna kan nå resurser på Azure-nätverket. Källans IP-adress av trafiken är skulle NAT till nodens primära IP-adress.

Noder använder den [kubenet][kubenet] Kubernetes-plugin-programmet. Du kan låta Azure-plattformen skapa och konfigurera de virtuella nätverken för dig eller välja att distribuera din AKS-kluster till ett befintligt undernät för virtuellt nätverk. Återigen endast noderna får en dirigerbara IP-adress och poddarna använder NAT för att kommunicera med andra resurser utanför AKS-klustret. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverk kan poddar att använda.

Mer information finns i [konfigurera kubenet nätverk för ett AKS-kluster][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (Avancerat)-nätverk

Med Azure CNI varje pod får en IP-adress från undernätet och kan nås direkt. Dessa IP-adresser måste vara unikt för ditt adressutrymme för nätverket och måste planeras i förväg. Varje nod har en konfigurationsparameter för det maximala antalet poddar som stöds. Det motsvarande antalet IP-adresser per nod reserveras sedan direkt för noden. Den här metoden kräver planering, som annars kan leda till IP-adress överbelastning eller att behöva återskapa kluster i ett större undernät allteftersom dina behov växer.

Noder använder den [Azure behållare nätverk gränssnitt (CNI)][cni-networking] Kubernetes-plugin-programmet.

![Diagram över två noder med bryggor ansluta dem till ett enda Azure virtuellt nätverk][advanced-networking-diagram]

Mer information finns i [konfigurera Azure CNI för ett AKS-kluster][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Jämför modeller för nätverk

Både kubenet och Azure CNI ger nätverksanslutning för AKS-kluster. Det finns emellertid fördelar och nackdelar för varje. På hög nivå gäller följande överväganden:

* **kubenet**
    * Sparar IP-adressutrymme.
    * Använder Kubernetes interna eller externa belastningsutjämnare för att nå poddar från utanför klustret.
    * Du måste manuellt hantera och underhålla användardefinierade vägar (Udr).
    * Högst 400 noder per kluster.
* **Azure CNI**
    * Poddar få fullständig virtuell nätverksanslutning och direkt kan nås från utanför klustret.
    * Kräver mer IP-adressutrymme.

Följande beteende skillnader finns mellan kubenet och Azure CNI:

| Funktion                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Distribuera kluster i befintliga eller nya virtuella nätverket                                            | Stöd för: Udr använde manuellt | Stöds |
| Pod-pod-anslutning                                                                         | Stöds | Stöds |
| Pod-VM-anslutning; Virtuell dator i samma virtuella nätverk                                          | Fungerar när initieras av poddar | Fungerar åt båda hållen |
| Pod-VM-anslutning; Virtuella datorer i peer-kopplade virtuella nätverk                                            | Fungerar när initieras av poddar | Fungerar åt båda hållen |
| Lokal åtkomst med VPN eller Express Route                                                | Fungerar när initieras av poddar | Fungerar åt båda hållen |
| Åtkomst till resurser som skyddas av Tjänsteslutpunkter                                             | Stöds | Stöds |
| Visa Kubernetes-tjänster som använder en load balancer tjänst eller App Gateway ingående styrenhet | Stöds | Stöds |
| Azure DNS och privata zoner                                                          | Stöds | Stöds |

### <a name="support-scope-between-network-models"></a>Supportens omfattning mellan nätverk modeller

Oavsett modellen nätverk du använder kan både kubenet och Azure CNI distribueras på något av följande sätt:

* Azure-plattformen kan automatiskt skapa och konfigurera de virtuella nätverksresurserna när du skapar ett AKS-kluster.
* Du kan manuellt skapa och konfigurera de virtuella nätverksresurserna och kopplas till dessa resurser när du skapar AKS-klustret.

Även om funktioner som Tjänsteslutpunkter eller udr: er stöds med både kubenet och Azure CNI den [stöder principer för AKS][support-policies] definierar vilka ändringar som du kan göra. Exempel:

* Om du manuellt skapa virtuella nätverksresurser för ett AKS-kluster stöds när du konfigurerar dina egna udr: er eller Tjänsteslutpunkter.
* Om Azure-plattformen automatiskt skapar virtuella nätverksresurser för AKS-klustret, går den inte att manuellt ändra dessa AKS-hanterade resurser för att konfigurera din egen udr: er eller Tjänsteslutpunkter.

## <a name="ingress-controllers"></a>Ingress-styrenheter

När du skapar en LoadBalancer typ Service skapas en underliggande Azure belastningsutjämningsresursen. Belastningsutjämnaren har konfigurerats för att distribuera trafik till poddarna i din tjänst på en viss port. LoadBalancer-fungerar bara på layer 4 - tjänsten är inte medveten om faktiska program och kan inte göra några ytterligare överväganden för routning.

*Ingress-styrenheter* fungerar på lager 7 och smartare regler kan använda för att distribuera trafik. Ett vanligt användningsområde för Ingress-kontrollant är att dirigera HTTP-trafik till olika program baserat på inkommande URL.

![Diagram över ingående trafikflödet i ett AKS-kluster][aks-ingress]

I AKS, kan du skapa en Ingress-resurs med något som NGINX eller använda funktionen AKS HTTP programmet routning. När du aktiverar HTTP programmet routning för ett AKS-kluster kan Azure-plattformen skapar Ingress-kontrollanten och en *externt DNS* controller. Då nya Ingress-resurser skapas i Kubernetes, skapas de nödvändiga DNS A-posterna i en klusterspecifika DNS-zon. Mer information finns i [distribuera routning av HTTP-programmet][aks-http-routing].

En annan vanlig funktion för ingång är SSL/TLS-avslutning. I stora webbprogram som kan nås via HTTPS, kan TLS-avslutning hanteras av Ingress-resursen inte själva programmet. Du kan konfigurera Ingress-resurs för att använda leverantörer som krypterar vi för att tillhandahålla automatisk generering av TLS-certifiering och konfiguration. Läs mer om hur du konfigurerar en NGINX-Ingress-kontrollant med ska vi kryptera [Ingångshändelser och TLS][aks-ingress-tls].

Du kan också konfigurera ingress-kontrollanten för att bevara klienten käll-IP för förfrågningar till behållare i AKS-klustret. När en klientbegäran dirigeras till en behållare i AKS-klustret via ingress-kontrollant, ursprunglig käll-ip för begäran inte tillgängliga för en målbehållare. När du aktiverar *klienten källa IP konservering*, käll-IP för klienten är tillgänglig i rubriken under *X vidarebefordras för*. Om du använder klienten källa IP konservering på ingress-kontrollant, kan du inte använda SSL direkt. Klienten källa IP bevara och SSL direkt kan användas med andra tjänster, till exempel den *LoadBalancer* typen.

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En nätverkssäkerhetsgrupp filtrerar trafik för virtuella datorer, till exempel AKS-noder. När du skapar tjänster, till exempel en LoadBalancer konfigurerar Azure-plattformen automatiskt alla regler för nätverkssäkerhetsgrupper som krävs. Inte manuellt konfigurera regler för nätverkssäkerhetsgrupper för att filtrera trafik för poddar i ett AKS-kluster. Definiera alla nödvändiga portar och vidarebefordran som en del av Kubernetes Service-manifest och låt Azure-plattformen skapa eller uppdatera lämpliga regler. Du kan också använda principer för nätverk, enligt beskrivningen i nästa avsnitt, att automatiskt tillämpa filter trafikregler på poddar.

## <a name="network-policies"></a>Nätverksprinciper

Som standard kan alla poddar i ett AKS-kluster skicka och ta emot trafik utan begränsningar. För ökad säkerhet kan du definiera regler som styr flödet av trafik. Serverdelsprogrammen exponeras ofta bara till nödvändiga frontend-tjänster eller databaskomponenter är endast tillgängliga på nivån för program som ansluter till dem.

Nätverksprincip är en funktion för Kubernetes som är tillgängliga i AKS som låter dig styra trafikflödet mellan poddar. Du kan välja att tillåta eller neka trafik baserat på inställningar, till exempel tilldelade etiketter, namnområde eller trafik port. Nätverkssäkerhetsgrupper är mer för AKS-noder inte poddar. Användningen av principer för nätverk är ett mer passande och molnbaserade sätt att styra flödet av trafik. Då poddar skapas dynamiskt i ett AKS-kluster, kan de nödvändiga nätverksprinciperna tillämpas automatiskt.

Mer information finns i [skydda trafik mellan poddar med hjälp av principer för nätverk i Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Nästa steg

Att komma igång med AKS nätverk, skapa och konfigurera ett AKS-kluster med dina egna IP-adressintervall med hjälp av [kubenet][aks-configure-kubenet-networking] or [Azure CNI][aks-configure-advanced-networking].

Associerade metodtips finns [bästa praxis för nätverksanslutning och säkerhet i AKS][operator-best-practices-network].

Mer information om core Kubernetes och AKS-begrepp finns i följande artiklar:

- [Kubernetes / AKS-kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS åtkomst och identitet][aks-concepts-identity]
- [Kubernetes / AKS-säkerhet][aks-concepts-security]
- [Kubernetes / AKS-lagring][aks-concepts-storage]
- [Kubernetes / AKS skala][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
