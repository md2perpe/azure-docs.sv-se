---
title: Distribuera behållare med Helm i Kubernetes på Azure
description: Lär dig hur du använder verktyget Helm paketering för att distribuera behållare i ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: 76a5391cbe142851d9b1f60ea9346af2e7a35d6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66392141"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installera program med Helm i Azure Kubernetes Service (AKS)

[Helm] [ helm] är ett verktyg med öppen källkod paketering som hjälper dig att installera och hantera livscykeln för Kubernetes-program. Liknar Linux pakethanterare som *APT* och *Yum*, Helm används för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm i en Kubernetes-kluster i AKS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du måste också Helm CLI-verktyget, vilket är den klient som körs i utvecklingssystemet. Det kan du starta, stoppa och hantera program med Helm. Om du använder Azure Cloud Shell är Helm CLI redan installerad. Installationsanvisningar på din lokala plattform finns [installera Helm][helm-install].

> [!IMPORTANT]
> Helm är avsedd att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du kontrollera att Helm poddar endast är schemalagda att köras på Linux-noder. Du måste också se till att alla Helm-diagram som du installerar också är schemalagda att köras på rätt noderna. Kommandona i den här artikeln [noden väljare] [ k8s-node-selector] att kontrollera att poddar schemaläggs för att rätta noder, men inte alla Helm-diagram kan exponera en nod-väljare. Du kan också överväga att använda andra alternativ på klustret som [taints][taints].

## <a name="create-a-service-account"></a>Skapa ett tjänstkonto

Innan du kan distribuera Helm i en RBAC-aktiverade AKS-kluster, behöver du ett tjänstkonto och rollen bindning för Tiller-tjänsten. Mer information om hur du skyddar Helm / Tiller i en RBAC aktiverat kluster, se [Tiller namnområden och RBAC][tiller-rbac]. Hoppa över det här steget om AKS-klustret inte är aktiverat RBAC.

Skapa en fil med namnet `helm-rbac.yaml` och kopiera följande YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Skapa kontot och rollen bindning med den `kubectl apply` kommando:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Skydda Tiller och Helm

Helm-klienten och Tiller tjänsten autentisera och kommunicera med varandra med hjälp av TLS/SSL. Den här autentiseringsmetoden hjälper till att skydda Kubernetes-klustret och vilka tjänster kan distribueras. Du kan skapa egna självsignerade certifikat för att förbättra säkerheten. Varje Helm-användare ska få sina egna klientcertifikatet och Tiller skulle initieras i Kubernetes-kluster med certifikat som används. Mer information finns i [med hjälp av TLS/SSL mellan Helm och Tiller][helm-ssl].

Du kan styra åtkomstnivån som Tiller har till klustret med en RBAC-aktiverade Kubernetes-kluster. Du kan definiera Kubernetes namnområdes Tiller distribuerad i och begränsa vilka namnområden Tiller kan sedan distribuera resurser i. Den här metoden kan du skapa Tiller-instanser i olika namnområden och gränsen distribution gränser och begränsa användare av Helm-klienten till vissa namnområden. Mer information finns i [Helm rollbaserade åtkomstkontroller][helm-rbac].

## <a name="configure-helm"></a>Konfigurera Helm

För att distribuera en grundläggande Tiller i ett AKS-kluster måste använda den [”helm init”] [ helm-init] kommando. Om klustret inte är aktiverat RBAC, ta bort den `--service-account` argument och värde. Om du har konfigurerat TLS/SSL för Tiller och Helm, hoppa över det här steget för grundläggande initieringen och i stället ange de nödvändiga `--tiller-tls-` som visas i nästa exempel.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

Om du har konfigurerat TLS/SSL mellan Helm och Tiller ger den `--tiller-tls-*` parametrar och namnen på dina egna certifikat, som visas i följande exempel:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="find-helm-charts"></a>Hitta Helm-diagram

Helm-diagram för att distribuera program till ett Kubernetes-kluster. För att söka efter färdiga Helm-diagram, Använd den [helm search] [ helm-search] kommando:

```console
helm search
```

Följande komprimerade exempel på utdata visar några av de Helm-diagram som är tillgängliga för användning:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Uppdatera en lista över diagram med de [helm-lagringsplatsen update] [ helm-repo-update] kommando. I följande exempel visar en lyckad lagringsplatsen uppdatering:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Kör Helm-diagram

Om du vill installera diagram med Helm, den [helm install] [ helm-install] kommandot och ange namnet på diagrammet för att installera. Om du vill se installerar ett Helm-diagram i praktiken ska vi installera en grundläggande nginx-distribution med ett Helm-diagram. Om du har konfigurerat TLS/SSL kan du lägga till den `--tls` parametern för att använda ditt Helm-klientcertifikat.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Följande komprimerade exempel på utdata visar Distributionsstatus för Kubernetes-resurser som skapas av Helm-diagrammet:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Det tar en minut eller två innan den *extern IP-adress* -adressen för nginx-ingress-controller-tjänsten ska fyllas i och gör att du kan komma åt den med en webbläsare.

## <a name="list-helm-releases"></a>Lista Helm versioner

Om du vill se en lista över versioner som är installerad på ditt kluster, använda den [helm lista] [ helm-list] kommando. I följande exempel visas den nginx-ingress-versionen som distribuerats i föregående steg. Om du har konfigurerat TLS/SSL kan du lägga till den `--tls` parametern för att använda ditt Helm-klientcertifikat.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Rensa resurser

När du distribuerar ett Helm-diagram, skapas ett antal Kubernetes-resurser. Dessa resurser inkluderar poddar, distributioner och tjänster. Om du vill rensa de här resurserna kan använda den `helm delete` kommandot och ange namnet på din version som hittades i föregående `helm list` kommando. I följande exempel tar bort den versionen med namnet *flailing alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Kubernetes programdistributioner med Helm finns i Helm-dokumentationen.

> [!div class="nextstepaction"]
> [Helm-dokumentation][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md