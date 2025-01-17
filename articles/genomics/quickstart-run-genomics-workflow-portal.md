---
title: 'Snabbstart: Köra ett arbetsflöde genom Microsoft Genomics'
description: I den här snabbstarten får du se hur du läser in indata i Azure Blob Storage och kör ett arbetsflöde genom Microsoft Genomics-tjänsten.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 4c8d488021ca12a704a5c0a06bb0c491588bcaed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781586"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Snabbstart: Kör ett arbetsflöde genom Microsoft Genomics-tjänsten

I den här snabbstarten får du se hur du läser in indata i Azure Blob Storage och kör ett arbetsflöde genom Microsoft Genomics-tjänsten. Microsoft Genomics är en skalbar, säker tjänst för sekundär analys som snabbt kan bearbeta ett genom, från råläsningar till produktion av anpassade läsningar och variantanrop. 

Kom igång med några få steg: 
1.  Konfigurera: Skapa ett Microsoft Genomics-konto via Azure-portalen och installera Microsoft Genomics Python-klienten i din lokala miljö. 
2.  Ladda upp indata: Skapa ett Microsoft Azure Storage-konto via Azure-portalen och ladda upp indatafiler. Indatafilerna ska vara parkopplade slutläsningar (FASTQ- eller BAM-filer).
3.  Kör: Använd kommandoradsgränssnittet i Microsoft Genomics för att köra arbetsflöden genom Microsoft Genomics-tjänsten. 

Mer information om Microsoft Genomics finns i [Vad är Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Konfigurera: Skapa ett Microsoft Genomics-konto på Azure-portalen

Skapa ett Microsoft Genomics-konto genom att gå till på [Azure-portalen](https://portal.azure.com/#create/Microsoft.Genomics). Om du ännu inte har en Azure-prenumeration kan du skapa en innan du skapar ett Microsoft Genomics-konto. 

![Microsoft Genomics på Azure-portalen](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics på Azure-portalen")



Skapa ditt Genomics-konto med följande information (se föregående bild): 

 |**Inställning**          |  **Föreslaget värde**  | **Fältbeskrivning** |
 |:-------------       |:-------------         |:----------            |
 |Prenumeration         | Ditt prenumerationsnamn|Detta är faktureringsenheten för dina Azure-tjänster – mer information om din prenumeration finns under [Prenumerationer](https://account.azure.com/Subscriptions) |      
 |Resursgrupp       | MinResursgrupp       |  Resursgrupper gör att du kan gruppera flera Azure-resurser (lagringskonto, Genomics-konto, o.s.v.) i en enda grupp för enkel hantering. Mer information finns i [Resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Information om giltiga resursgruppnamn finns under [Namngivningsregler](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) |
 |Kontonamn         | MittGenomicsKonto     |Välj ett unikt konto-ID. Se [Namngivningsregler](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) för giltiga namn |
 |Location                   | Västra USA 2                    |    Tjänsten är tillgänglig i USA, västra 2, Europa, västra och Sydostasien |




Du kan klicka på Aviseringar i det översta verktygsfältet för att övervaka distributionsprocessen.
![Microsoft Genomics-aviseringar](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box.png "Microsoft Genomics-aviseringar")



## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Konfigurera: Installera Microsoft Genomics Python-klienten

Användarna måste installera både Python och Microsoft Genomics Python-klienten i sin lokala miljö. 

### <a name="install-python"></a>Installera Python

Microsoft Genomics Python-klienten är kompatibel med Python 2.7. 12 eller senare 2.7.xx-version; 2.7.15 är den senaste versionen när det här dokumentet skrivs; 2.7.14 är den rekommenderade versionen. Du hittar nedladdningen [här](https://www.python.org/downloads/). 

OBS! Python 3.x är inte kompatibelt med Python 2.7.xx.  MSGen är ett Python 2.7-program. När du kör MSGen kontrollerar du att din aktiva Python-miljö använder en 2.7.xx-version av Python. Du kan få ett felmeddelande om du försöker använda MSGen med en 3.x-version av Python.


### <a name="install-the-microsoft-genomics-client"></a>Installera Microsoft Genomics-klienten

Använda Python-pip för att installera Microsoft Genomics-klienten `msgen`. I följande anvisningar förutsätts Python redan finnas i systemsökvägen. Om du har problem med att pip-installationen inte identifieras måste du lägga till Python och skriptundermappen systemsökvägen.


```
pip install --upgrade --no-deps msgen
pip install msgen
```


Om du inte vill installera `msgen` som ett systemtäckande, binärt och systemändrande Python-paket kan du använda `–-user`-flaggan med `pip`.
Om du använder den paketbaserade installationen eller setup.py installeras alla nödvändiga paket. Annars är de nödvändiga paketen för msgen 

 * [Azure-storage](https://pypi.python.org/pypi/azure-storage). 
 * [Requests](https://pypi.python.org/pypi/requests). 


Du kan installera de här paketen med `pip`, `easy_install` eller via `setup.py`-standardprocedurer. 





### <a name="test-the-microsoft-genomics-client"></a>Testa Microsoft Genomics-klienten
Ladda ned konfigurationsfilen från ditt Genomics-konto för att testa Microsoft Genomics-klienten. Navigera till ditt Genomics-konto genom att klicka på **Alla tjänster** längst ned till vänster, filtrera och välja Genomics-konton.


![Filtrera efter Microsoft Genomics på Azure-portalen](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Filtrera efter Microsoft Genomics på Azure-portalen")



Välj det Genomics-konto du just skapade, gå till **Åtkomstnycklar** och ladda ned konfigurationsfilen.

![Ladda ned konfigurationsfilen från Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Ladda ned konfigurationsfilen från Microsoft Genomics")


Kontrollera att Microsoft Genomics Python-klienten fungerar med följande kommando


```
msgen list -f “<full path where you saved the config file>”
```

## <a name="create-a-microsoft-azure-storage-account"></a>Skapa ett Microsoft Azure Storage-konto 
I Microsoft Genomics-tjänsten förväntas indata lagras som blockblobar i ett Azure Storage-konto. Utdatafilerna skrivs också som blockblobar till en container som angetts av användaren i ett Azure Storage-konto. In- och utdata kan finnas i olika lagringskonton.
Om du redan har data i ett Azure Storage-konto behöver du bara se till att det finns på samma plats som Genomics-kontot. Annars debiteras du för utgående trafik när du kör Genomics-tjänsten. Om du ännu inte har ett Microsoft Azure Storage-konto måste du skapa ett och ladda upp data. Du hittar mer information om Azure Storage-konton [här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), bland annat om vad ett lagringskonto är och vilka tjänster det omfattar. Om du vill skapa ett Microsoft Azure Storage-konto ska du gå till [Azure-portalen](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM ).  

![Bladet Skapa lagring](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade.png "Bladet Skapa lagring")

Konfigurera ditt lagringskonto med följande information (se föregående bild). Använda de flesta standardalternativen för ett lagringskonto, men ange att kontot är blob-lagring och inte för generell användning. Blob-lagring kan vara 2–5 gånger snabbare för ned- och uppladdningar.  Standarddistributionsmodellen Resource Manager rekommenderas.  


 |**Inställning**          |  **Föreslaget värde**  | **Fältbeskrivning** |
 |:-------------------------       |:-------------         |:----------            |
 |Prenumeration         | Din Azure-prenumeration |Mer information om din prenumeration finns i [Prenumerationer](https://account.azure.com/Subscriptions) |      
 |Resursgrupp       | MinResursgrupp       |  Du kan välja samma resursgrupp som ditt Genomics-konto. Information om giltiga resursgruppnamn finns under [Namngivningsregler](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) |
 |Lagringskontots namn         | MittLagringskonto     |Välj ett unikt konto-ID. Se [Namngivningsregler](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) för giltiga namn |
 |Location                  | Västra USA 2                  | Använd samma plats som platsen för Genomics-kontot för att minska kostnaderna för utgående trafik och minska svarstiden.  | 
 |Prestanda                  | Standard                   | Standardinställningen är Standard. Mer information om standard- och premiumlagringskonton finns i [introduktion till Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Typ av konto       | Blob Storage       |  Blob-lagring kan vara 2–5 gånger snabbare än lagring generell användning för ned- och uppladdningar. |
 |Replikering                  | Lokalt redundant lagring                  | Med lokalt redundant lagring replikeras dina data i datacentret i den region där du har skapat ditt lagringskonto. Mer information finns i [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Åtkomstnivå                  | Frekvent                   | Frekvent åtkomst indikerar att objekten på lagringskontot kommer att användas oftare.    |


Skapa sedan lagringskontot genom att klicka på `Review + create`. Precis som du gjorde när du skapade Genomics-konto kan du klicka på Aviseringar den i övre menyraden för att övervaka distributionsprocessen. 


## <a name="upload-input-data-to-your-storage-account"></a>Ladda upp indata till ditt lagringskonto

I Microsoft Genomics-tjänsten förväntas parkopplade slutläsningar som indatafiler. Du kan välja att antingen ladda upp dina egna data eller utforska med offentligt tillgängliga exempeldata som du får. Om du vill använda offentligt tillgängliga exempeldata finns de här:


[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)


I ditt lagringskonto måste du skapa en blob-container för dina indata och en andra blob-container för dina utdata.  Ladda upp indata till blob-containern för indata. Olika verktyg kan användas för att göra detta, bland annat [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), [blobporter](https://github.com/Azure/blobporter) eller [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 



## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Köra ett arbetsflöde genom Microsoft Genomics-tjänsten med hjälp av Python-klienten 

Om du vill köra ett arbetsflöde genom Microsoft Genomics-tjänsten ska du redigera filen config.txt och ange lagringscontainern för dina in- och utdata.
Öppna filen config.txt som du laddade ned från Genomics-kontot. De avsnitt du måste ange är din prenumerationsnyckel och de sex elementen längst ned, lagringskontonamnet, nyckel och containernamn för både indata och utdata. Du kan hitta den här informationen på portalen genom att gå till **Åtkomstnycklar** för ditt lagringskonto, eller direkt från Azure Storage Explorer.  


![Genomics-konfiguration](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomics-konfiguration")


Om du vill köra GATK4 ställer du in parametern `process_name` på gatk4 eller gatk4-promo. Mer information om GATK4-befordran finns på [den här sidan](https://aka.ms/msgatk4).

Som standard visar Genomics-tjänsten VCF-filer. Om du vill ha gVCF-utdata i stället för VCF-utdata (motsvarar `-emitRefConfidence` i GATK 3.x och `emit-ref-confidence` i GATK 4.x) lägger du till parametern `emit_ref_confidence` till `config.txt` och ger den värdet `gvcf`, vilket visas i bilden ovan.  Om du vill ändra tillbaka till VCF-utdata tar du antingen bort den från filen `config.txt` eller ställer in parametern `emit_ref_confidence` på `none`. 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Skicka arbetsflödet till Microsoft Genomics-tjänsten

Använd Microsoft Genomics Python-klienten för att skicka ditt arbetsflöde med följande kommando:


```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```


Du kan visa statusen för dina arbetsflöden med hjälp av följande kommando: 
```python
msgen list -f c:\temp\config.txt 
```


När arbetsflödet har slutförts kan du visa utdatafilerna i ditt Azure Storage-konto i containern för utdata som du konfigurerade. 


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du laddat upp exempelindata i Azure Storage och skickat ett arbetsflöde till Microsoft Genomics-tjänsten via `msgen` Python-klienten. Mer information om andra typer av indatafiler som kan användas med Microsoft Genomics-tjänsten finns på följande sidor: [par FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Flera FASTQ eller BAM](quickstart-input-multiple.md). Du kan även utforska den här självstudien med hjälp av vår [Azure-självstudie som anteckningsbok.](https://aka.ms/genomicsnotebook)
