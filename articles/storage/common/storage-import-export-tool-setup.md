---
title: Hur du konfigurerar verktyget Azure Import/Export | Microsoft Docs
description: Lär dig hur du ställer in förberedelsen av enheten och reparera verktyg för tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: d2ce6c409ae9cbf99589d11dfc850e2324d1b0c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320473"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Konfigurera Azure Import/Export-verktyget

Microsoft Azure Import/Export-verktyget är den enhet förberedelser och reparera verktyg som du kan använda med Microsoft Azure Import/Export-tjänsten. Du kan använda verktyget för följande funktioner:

* Innan du skapar ett importjobb, kan du använda det här verktyget för att kopiera data till hårddiskar som du ska skicka till ett Azure-datacenter.
* När importen har slutförts kan använda du det här verktyget för att reparera alla blobbar som var skadade saknades eller är i konflikt med andra blobar.
* Du kan använda det här verktyget för att reparera alla filer som var skadad eller saknas på enheter när du har fått enheterna från en slutförd export-jobbet.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du är **förbereda enheter** för ett importjobb följande krav måste uppfyllas:

* Du måste ha en aktiv Azure-prenumeration.
* Prenumerationen måste innehålla ett lagringskonto med tillräckligt mycket ledigt utrymme att lagra filerna som du tänker importera.
* Du behöver minst en av storage-kontonycklar för åtkomst.
* Behöver du en dator (”kopiera-dator”) med Windows 7, Windows Server 2008 R2 eller ett nyare Windows-operativsystem installerat.
* .NET Framework 4 måste installeras på datorn kopia.
* BitLocker måste vara aktiverat på Kopiera-datorn.
* Du behöver en eller flera tomt 2,5-tums eller 3,5-tums SATA II eller III eller SSD-hårddiskar är anslutna till kopiera machine.
* De filer du vill importera måste vara tillgänglig från kopia-dator, oavsett om de är på en nätverksresurs eller en lokal hårddisk.

Om du försöker **reparera en import** som misslyckades delvis måste du:

* Kopiera loggfiler
* Lagringskontonyckeln

Om du försöker **reparera en export** som misslyckades delvis måste du:

* Kopiera loggfiler
* Manifestet filer (valfritt)
* Lagringskontonyckeln

## <a name="installing-the-azure-importexport-tool"></a>Installera verktyget Azure Import/Export

Först [ladda ned verktyget Azure Import/Export](https://www.microsoft.com/download/details.aspx?id=55280) och packa upp den till en katalog på din dator, till exempel `c:\WAImportExport`.

Verktyget Azure Import/Export består av följande filer:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

Därefter öppnar ett kommandotolksfönster i **administratörsläge**, och ändra till den katalog som innehåller de extrahera filerna.

I utdata-hjälpen för kommandot Kör verktyget (`WAImportExport.exe`) utan parametrar:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Nästa steg

* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Förhandsgranska diskanvändning för ett exportjobb](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Granska jobbstatus med kopiera loggfiler](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
