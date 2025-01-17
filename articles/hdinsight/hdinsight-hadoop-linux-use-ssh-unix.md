---
title: Använda SSH med Hadoop – Azure HDInsight
description: Du kan komma åt HDInsight med hjälp av SSH (Secure Shell). Det här dokumentet innehåller information om hur du ansluter till HDInsight med hjälp av ssh- och scp-kommandon från Windows, Linux, Unix eller macOS klienter.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: hadoop-kommandon i linux, hadoop linux-kommandon, hadoop macos, ssh hadoop, ssh hadoop-kluster
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: cc60a8ca0e0b04a5fcec5300fbeb1a0f34d1c7b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686369"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH

Lär dig hur du använder [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) för säker anslutning till Apache Hadoop på Azure HDInsight. Information om hur du ansluter via ett virtuellt nätverk finns i [Azure HDInsight virtuella nätverksarkitekturen](./hdinsight-virtual-network-architecture.md) och [utöka Azure HDInsight med hjälp av Azure Virtual Network](./hdinsight-extend-hadoop-virtual-network.md).

I följande tabell innehåller adress och port information som behövs för att ansluta till HDInsight med hjälp av en SSH-klient:

| Adress | Port | Ansluter till ... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Den primära huvudnoden |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Den sekundära huvudnoden |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Edge-nod (ML Services på HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Kantnod (alla andra klustertyper, om det finns en kantnod) |

Ersätt `<clustername>` med namnet på klustret. Ersätt `<edgenodename>` med namnet på kantnoden. 

Om klustret innehåller en kantnod, rekommenderar vi att du __alltid ansluter till kantnoden__ via SSH. Värdtjänster för huvudnoder är viktiga för Hadoops hälsotillstånd. Kantnoden kör bara det som du placerar på den. Mer information om hur du använder kantnoder finns i [Använda kantnoder i HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node).

> [!TIP]  
> När du ansluter till HDInsight första gången kan SSH-klienten visa en varning att värdens äkthet inte kan fastställas. Vid uppmaning väljer du ”ja” för att lägga till värden i SSH-klientens lista över betrodda servrar.
>
> Om du tidigare har anslutit till en server med samma namn kan du få en varning att den lagrade värdnyckeln inte matchar serverns värdnyckel. Läs dokumentationen för SSH-klienten om du vill ha information om hur du tar bort den befintliga posten för servernamnet.

## <a name="ssh-clients"></a>SSH-klienter

Linux, Unix- och macOS system ger kommandon `ssh` och `scp`. Klienten `ssh` används ofta för att skapa en fjärrsession med kommandoradsverktyget med Linux eller Unix-baserade system. Klienten `scp` används för att kopiera filer mellan klienten och fjärrdatorn på ett säkert sätt.

Microsoft Windows installerar ingen SSH-klient som standard. Klienterna `ssh` och `scp` är tillgängliga för Windows via följande paket:

* [OpenSSH-klient](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse). Det här är en valfri funktion som introducerades i Windows 10 Fall Creators Update.

* [Bash på Ubuntu på Windows 10](https://docs.microsoft.com/windows/wsl/about).

* [Azure Cloud Shell](../cloud-shell/quickstart.md). Cloud Shell tillhandahåller en Bash-miljö i din webbläsare.

* [Git](https://git-scm.com/).

Det finns också flera grafiska SSH-klienter, till exempel [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) och [MobaXterm](https://mobaxterm.mobatek.net/). Dessa klienter kan användas för att ansluta till HDInsight, men processen för att ansluta skiljer sig från anslutningsprocessen med `ssh`-verktyget. Mer information finns i dokumentationen för den grafiska klient som du använder.

## <a id="sshkey"></a>Autentisering: SSH nycklar

SSH-nycklar använder [kryptografi med offentliga nycklar](https://en.wikipedia.org/wiki/Public-key_cryptography) att autentisera SSH-sessioner. SSH-nycklar är säkrare än lösenord och är ett enkelt sätt att skydda Hadoop-klustret.

Om ditt SSH-konto skyddas med en nyckel måste klienten tillhandahålla den matchande privata nyckeln när du ansluter:

* De flesta klienter kan konfigureras att använda en __standardnyckel__. Exempelvis söker `ssh`-klienten efter en privat nyckel i `~/.ssh/id_rsa` i Linux- och Unix-miljöer.

* Du kan ange __sökvägen till en privat nyckel__. Med `ssh`-klienten används `-i`-parametern för att ange sökvägen till den privata nyckeln. Till exempel `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Om du har __flera privata nycklar__ för användning med olika servrar kan verktyg som [ssh-agent användas (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). Verktyget `ssh-agent` kan användas för att automatiskt välja nyckeln som ska användas när en SSH-session etableras.

> [!IMPORTANT]  
> Om du skyddar den privata nyckeln med en lösenfras måste du ange lösenfrasen när du använder nyckeln. Verktyg som `ssh-agent` kan underlätta för dig genom att cachelagra lösenordet.

### <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Använd `ssh-keygen`-kommandot för att skapa filer för offentliga och privata nycklar. Följande kommando genererar ett 2048-bitars RSA-nyckelpar som kan användas med HDInsight:

    ssh-keygen -t rsa -b 2048

Du uppmanas att ange information när nyckeln skapas. Till exempel var nycklarna lagras eller om du vill använda en lösenfras. När processen har slutförts har två filer skapats: en offentlig nyckel och en privat nyckel.

* Den __offentliga nyckeln__ används för att skapa ett HDInsight-kluster. Den offentliga nyckeln har filnamnstillägget `.pub`.

* Den __privata nyckeln__ används för att autentisera din klient mot HDInsight-klustret.

> [!IMPORTANT]  
> Du kan skydda dina nycklar med hjälp av en lösenfras. Detta är ett lösenord för den privata nyckeln. Även om någon får tag på din privata nyckel behöver de lösenfrasen för att kunna använda nyckeln.

### <a name="create-hdinsight-using-the-public-key"></a>Skapa HDInsight med hjälp av den offentliga nyckeln

| Genereringsmetod | Så här använder du den offentliga nyckeln |
| ------- | ------- |
| Azure Portal | Avmarkera __Använd samma lösenord som klusterinloggning__ och välj sedan __Offentlig nyckel__ som SSH-autentiseringstyp. Välj slutligen filen för den offentliga nyckeln eller klistra in textinnehållet från filen i fältet __Offentlig SSH-nyckel__.</br>![Dialogrutan Offentlig SSH-nyckel vid generering av HDInsight-kluster](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | Använd den `-SshPublicKey` -parametern för den [New AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet och skicka innehållet i den offentliga nyckeln som en sträng.|
| Azure CLI | Använd den `--sshPublicKey` -parametern för den [az hdinsight skapa](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) och skicka innehållet i den offentliga nyckeln som en sträng. |
| Resource Manager-mall | Ett exempel på hur du använder SSH-nycklar med en mall finns i avsnittet [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Distribuera HDInsight i Linux med en SSH-nyckel). `publicKeys`-elementet i filen [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) används för att skicka nycklarna till Azure när klustret skapas. |

## <a id="sshpassword"></a>Autentisering: Lösenord

SSH-konton kan skyddas med ett lösenord. När du ansluter till HDInsight med hjälp av SSH uppmanas du att ange lösenordet.

> [!WARNING]  
> Microsoft rekommenderar inte lösenordsverifiering för SSH. Lösenord kan gissas och är sårbara för råstyrkeattacker. I stället rekommenderar vi att du använder [SSH-nycklar för autentisering](#sshkey).

> [!IMPORTANT]  
> SSH-kontolösenordet upphör att gälla 70 dagar efter att HDInsight-klustret har skapats. Om ditt lösenord upphört att gälla, kan du ändra det med hjälp av informationen i dokumentet [Hantera HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Skapa HDInsight med ett lösenord

| Genereringsmetod | Så här anger du lösenordet |
| --------------- | ---------------- |
| Azure Portal | SSH-användarkontot har som standard samma lösenord som kontot för klusterinloggning. Om du vill använda ett annat lösenord avmarkerar du __Använd samma lösenord som klusterinloggning__ och anger sedan lösenordet i fältet __SSH-lösenord__.</br>![Dialogrutan SSH-lösenord när ett HDInsight-kluster skapas](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | Använd den `--SshCredential` -parametern för den [New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet och skicka en `PSCredential` objekt som innehåller SSH-användarens kontonamn och lösenord. |
| Azure CLI | Använd den `--sshPassword` -parametern för den [az hdinsight skapa](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) kommandot och ange lösenordsvärdet. |
| Resource Manager-mall | Ett exempel på hur du använder ett lösenord med en mall finns i [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Distribuera HDInsight i Linux med SSH-lösenord). `linuxOperatingSystemProfile`-elementet i filen [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) används för att skicka SSH-kontonamnet och SSH-lösenordet till Azure när klustret skapas.|

### <a name="change-the-ssh-password"></a>Ändra SSH-lösenordet

Information om hur du ändrar lösenordet för SSH-användarkontot finns i avsnittet __Change passwords__ (Ändra lösenord) i dokumentet [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) (Hantera HDInsight).

## <a id="domainjoined"></a>Autentisering: Domänansluten HDInsight

Om du använder ett __domänanslutet HDInsight-kluster__ måste du använda `kinit`-kommandot efter anslutning med lokal SSH-användare. Det här kommandot frågar efter en domänanvändare och ett lösenord och autentiserar din session med Azure Active Directory-domänen som är associerad med klustret.

Du kan också aktivera Kerberos-autentisering på varje domänansluten domännod (till exempel huvudnoden, kantnod) för att ssh med domänkontot. Det gör du genom att redigera sshd-konfigurationsfilen:
```bash
sudo vi /etc/ssh/sshd_config
```
avkommentera och ändra `KerberosAuthentication` till `yes`

```bash
sudo service sshd restart
```

Du kan när som helst verifiera om Kerberos-autentiseringen lyckades eller inte genom att använda kommandot `klist`.

Mer information finns i avsnittet [Configure domain-joined HDInsight](./domain-joined/apache-domain-joined-configure.md) (Konfigurera domänanslutna HDInsight-kluster).

## <a name="connect-to-nodes"></a>Anslut till noder

Huvudnoderna och kantnoden (om sådan finns) kan nås via Internet på port 22 och 23.

* När du ansluter till __huvudnoder__ använder du __22__ för att ansluta till den primära huvudnoden, och port __23__ för att ansluta till den sekundära huvudnoden. Det fullständiga domännamnet som ska användas är `clustername-ssh.azurehdinsight.net`, där `clustername` är namnet på klustret.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```
    
* När du ansluter till __kantnoden__ använder du port 22. Det fullständiga domännamnet är `edgenodename.clustername-ssh.azurehdinsight.net`, där `edgenodename` är det namn du angav när du skapade kantnoden. `clustername` är namnet på klustret.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> I föregående exempel förutsätts att du använder lösenordsverifiering eller att certifikatautentisering sker automatiskt. Om du använder ett SSH-nyckelpar för autentisering och certifikatet inte används automatiskt, anger du den privata nyckeln med parametern `-i`. Till exempel `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

När du är ansluten ändras fönstret till att visa SSH-användarnamnet och den nod du är ansluten till. När du exempelvis är ansluten till den primära huvudnoden som `sshuser` visar fönstret `sshuser@hn0-clustername:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Ansluta till arbetarnoder och Apache Zookeeper-noder

Arbetarnoder och Zookeeper-noder är inte tillgängliga direkt från internet. De kan nås från klustrets huvudnoder eller kantnoder. Här är de allmänna steg som du följer för att ansluta till andra noder:

1. Använd SSH för att ansluta till en huvud- eller kantnod:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. Från SSH-anslutningen till huvud- eller kantnoden använder du `ssh`-kommandot för att ansluta till en arbetarnod i klustret:

        ssh sshuser@wn0-myhdi

    Om du vill hämta en lista över nodnamnen finns i den [hantera HDInsight med hjälp av Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentet.

Om SSH-kontot är skyddat med ett __lösenord__ anger du lösenordet när du ansluter.

Om SSH-kontot är säkrad med __SSH-nycklar__ kontrollerar du att SSH-vidarebefordran är aktiverad på klienten.

> [!NOTE]  
> Ett annat sätt att direkt komma åt alla noder i klustret är att installera HDInsight i ett virtuellt Azure-nätverk. Därefter kan du ansluta till din fjärrdatorn i samma virtuella nätverk och direkt komma åt alla noder i klustret.
>
> Mer information finns i [Use a virtual network with HDInsight](hdinsight-extend-hadoop-virtual-network.md) (Använda ett virtuellt nätverk med HDInsight).

### <a name="configure-ssh-agent-forwarding"></a>Konfigurera vidarebefordran med SSH-agenten

> [!IMPORTANT]  
> I följande steg förutsätter vi att du har ett Linux- eller UNIX-baserat system och att du arbetar med Bash i Windows 10. Om de här stegen inte fungerar på din dator kan du behöva läsa dokumentationen för SSH-klienten.

1. Använd en textredigerare och öppna `~/.ssh/config`. Om den här filen inte finns kan du skapa den genom att ange `touch ~/.ssh/config` på en kommandorad.

2. Lägg till följande text i `config`-filen.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Ersätt informationen för __Host__ med adressen för den nod som du ansluter till med hjälp av SSH. I föregående exempel används kantnoden. Den här posten konfigurerar vidarebefordran med SSH-agenten för den angivna noden.

3. Testa SSH-agentvidarebefordran med hjälp av följande kommando från terminalen:

        echo "$SSH_AUTH_SOCK"

    Det här kommandot returnerar information liknande följande text:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Om inget returneras så körs inte `ssh-agent`. Läs informationen om skripten för agentstart i [Använda ssh-agent med ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) eller läs dokumentationen för din SSH-klient för mer information.

4. När du har verifierat att **ssh-agent** körs använder du följande för att lägga till din privata SSH-nyckel till agenten:

        ssh-add ~/.ssh/id_rsa

    Om din privata nyckel finns i en annan fil ersätter du `~/.ssh/id_rsa` med sökvägen till filen.

5. Anslut till kant- eller huvudnoder i klustret med hjälp av SSH. Använd sedan SSH-kommandot för att ansluta till en arbetar- eller Zookeeper-nod. Anslutningen upprättas med hjälp av den vidarebefordrade nyckeln.

## <a name="copy-files"></a>Kopiera filer

Verktyget `scp` kan användas för att kopiera filer till och från enskilda noder i ett kluster. Med följande kommando kopieras till exempel katalogen `test.txt` från det lokala systemet till den primära huvudnoden:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Eftersom ingen sökväg har angetts efter `:` placeras filen i hemkatalogen `sshuser`.

I följande exempel kopieras filen `test.txt` från hemkatalogen `sshuser` på den primära huvudnoden till det lokala systemet:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]  
> `scp` har bara åtkomst tull filsystemet för enskilda noder i klustret. Det kan inte användas för att få åtkomst till data i det HDFS-kompatibla lagringsutrymmet för klustret.
>
> Använd `scp` när du behöver ladda upp en resurs för en SSH-session. Ladda exempelvis upp ett Python-skript och kör det sedan från en SSH-session.
>
> I följande dokument hittar du information om att läsa in data direkt till det HDFS-kompatibla lagringsutrymmet:
>
> * [HDInsight med Azure Storage](hdinsight-hadoop-use-blob-storage.md).
>
> * [HDInsight med hjälp av Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>Nästa steg

* [Använda SSH-tunnlar med HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)
* [Använda ett virtuellt nätverk med HDInsight](hdinsight-extend-hadoop-virtual-network.md)
* [Använda kantnoder i HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
