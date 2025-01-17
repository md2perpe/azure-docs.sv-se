---
title: Kopiera data från HDFS med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en källa för molnet eller lokalt HDFS till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: c528f37c8970380678a318ec2d63babd37f89501
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228030"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiera data från HDFS med Azure Data Factory
> [!div class="op_single_selector" title1="Välj versionen av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [Aktuell version](connector-hdfs.md)

Den här artikeln beskrivs hur du kopierar data från HDFS-servern. Läs om Azure Data Factory den [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här HDFS-anslutningsappen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [källa/mottagare matris som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Mer specifikt stöder den här HDFS-anslutningen:

- Kopiering av filer med hjälp av **Windows** (Kerberos) eller **anonym** autentisering.
- Kopiering av filer med hjälp av **webhdfs** protokoll eller **inbyggda DistCp** stöd.
- Kopiera filer som-är eller parsning/genererar filer med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kopiera data från ett HDFS som inte är allmänt tillgänglig, måste du konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](concepts-integration-runtime.md) artikeln om du vill få mer detaljerad information.

> [!NOTE]
> Se till att Integration Runtime har åtkomst till **alla** [nod namnservern]: [name noden port] och [data nodservrar]: [nod dataporten] av Hadoop-kluster. Standard [namn noden port] är 50070 och standardvärdet [data noden port] är 50075.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för HDFS.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för HDFS som länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **Hdfs**. | Ja |
| url |URL: en med HDFS |Ja |
| authenticationType | Tillåtna värden är: **Anonym**, eller **Windows**. <br><br> Att använda **Kerberos-autentisering** HDFS-anslutningstjänsten finns i [i det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) att ställa in din lokala miljö på lämpligt sätt. |Ja |
| userName |Användarnamn för Windows-autentisering. Kerberos-autentisering, ange `<username>@<domain>.com`. |Ja (för Windows-autentisering) |
| password |Lösenordet för Windows-autentisering. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja (för Windows-autentisering) |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel: använder anonym autentisering**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: med hjälp av Windows-autentisering**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. 

- För **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text formatera datauppsättning](#parquet-and-delimited-text-format-dataset) avsnittet.
- För andra format som **ORC/Avro/JSON/binära formatet**, referera till [andra format datauppsättning](#other-format-dataset) avsnittet.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet och avgränsad text format datauppsättning

Kopiera data från HDFS i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artikel om format-baserade datauppsättning och som stöds inställningar. Följande egenskaper har stöd för HDFS under `location` inställningar i formatet-baserade datauppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Egenskapen type under `location` i datauppsättningen måste anges till **HdfsLocation**. | Ja      |
| folderPath | Sökvägen till mappen. Om du vill använda jokertecken för att filtrera mappar hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |
| fileName   | Filnamnet under den angivna folderPath. Om du vill använda jokertecken för att filtrera filerna hoppa över den här inställningen och ange i källinställningar för aktiviteten. | Nej       |

> [!NOTE]
> **Filresursen** typ datauppsättning med Parquet-/ textformat som nämns i nästa avsnitt stöds fortfarande som – för kopiera/Lookup-aktiviteten för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Andra format-datauppsättning

Kopiera data från HDFS i **ORC/Avro/JSON/binära formatet**, stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **FileShare** |Ja |
| folderPath | Sökvägen till mappen. Jokerteckenfilter stöds, tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska filnamnet har jokertecken eller den här escape-tecken i. <br/><br/>Exempel: rootfolder/undermappen/se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). |Ja |
| fileName |  **Namn eller jokertecken-filtret** för den eller filerna under den angivna ”folderPath”. Om du inte anger ett värde för den här egenskapen datauppsättningen pekar på alla filer i mappen. <br/><br/>För filter tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. |Nej |
| modifiedDatetimeStart | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Tänk på dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| modifiedDatetimeEnd | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br/><br/> Tänk på dataförflyttning serverns prestanda påverkas genom att aktivera den här inställningen när du vill filen filter från stora mängder filer. <br/><br/> Egenskaperna kan vara NULL som innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde.| Nej |
| format | Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia).<br/><br/>Om du vill parsa filer med ett visst format stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej (endast för binär kopia scenario) |
| compression | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbaste**. |Nej |

>[!TIP]
>Kopiera alla filer i en mapp genom att ange **folderPath** endast.<br>Om du vill kopiera en enstaka fil med ett givet namn, ange **folderPath** med mappdelen och **fileName** med filnamnet.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger **folderPath** med mappdelen och **fileName** med jokertecken-filtret.

**Exempel:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av HDFS-källa.

### <a name="hdfs-as-source"></a>HDFS som källa

- För kopia från **Parquet och avgränsat textformat**, referera till [Parquet och avgränsad text format källa](#parquet-and-delimited-text-format-source) avsnittet.
- För kopia från andra format som **ORC/Avro/JSON/binära formatet**, referera till [annan format källa](#other-format-source) avsnittet.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet och avgränsad text format källa

Kopiera data från HDFS i **Parquet eller avgränsat textformat**, referera till [Parquet-format](format-parquet.md) och [avgränsat textformat](format-delimited-text.md) artikel om format-baserade aktiviteten kopieringskälla och inställningar som stöds. Följande egenskaper har stöd för HDFS under `storeSettings` inställningar i formatet-baserade kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Egenskapen type under `storeSettings` måste anges till **HdfsReadSetting**. | Ja                                           |
| recursive                | Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursiv har angetts till true och mottagaren är en filbaserad store, en tom mapp eller undermapp inte kopieras eller skapat i mottagaren. Tillåtna värden är **SANT** (standard) och **FALSKT**. | Nej                                            |
| wildcardFolderPath       | Sökvägen till mappen med jokertecken för att filtrera källa mappar. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i. <br>Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Nej                                            |
| wildcardFileName         | Filnamn med jokertecken under den angivna folderPath/wildcardFolderPath filter källfilerna för. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar noll eller valfritt tecken); Använd `^` att undvika om din faktiska mappnamn har jokertecken eller den här escape-tecken i.  Se fler exempel i [mapp och fil Filterexempel](#folder-and-file-filter-examples). | Ja om `fileName` har inte angetts i datauppsättningen |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: Senast ändrades. Filerna markerade om deras tid för senaste ändring är inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd`. Tid som tillämpas på UTC-tidszonen i formatet ”2018-12-01T05:00:00Z”. <br> Egenskaperna kan vara NULL vilket innebär att inga filfilter för attributet som ska användas för datauppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är NULL, innebär det att filer vars senaste ändrade attribut är större än eller lika med datum/tid-värde väljs.  När `modifiedDatetimeEnd` har datetime-värde men `modifiedDatetimeStart` är NULL, innebär det att filer vars senaste ändrade attributet är mindre än det markerade datetime-värde. | Nej                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Nej                                            |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej                                            |

> [!NOTE]
> För Parquet/avgränsat textformat **FileSystemSource** typen kopiera aktivitetskälla som nämns i nästa avsnitt stöds fortfarande som – är för bakåtkompatibilitet. Du rekommenderas för att använda den nya modellen framöver och ADF redigering Användargränssnittet har ändrats till att generera dessa nya typer.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Andra format-källa

Kopiera data från HDFS i **ORC/Avro/JSON/binära formatet**, följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **HdfsSource** |Ja |
| recursive | Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. Obs när rekursiv har angetts till true och mottagare är filbaserade store, tom mapp/underanvändningsfall-folder kan inte kopieras/skapas vid mottagare.<br/>Tillåtna värden är: **SANT** (standard), **FALSKT** | Nej |
| distcpSettings | Egenskapsgruppen när du använder HDFS DistCp. | Nej |
| resourceManagerEndpoint | Yarn Resource Manager-slutpunkten | Ja om du använder DistCp |
| tempScriptPath | En mappsökväg som används för att lagra temporära DistCp-kommandoskript. Skriptfilen genereras av Data Factory och tas bort när kopieringsjobb är klar. | Ja om du använder DistCp |
| distcpOptions | Ytterligare alternativ till kommandot DistCp. | Nej |
| maxConcurrentConnections | Antal anslutningar för att ansluta till storage store samtidigt. Ange bara när du vill begränsa samtidiga anslutningen till datalagret. | Nej |

**Exempel: HDFS-källan i kopieringsaktiviteten använda DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Läs mer om hur du använder DistCp för att kopiera data från HDFS effektivt från nästa avsnitt.

### <a name="folder-and-file-filter-examples"></a>Mapp och fil filter-exempel

Det här avsnittet beskriver resultatet av sökvägen och namnet på mappen med jokertecken filter.

| folderPath | fileName             | recursive | Käll-mappen struktur och filtrera resultatet (filer i **fetstil** hämtas) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (tom, Använd standard) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (tom, Använd standard) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Använd DistCp för att kopiera data från HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) är ett Hadoop interna kommandoradsverktyg göra distribuerad kopia i ett Hadoop-kluster. När du kör en kommandot Distcp, kommer den först lista över alla filer kopieras, skapar flera jobb för kartan i Hadoop-kluster och varje jobb karta gör binär kopia från källa till mottagare.

Kopiera aktivitet support via DistCp för att kopiera filer som – i Azure Blob (inklusive [mellanlagrad kopiering](copy-activity-performance.md) eller Azure Data Lake Store, i vilket fall det helt utnyttja ditt kluster fördelar i stället för som körs på den lokala Integration Runtime . Det ger bättre genomströmning för kopiera särskilt om klustret är ett kraftfullt verktyg. Utifrån din konfiguration i Azure Data Factory kan Kopieringsaktivitet automatiskt skapar en kommandot distcp, skicka Hadoop-klustret och övervaka kopieringsstatusen.

### <a name="prerequisites"></a>Nödvändiga komponenter

Använd DistCp för att kopiera filer som – är från HDFS till Azure Blob (inklusive mellanlagrad kopiering) eller Azure Data Lake Store och kontrollera att ditt Hadoop-kluster uppfyller nedan krav:

1. MapReduce och Yarn tjänster är aktiverade.
2. Yarn-versionen är 2.5 eller senare.
3. HDFS-servern är integrerad med ditt datalager som mål - Azure Blob eller Azure Data Lake Store:

    - Azure Blob-filsystem stöds internt sedan Hadoop 2.7. Du behöver bara ange jar-sökväg i Hadoop env config.
    - Azure Data Lake Store FileSystem är paketerat med början från Hadoop 3.0.0-alpha1. Om ditt Hadoop-kluster är lägre än den här versionen, måste du manuellt importera ADLS relaterade jar-paket (azure-datalake-store.jar) i klustret från [här](https://hadoop.apache.org/releases.html), och anger jar-sökvägen i Hadoop env config.

4. Förbered en tillfällig mapp för i HDFS. Den här tillfällig mapp används för att lagra DistCp shell-skript, så att den tar upp utrymme i KB-nivå.
5. Kontrollera att det användarkonto som anges i HDFS-länkade tjänsten har behörighet att en) skickar programmet i Yarn; (b) har behörighet att skapa undermapp, läsa/skriva filer under ovan temp-mappen.

### <a name="configurations"></a>Konfigurationer

Se DistCp säkerhetsrelaterade konfigurationer och exemplen i [HDFS som källa](#hdfs-as-source) avsnittet.

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använda Kerberos-autentisering för HDFS-anslutningstjänsten

Det finns två alternativ för att konfigurera den lokala miljön för att använda Kerberos-autentisering i HDFS-anslutningsappen. Du kan välja som passar bättre ditt ärende.
* Alternativ 1: [Ansluta till lokal Integration Runtime-dator i Kerberos-sfär](#kerberos-join-realm)
* Alternativ 2: [Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Alternativ 1: Ansluta till lokal Integration Runtime-dator i Kerberos-sfär

#### <a name="requirements"></a>Krav

* Lokal Integration Runtime-dator måste ansluta till Kerberos-sfären och det går inte att ansluta till en Windows-domän.

#### <a name="how-to-configure"></a>Så här konfigurerar du

**På datorn för lokal Integration Runtime:**

1.  Kör den **Ksetup** verktyg för att konfigurera Kerberos KDC-servern och sfär.

    Datorn måste konfigureras som en medlem i en arbetsgrupp, eftersom en Kerberos-sfär skiljer sig från en Windows-domän. Detta kan uppnås genom att Kerberos-sfären och lägga till en KDC-server på följande sätt. Ersätt *REALM.COM* med din egen respektive sfär vid behov.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Starta om** datorn när du kör kommandona 2.

2.  Verifiera konfigurationen med **Ksetup** kommando. Resultatet bör se ut som:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**I Azure Data Factory:**

* Konfigurera HDFS anslutningsapp med **Windows-autentisering** tillsammans med dina Kerberos-huvudnamn och lösenord för att ansluta till HDFS-datakälla. Kontrollera [HDFS länkade tjänstegenskaper](#linked-service-properties) avsnittet på konfigurationsinformation.

### <a name="kerberos-mutual-trust"></a>Alternativ 2: Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär

#### <a name="requirements"></a>Krav

*   Lokal Integration Runtime-dator måste ansluta till en Windows-domän.
*   Du behöver behörighet att uppdatera inställningarna för den domänkontrollanten.

#### <a name="how-to-configure"></a>Så här konfigurerar du

> [!NOTE]
> Ersätt REALM.COM och AD.COM i följande självstudie med dina egna respektive sfär och domänkontrollern efter behov.

**På KDC-servern:**

1. Redigera KDC-konfigurationen i **krb5.conf** filen så att KDC förtroende för Windows-domänen för följande konfiguration. Som standard konfigurationen finns i **/etc/krb5.conf**.

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   **Starta om** KDC-tjänsten efter konfigurationen.

2. Förbereda ett huvudnamn med namnet **krbtgt/REALM.COM\@AD.COM** i KDC-server med följande kommando:

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. I **hadoop.security.auth_to_local** HDFS-tjänstkonfigurationen Lägg till `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**På domänkontrollanten:**

1.  Kör följande **Ksetup** kommandon för att lägga till en sfär post:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Upprätta förtroende från Windows-domän att Kerberos-sfär. [lösenord] är lösenordet för huvudnamn **krbtgt/REALM.COM\@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Välj krypteringsalgoritmen som används i Kerberos.

    1. Gå till Server Manager > hantering av Grupprincip > domän > grupprincipobjekt > standard eller aktiva domänprincip och redigera.

    2. I den **Redigeraren för Grupprinciphantering** popup-fönster, går du till Datorkonfiguration > Principer > Windows-inställningar > säkerhetsinställningar > lokala principer > säkerhetsalternativ, och konfigurera **nätverk säkerhet: Konfigurera krypteringstyper som tillåts för Kerberos**.

    3. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC. Ofta, kan du helt enkelt välja alla alternativ.

        ![Config-krypteringstyperna för Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Använd **Ksetup** kommando för att ange krypteringsalgoritmen som ska användas på specifika området.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Skapa mappningen mellan domänkontot och Kerberos-huvudnamn för att kunna använda Kerberos-huvudnamn i Windows-domän.

    1. Starta administrativa verktyg > **Active Directory-användare och datorer**.

    2. Konfigurera avancerade funktioner genom att klicka på **visa** > **avancerade funktioner**.

    3. Leta upp det konto som du vill skapa mappningar och högerklicka för att visa **namnmappningar** > klickar du på **Kerberos-namn** fliken.

    4. Lägg till ett huvudnamn för från området.

        ![Mappa säkerhetsidentitet](media/connector-hdfs/map-security-identity.png)

**På datorn för lokal Integration Runtime:**

* Kör följande **Ksetup** kommandon för att lägga till en sfär-post.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**I Azure Data Factory:**

* Konfigurera HDFS anslutningsapp med **Windows-autentisering** tillsammans med dina domänkonto eller Kerberos-huvudnamn att ansluta till HDFS-datakälla. Kontrollera [HDFS länkade tjänstegenskaper](#linked-service-properties) avsnittet på konfigurationsinformation.


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
