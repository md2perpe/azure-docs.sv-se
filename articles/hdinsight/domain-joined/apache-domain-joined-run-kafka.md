---
title: Självstudie – konfigurera principer för Apache Kafka i HDInsight med Enterprise Security Package - Azure
description: Självstudie – Lär dig hur du konfigurerar Apache Ranger-principer för Kafka i Azure HDInsight med Enterprise Security Package.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: ba16a975aa3b1e60393006ef49a7e422c572931e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441374"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Självstudier: Konfigurera Apache Kafka-principer i HDInsight med Enterprise Security Package (förhandsversion)

Lär dig hur du konfigurerar Apache Ranger-principer för Apache Kafka-kluster med Enterprise Security Package (ESP). ESP-kluster är anslutna till en domän så att användare kan autentisera med autentiseringsuppgifter för domänen. I den här självstudien skapar du två Ranger-principer för att begränsa åtkomsten till `sales`- och `marketingspend`-ämnen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa domänanvändare
> * Skapa Ranger-principer
> * Skapa ämnen i ett Kafka-kluster
> * Testa Ranger-principer

## <a name="prerequisite"></a>Krav

En [HDInsight Kafka-kluster med Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet

1. Anslut till Ranger-administratörsanvändargränssnittet från en webbläsare med hjälp av URL:en `https://ClusterName.azurehdinsight.net/Ranger/`. Kom ihåg att ändra `ClusterName` till namnet på ditt Kafka-kluster. Ranger-autentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för Hadoop-kluster. Om du vill förhindra att webbläsare använder cachade Hadoop-autentiseringsuppgifter använder du ett nytt InPrivate-webbläsarfönster för att ansluta till Ranger-administratörsgränssnittet.

2. Logga in med dina administratörsautentiseringsuppgifter för Azure Active Directory (AD). Azure AD-administratörsautentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för HDInsight-kluster eller SSH-autentiseringsuppgifterna för Linux HDInsight-noder.

   ![Apache Ranger-administratörsanvändargränssnitt](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Skapa domänanvändare

Läs avsnittet om hur du [skapar ett HDInsight-kluster med Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md) för att se hur du skapar domänanvändarna **sales_user** och **marketing_user**. I ett produktionsscenario kommer domänanvändarna från din Active Directory-klientorganisation.

## <a name="create-ranger-policy"></a>Skapa en Ranger-princip

Skapa en Ranger-princip för **sales_user** och **marketing_user**.

1. Öppna **Ranger-administratörsanvändargränssnittet**.

2. Välj  **\<klusternamn > _kafka** under **Kafka**. En förkonfigurerad princip kan visas.

3. Välj **Lägg till ny princip** och ange följande värden:

   |Inställning  |Föreslaget värde  |
   |---------|---------|
   |Principnamn  |  hdi sales* policy   |
   |Ämne   |  sales* |
   |Välj användare  |  sales_user1 |
   |Behörigheter  | publish, consume, create |

   Följande jokertecken kan användas i ämnesnamnet:

   * ’*’ anger inga eller flera förekomster av tecken.
   * ’?’ anger ett enskilt tecken.

   ![Skapa en princip i Apache Ranger-administratörsanvändargränssnittet](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Vänta en stund medan Ranger synkroniserar med Azure AD om en domänanvändare inte automatiskt har fyllts i för **Välj användare**.

4. Välj **Lägg till** att spara principen.

5. Välj **Lägg till ny princip** och ange sedan följande värden:

   |Inställning  |Föreslaget värde  |
   |---------|---------|
   |Principnamn  |  hdi marketing policy   |
   |Ämne   |  marketingspend |
   |Välj användare  |  marketing_user1 |
   |Behörigheter  | publish, consume, create |

   ![Skapa en princip i Apache Ranger-administratörsanvändargränssnittet](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Välj **Lägg till** att spara principen.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Skapa ämnen i ett Kafka-kluster med ESP

Så här skapar du de två avsnitten `salesevents` och `marketingspend`:

1. Använd följande kommando för att öppna en SSH-anslutning till klustret:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Ersätt `DOMAINADMIN` med administratörsanvändaren för ditt kluster som konfigurerats under [skapa kluster](./apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp), och ersätt `CLUSTERNAME` med namnet på klustret. Ange lösenordet för administratörsanvändarkontot om du uppmanas till det. Mer information om hur du använder `SSH` med HDInsight finns i [Använda SSH med HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommandon för att spara klustrets namn som en variabel och installera ett JSON-parsningsverktyg, `jq`. Ange Kafka-klustrets namn när du uppmanas till detta.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Använd följande kommandon för att hämta värdarna för Kafka-koordinatortjänsterna. När du blir ombedd att göra det anger du lösenordet till klusteradministratörskontot.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Du kan behöva ställa in din utvecklingsmiljö om du inte redan har gjort det innan du fortsätter. Du måste komponenter, till exempel Java JDK, Apache Maven och en SSH-klient med scp. Mer information finns i [Installationsinstruktioner](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Ladda ned [exemplen på Apache Kafka-domänansluten producent/konsument](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Följ steg 2 och 3 under **Skapa och distribuera exemplet** i [Självstudie: Använda Apache Kafka-producenten och konsument-API:er](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Kör följande kommandon:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testa Ranger-principerna

Baserat på Ranger-principerna som konfigurerats kan **sales_user** skapa/använda ämnet `salesevents` men inte ämnet `marketingspend`. På motsvarande sätt kan **marketing_user** skapa/använda ämnet `marketingspend` men inte ämnet `salesevents`.

1. Öppna en ny SSH-anslutning till klustret. Använd följande kommando för att logga in som **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Kör följande kommando:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Använd koordinatornamnen från föregående avsnitt till att ange följande miljövariabler:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exempel: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Följ steg 3 under **Skapa och distribuera exemplet** i [Självstudie: Använd producent- och konsument-API:er för Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) så att `kafka-producer-consumer.jar` också är tillgänglig för **sales_user**.

5. Kontrollera att **sales_user1** kan producera till ämnet `salesevents` genom att köra följande kommando:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Kör följande kommando för att konsumera från ämnet `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Kontrollera att du kan läsa meddelandena.

7. Kontrollera att **sales_user1** inte kan producera till ämnet `marketingspend` genom att köra följande i samma ssh-fönster:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Ett auktoriseringsfel inträffar och kan ignoreras.

8. Observera att **marketing_user1** inte kan konsumera från ämnet `salesevents`.

   Upprepa steg 1–4 ovan, men nu som **marketing_user1**.

   Kör följande kommando för att konsumera från ämnet `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Det går inte att visas tidigare meddelanden.

9. Granska åtkomsthändelserna i Ranger-användargränssnittet.

   ![Principgranskning i Ranger-användargränssnittet](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda det här programmet, tar du bort Kafka-klustret som du skapade med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. I rutan **Sök** längst upp skriver du **HDInsight**.
1. Välj **HDInsight-kluster** under **Tjänster**.
1. I listan över HDInsight-kluster som visas klickar du på **...** intill det kluster som du skapade för den här självstudien. 
1. Klicka på **Ta bort**. Klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ta med din egen nyckel till Apache Kafka](../kafka/apache-kafka-byok.md)