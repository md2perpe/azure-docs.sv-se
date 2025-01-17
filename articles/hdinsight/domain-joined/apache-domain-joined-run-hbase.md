---
title: Självstudie – konfigurera principer för Apache HBase i HDInsight med Enterprise Security Package - Azure
description: Självstudie – Lär dig hur du konfigurerar Apache Ranger-principer för HBase i Azure HDInsight med Enterprise Security Package.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 04592ba307cd696c20778d4a79f03be2eb0ac987
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274391"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>Självstudier: Konfigurera principer för Apache HBase i HDInsight med Enterprise Security Package

Lär dig hur du konfigurerar Apache Ranger-principer för Apache HBase-kluster med Enterprise Security Package (ESP). ESP-kluster är anslutna till en domän så att användare kan autentisera med autentiseringsuppgifter för domänen. I den här självstudien skapar du två Ranger-principer för att begränsa åtkomsten till olika kolumnserier i en HBase-tabell.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa domänanvändare
> * Skapa Ranger-principer
> * Skapa tabeller i ett HBase-kluster
> * Testa Ranger-principer

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure Portal](https://portal.azure.com/).

* Skapa ett [HDInsight HBase-kluster med Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet

1. Anslut till Ranger-administratörsanvändargränssnittet från en webbläsare med hjälp av URL:en `https://<ClusterName>.azurehdinsight.net/Ranger/`. Kom ihåg att ändra `<ClusterName>` till namnet på ditt HBase-kluster.

    > [!NOTE]  
    > Ranger-autentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för Hadoop-kluster. Om du vill förhindra att webbläsare använder cachade Hadoop-autentiseringsuppgifter använder du ett nytt InPrivate-webbläsarfönster för att ansluta till Ranger-administratörsgränssnittet.

2. Logga in med dina administratörsautentiseringsuppgifter för Azure Active Directory (AD). Azure AD-administratörsautentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för HDInsight-kluster eller SSH-autentiseringsuppgifterna för Linux HDInsight-noder.

## <a name="create-domain-users"></a>Skapa domänanvändare

Läs avsnittet om hur du [skapar ett HDInsight-kluster med Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) för att se hur du skapar domänanvändarna **sales_user1** och **marketing_user1**. I ett produktionsscenario kommer domänanvändarna från din Active Directory-klientorganisation.

## <a name="create-hbase-tables-and-import-sample-data"></a>Skapa HBase-tabeller och importera exempeldata

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) för att skapa HBase-tabeller, infoga data och köra frågor mot data. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

### <a name="to-use-the-hbase-shell"></a>Använda HBase-gränssnittet

1. Från SSH kör du följande HBase-kommando:
   
    ```bash
    hbase shell
    ```

2. Skapa HBase-tabellen `Customers` med två kolumnserier: `Name` och `Contact`.

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. Infoga data:
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. Visa tabellens innehåll:
    
    ```hbaseshell
    scan 'Contacts'
    ```
    ![HDInsight Hadoop HBase shell](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Skapa Ranger-principer

Skapa en Ranger-princip för **sales_user1** och **marketing_user1**.

1. Öppna **Ranger-administratörsanvändargränssnittet**. Klicka på **\<ClusterName>_hbase** (Klusternamn) under **HBase**.

   ![Apache Ranger-administratörsanvändargränssnitt](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. Skärmen **Lista över principer** visar alla Ranger-principer som skapats för det här klustret. En förkonfigurerad princip kan visas. Klicka på **Lägg till ny princip**.

    ![Skapa en princip i Apache Ranger-administratörsanvändargränssnittet](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. På skärmen **Skapa princip** anger du följande värden:

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|
   |Principnamn  |  sales_customers_name_contact   |
   |HBase-tabell   |  Kunder |
   |HBase-kolumnserie   |  Namn, Kontakt |
   |HBase-kolumn   |  * |
   |Välj grupp  | |
   |Välj användare  | sales_user1 |
   |Behörigheter  | Läsa |

   Följande jokertecken kan användas i ämnesnamnet:

   * `*` anger noll eller flera förekomster av tecken.
   * `?` anger ett enskilt tecken.

   ![Skapa en princip i Apache Ranger-administratörsanvändargränssnittet](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >Vänta en stund medan Ranger synkroniserar med Azure AD om en domänanvändare inte automatiskt har fyllts i för **Välj användare**.

4. Klicka på **Lägg till** för att spara principen.

5. Klicka på **Lägg till ny princip** och ange sedan följande värden:

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|
   |Principnamn  |  marketing_customers_contact   |
   |HBase-tabell   |  Kunder |
   |HBase-kolumnserie   |  Kontakta |
   |HBase-kolumn   |  * |
   |Välj grupp  | |
   |Välj användare  | marketing_user1 |
   |Behörigheter  | Läsa |

   ![Skapa en princip i Apache Ranger-administratörsanvändargränssnittet](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. Klicka på **Lägg till** för att spara principen.

## <a name="test-the-ranger-policies"></a>Testa Ranger-principerna

Baserat på de konfigurerade Ranger-principerna kan **sales_user1** visa alla data för kolumnerna i båda kolumnserierna `Name` och `Contact`. **marketing_user1** kan endast visa data i kolumnserien `Contact`.

### <a name="access-data-as-salesuser1"></a>Komma åt data som sales_user1

1. Öppna en ny SSH-anslutning till klustret. Använd följande kommando för att logga till klustret:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Använd kommandot kinit för att ändra till kontexten för vår önskade användare.

   ```bash
   kinit sales_user1
   ```

2. Öppna HBase-gränssnittet och söka igenom tabellen `Customers`.

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. Observera att försäljningsanvändaren kan visa alla kolumner i tabellen `Customers`, inklusive de två kolumnerna i kolumnserien `Name` samt de fem kolumnerna i kolumnserien `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketinguser1"></a>Komma åt data som marketing_user1

1. Öppna en ny SSH-anslutning till klustret. Använd följande kommando för att logga in som **marketing_user1**:

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. Använd kommandot kinit för att ändra till kontexten för vår önskade användare

   ```bash
   kinit marketing_user1
   ```

2. Öppna HBase-gränssnittet och söka igenom tabellen `Customers`:

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. Observera att marknadsföringsanvändaren endast kan visa de fem kolumnerna i kolumnserien `Contact`.

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

9. Granska åtkomsthändelserna i Ranger-användargränssnittet.

   ![Principgranskning i Ranger-användargränssnittet](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda det här programmet tar du bort det HBase-kluster som du skapade med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. I rutan **Sök** längst upp skriver du **HDInsight**. 
1. Välj **HDInsight-kluster** under **Tjänster**.
1. I listan över HDInsight-kluster som visas klickar du på **...** intill det kluster som du skapade för den här självstudien. 
1. Klicka på **Ta bort**. Klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Komma igång med en Apache HBase](../hbase/apache-hbase-tutorial-get-started-linux.md)
