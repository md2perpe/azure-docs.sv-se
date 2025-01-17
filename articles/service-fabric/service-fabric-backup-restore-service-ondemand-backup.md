---
title: Säkerhetskopiering på begäran i Azure Service Fabric | Microsoft Docs
description: Säkerhetskopiera och återställa funktionen i Service Fabric för att säkerhetskopiera dina programdata på basis av behov.
services: service-fabric
documentationcenter: .net
author: aagup
manager: chackdan
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: bed3402de83984cae9134fe44058980ec18861b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413945"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Säkerhetskopiering på begäran i Azure Service Fabric

Du kan säkerhetskopiera data för tillståndskänsliga Reliable services och Reliable Actors till adress katastrof eller data går förlorade.

Azure Service Fabric har funktioner för den [regelbunden säkerhetskopiering av data](service-fabric-backuprestoreservice-quickstart-azurecluster.md) och säkerhetskopiering av data på basis av behov. Säkerhetskopiering på begäran är användbart eftersom det skyddar mot _dataförlust_/_skadade data_ på grund av planerade ändringar i den underliggande tjänsten eller dess miljö.

Säkerhetskopiering på begäran-funktionerna är användbara för att samla in tillståndet för tjänsterna innan du utlöser manuellt en tjänst eller en tjänståtgärd för miljön. Exempel: Om du gör en ändring i binärfilerna för när du uppgraderar eller nedgraderar tjänsten. I detta fall är kan säkerhetskopiering på begäran skydda data mot korruption av programmet kod buggar.
## <a name="prerequisites"></a>Nödvändiga komponenter

- Installera Microsoft.ServiceFabric.Powershell.Http modulen [förhandsgranskning] för configuration anrop.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

- Se till att klustret är anslutna med hjälp av den `Connect-SFCluster` kommandot innan du gör några konfigurationsbegäran med Microsoft.ServiceFabric.Powershell.Http-modulen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggering-on-demand-backup"></a>Aktiverar säkerhetskopiering på begäran

Säkerhetskopiering på begäran kräver lagringsinformation för att ladda upp säkerhetskopior. Du anger på begäran till säkerhetskopieringsplatsen i principen för regelbunden säkerhetskopiering eller i en begäran om säkerhetskopiering på begäran.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Säkerhetskopiering på begäran till lagring som anges av en princip för regelbunden säkerhetskopiering

Du kan konfigurera principen för regelbunden säkerhetskopiering för att använda en partition av en tillförlitlig tillståndskänslig tjänst eller tillförlitliga aktörer för extra säkerhetskopiering på begäran till lagring.

Nedan visas några exempel är fortsättningen på scenariot i [aktiverar regelbunden säkerhetskopiering för tillförlitlig tillståndskänslig tjänst och Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). I detta fall använder du aktiverar en princip för säkerhetskopiering att använda en partition och en säkerhetskopia som uppstår på en set-frekvens i Azure Storage.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av Powershell

Använd den [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API för att ställa in utlösa för säkerhetskopiering på begäran för partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Använd den [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API för att aktivera spårning för den [på begäran-säkerhetskopiering](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Säkerhetskopiering på begäran till angiven storage

Du kan begära säkerhetskopiering på begäran för en partition av en tillförlitlig tillståndskänslig tjänst eller tillförlitliga aktörer. Ange information för lagring som en del av begäran om säkerhetskopiering på begäran.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av Powershell

Använd den [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API för att ställa in utlösa för säkerhetskopiering på begäran för partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Inkludera följande information i Azure Storage:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Du kan använda den [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API för att konfigurera spårning för den [på begäran-säkerhetskopiering](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

## <a name="tracking-on-demand-backup-progress"></a>Spårning på begäran-säkerhetskopiering

En partition av en tillförlitlig tillståndskänslig tjänst eller en Reliable Actor accepterar endast en säkerhetskopiering på begäran-begäran i taget. En annan begäran kan accepteras endast efter den aktuella säkerhetskopiering på begäran-begäran har slutförts.

Olika partitioner kan utlösa en säkerhetskopiering på begäran-begäranden på samma gång.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell med Microsoft.ServiceFabric.Powershell.Http-modulen

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>REST-anrop med hjälp av Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Säkerhetskopiering på begäran-begäranden kan ha följande tillstånd:

- **Godkänt**: Säkerhetskopieringen har startats på partitionen och håller på att skapas.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Lyckade**, **fel**, eller **Timeout**: En begärda säkerhetskopiering på begäran kan utföras på något av följande tillstånd:
  - **Lyckade**: En _lyckades_ säkerhetskopiering tillstånd anger att partition tillståndet har säkerhetskopierats. Svaret innehåller _BackupEpoch_ och _BackupLSN_ för partitionen tillsammans med tiden i UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Fel**: En _fel_ säkerhetskopiering tillstånd anger att ett fel uppstod vid säkerhetskopiering av partitionens tillstånd. Orsaken till felet anges i svaret.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**: En _Timeout_ säkerhetskopiering tillstånd anger att det inte gick att skapa systemtillståndet partition inom en viss tid. Standardvärdet för timeout är 10 minuter. Starta en ny säkerhetskopiering begäran med på begäran med större [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) i det här scenariot.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Nästa steg

- [Förstå periodiska säkerhetskopieringskonfiguration](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore REST API-referens](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
