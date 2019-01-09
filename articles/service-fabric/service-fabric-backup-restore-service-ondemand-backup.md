---
title: Azure Service Fabric でのオンデマンド バックアップ | Microsoft Docs
description: Service Fabric のバックアップと復元機能を使用し、必要に応じてアプリケーションのデータをバックアップします。
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730583"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Azure Service Fabric でのオンデマンド バックアップ

障害やデータ損失のシナリオに対応するため、Reliable Stateful サービスと Reliable Actors のデータをバックアップできます。

Service Fabric は、[データの定期的なバックアップ](service-fabric-backuprestoreservice-quickstart-azurecluster.md)および必要に応じたデータのバックアップの機能を備えています。 オンデマンド バックアップは、基になるサービスまたはその環境での計画的な変更による "_データ損失_/_データ破損_" からの保護として役に立ちます。

オンデマンド バックアップ機能は、サービスまたはサービス環境に関連する手動でトリガーされる操作の前に、サービスの状態をキャプチャするのに便利です。 サービス バイナリの変更 (つまり、サービスのアップグレードまたはダウン グレード) などです。アプリケーションのコードのバグによるデータの破損からデータを保護します。

## <a name="triggering-on-demand-backup"></a>オンデマンド バックアップをトリガーする

オンデマンド バックアップには、バックアップ ファイルをアップロードするためのストレージの詳細が必要です。 オンデマンド バックアップは、定期的バックアップ ポリシーで指定されているストレージ、またはオンデマンド バックアップ要求で指定されたストレージで実行されます。

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>定期的バックアップ ポリシーで指定されているストレージへのオンデマンド バックアップ

Reliable Stateful サービスまたは Reliable Actors のパーティションでは、定期的バックアップ ポリシーで指定されているストレージへの必要に応じた追加のバックアップを要求できます。 

次のケースは、「[Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)」で説明されているサンプルの継続です。パーティションではバックアップ ポリシーが有効になっており、Azure Storage で必要な頻度でバックアップされます。

パーティションの ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` のオンデマンド バックアップは、[BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API によってトリガーできます。 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[オンデマンド バックアップの進行状況](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)は、[GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API で追跡できます。

### <a name="on-demand-backup-to-specified-storage"></a>指定したストレージへのオンデマンド バックアップ

Reliable Stateful サーバーまたは Reliable Actors のパーティションとストレージ情報を指定して、オンデマンド バックアップを要求でいます。 オンデマンド バックアップ要求の一部として、ストレージ情報を指定する必要があります。

次に示すように、パーティションの ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` のオンデマンド バックアップは、[BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API と Azure Storage 情報によってトリガーできます。

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

[オンデマンド バックアップの進行状況](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)は、[GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API で追跡できます。


## <a name="tracking-on-demand-backup-progress"></a>オンデマンド バックアップの進行状況の追跡

Reliable Stateful サービスまたは Reliable Actors のパーティションで受け付けられるオンデマンド バックアップ要求は、一度に 1 つだけです。 別の要求は、現在のオンデマンド バックアップ要求が完了した場合にのみ受け付けられます。 

パーティションが異なれば、同時に複数のオンデマンド バックアップ要求をトリガーできます。

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

オンデマンド バックアップ要求の進行状況は、次のいずれかの状態です。

* **受け付け済み** - バックアップはパーティションで開始され、進行中です。
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **成功/失敗/タイムアウト** - 要求されたオンデマンド バックアップは、次の状態のいずれかで完了する可能性があります。 各状態の重要性と応答の詳細は次のとおりです。

    * **成功** - "_成功_" バックアップ状態は、パーティションの状態が正常にバックアップされたことを示します。 応答では、パーティションの __BackupEpoch__ および __BackupLSN__ と UTC 時刻が提供されます。
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **エラー** - "_エラー_" バックアップ状態は、パーティションの状態のバックアップ中にエラーが発生したことを示します。 エラーの原因は応答で示されています。
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **タイムアウト** - "_タイムアウト_" バックアップ状態は、パーティション状態のバックアップを指定された期間 (既定値は 10 分) で作成できなかったことを示します。 このシナリオでは、オンデマンド バックアップ要求でさらに大きい [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) を指定して新しいバックアップ要求を開始することをお勧めします。

        ```
        BackupState             : Timeout
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
        ```

## <a name="next-steps"></a>次の手順
- [定期的なバックアップの構成について](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [バックアップと復元用の REST API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

