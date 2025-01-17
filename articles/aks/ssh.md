---
title: SSH till noder i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar en SSH-anslutning med Azure Kubernetes Service (AKS) klusternoder för felsökning och underhållsåtgärder.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 34f2d11cf4e1fb8e03d037be221e7b18ed4c5ad0
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303329"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ansluta med SSH till Azure Kubernetes Service (AKS) klusternoderna för underhåll och felsökning

Du kan behöva åtkomst till ett AKS-noden under livscykeln för ditt kluster i Azure Kubernetes Service (AKS). Den här åtkomsten kan vara för underhåll, Logginsamling eller andra felsökning åtgärder. Du kan komma åt AKS-noder med SSH, inklusive Windows Server-noder (för närvarande i förhandsversion i AKS). Du kan också [ansluta till Windows Server-noder med hjälp av Fjärrskrivbordsprotokollet (RDP) anslutningar][aks-windows-rdp]. Av säkerhetsskäl exponeras inte AKS-noder till internet.

Den här artikeln visar hur du skapar en SSH-anslutning med ett AKS-noden med sina privata IP-adresser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.64 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Lägg till din offentliga SSH-nyckel

Som standard är SSH-nycklar fick, eller genereras och läggs till noder när du skapar ett AKS-kluster. Om du vill ange olika SSH-nycklar än de som används när du skapade ditt AKS-kluster kan du lägga till din offentliga SSH-nyckel Linux AKS-noder. Om det behövs kan du skapa en SSH nyckeln med hjälp av [macOS eller Linux][ssh-nix] or [Windows][ssh-windows]. Om du använder PuTTY Gen för att skapa nyckelparet, spara nyckelpar i en OpenSSH-format i stället för standardvärdet PuTTy format på privat nyckel (.ppk-fil).

> [!NOTE]
> SSH-nycklar kan att för närvarande endast lägga till Linux-noder med Azure CLI. Om du använder Windows Server-noder kan använda SSH-nycklarna som anges när du skapade AKS-klustret och vidare till steg på [så här hämtar du den AKS nodadressen](#get-the-aks-node-address). Eller, [ansluta till Windows Server-noder med hjälp av Fjärrskrivbordsprotokollet (RDP) anslutningar][aks-windows-rdp].

Stegen för att hämta AKS-nodernas privata IP-adressen är olika beroende på vilken typ av AKS-kluster som du kör:

* För de flesta AKS-kluster följer du stegen för att [hämta IP-adressen för regelbundna AKS-kluster](#add-ssh-keys-to-regular-aks-clusters).
* Om du använder alla funktioner i förhandsversion i AKS med VM-skalningsuppsättningar, till exempel flera nodpooler eller stöd för Windows Server-behållare kan [följer du stegen för VM scale set-baserade AKS kluster](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Lägg till SSH-nycklar till vanliga AKS-kluster

Om du vill lägga till din SSH-nyckel till en Linux AKS-nod, utför du följande steg:

1. Hämta resursgruppens namn för dina resurser för AKS-kluster som använder [az aks show][az-aks-show]. Klusternamnet tilldelas till variabeln med namnet *CLUSTER_RESOURCE_GROUP*. Ersätt *myResourceGroup* med namnet på resursgruppen där du AKS-kluster finns:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Lista de virtuella datorerna i AKS kluster resource gruppen med den [az vm list][az-vm-list] kommando. Dessa virtuella datorer är AKS-noder:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    Följande Exempelutdata visar AKS-noder:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Lägg till SSH-nycklar till noden genom att använda den [az vm user update][az-vm-user-update] kommando. Ange resursgruppens namn och sedan en av noderna i AKS som hämtades i föregående steg. Användarnamn för AKS-noder är som standard *azureuser*. Ange platsen för din egen SSH offentlig nyckel plats, till exempel *~/.ssh/id_rsa.pub*, eller klistra in innehållet i din offentliga SSH-nyckel:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Lägg till SSH-nycklar i VM scale set-baserade AKS-kluster

Utför följande steg för att lägga till din SSH-nyckel till en Linux AKS-nod som är en del av en VM-skalningsuppsättning:

1. Hämta resursgruppens namn för dina resurser för AKS-kluster som använder [az aks show][az-aks-show]. Klusternamnet tilldelas till variabeln med namnet *CLUSTER_RESOURCE_GROUP*. Ersätt *myResourceGroup* med namnet på resursgruppen där du AKS-kluster finns:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Hämta sedan skalningsuppsättning för virtuell dator för AKS-kluster med den [az vmss list][az-vmss-list] kommando. VM scale set namnet tilldelas till variabeln med namnet *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Lägg till SSH-nycklar till noderna i en skalningsuppsättning för virtuell dator genom att använda den [az vmss-tilläggsuppsättningen][az-vmss-extension-set] kommando. Klusterresursgrupp och VM scale set namn tillhandahålls i föregående kommandon. Användarnamn för AKS-noder är som standard *azureuser*. Om det behövs uppdaterar du platsen för din egen SSH offentlig nyckel plats, till exempel *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Tillämpa SSH-nyckeln till noder med hjälp av den [az vmss update-instances][az-vmss-update-instances] kommando:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Hämta AKS nod-adressen

AKS-nodernas exponeras inte offentligt på Internet. SSH för AKS-noder kan du använda den privata IP-adressen. I nästa steg ska skapa du en helper-pod AKS-klustret som låter dig SSH till den här privata IP-adressen för noden. Stegen för att hämta AKS-nodernas privata IP-adressen är olika beroende på vilken typ av AKS-kluster som du kör:

* För de flesta AKS-kluster följer du stegen för att [hämta IP-adressen för regelbundna AKS-kluster](#ssh-to-regular-aks-clusters).
* Om du använder alla funktioner i förhandsversion i AKS med VM-skalningsuppsättningar, till exempel flera nodpooler eller stöd för Windows Server-behållare kan [följer du stegen för VM scale set-baserade AKS kluster](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>SSH till vanliga AKS-kluster

Visa privat IP-adressen för ett AKS-kluster noden med den [az vm list-ip-adresser][az-vm-list-ip-addresses] command. Provide your own AKS cluster resource group name obtained in a previous [az-aks-show][az-aks-show] steg:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Följande Exempelutdata visar privata IP-adresserna för AKS-noder:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>SSH till VM scale set-baserade AKS-kluster

Lista de noder som använder interna IP-adress i [kubectl hämta kommando][kubectl-get]:

```console
kubectl get nodes -o wide
```

Följande Exempelutdata visar interna IP-adresserna för alla noder i klustret, inklusive en Windows Server-nod.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Anteckna den interna IP-adressen för den nod som du vill felsöka. Du använder den här adressen i ett senare steg.

## <a name="create-the-ssh-connection"></a>Skapa SSH-anslutning

För att skapa en SSH-anslutning till ett AKS-nod, kör du en helper-pod AKS-klustret. Den här helper-pod innehåller SSH-åtkomst till klustret och sedan ytterligare SSH-noden åtkomst. Om du vill skapa och använda den här helper-pod, gör du följande:

1. Kör en `debian` behållare avbildningen och bifoga en terminalsession till den. Den här behållaren kan användas för att skapa en SSH-session med en nod i AKS-klustret:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Om du använder Windows Server-noder (för närvarande i förhandsversion i AKS), lägger du till en nod-väljare i kommandot för att schemalägga Debian-behållare på en Linux-nod på följande sätt:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Den grundläggande Debian-avbildningen innehåller inte SSH-komponenter. När terminalsessionen är ansluten till behållaren, installerar du en SSH-klient med hjälp av `apt-get` på följande sätt:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. I ett nytt terminalfönster inte är ansluten till din behållare, en lista med poddarna på AKS-kluster med den [kubectl hämta poddar][kubectl-get] kommando. Pod som skapades i föregående steg som startar med namnet *aks-ssh*, enligt följande exempel:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Du lagt till din offentliga SSH-nyckel AKS-noden i det första steget i den här artikeln. Nu kan kopiera den privata SSH-nyckeln till din pod. Den privata nyckeln används för att skapa en SSH i AKS-noder.

    Ange din egen *aks-ssh* podnamn hämtades i föregående steg. Om det behövs ändrar *~/.ssh/id_rsa* till platsen för din privata SSH-nyckel:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Tillbaka i terminalsession till behållaren, uppdatera behörigheterna på den kopierade `id_rsa` privata SSH-nyckeln så att den är användaren skrivskyddad:

    ```console
    chmod 0600 id_rsa
    ```

1. Nu ska du skapa en SSH-anslutning till AKS-nod. Igen, Standardanvändarnamnet för AKS-noder är *azureuser*. Acceptera uppmaningen att fortsätta med anslutningen som SSH-nyckeln är den första betrodda. Du sedan får bash-Kommandotolken för AKS-nod:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Ta bort SSH-åtkomst

När du är klar `exit` SSH-sessionen och sedan `exit` interaktiva container-sessionen. När den här behållaren sessionen stängs tas pod som används för SSH-åtkomst från AKS-klustret bort.

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsökning data, kan du [visa kubelet-loggar][view-kubelet-logs] or [view the Kubernetes master node logs][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
