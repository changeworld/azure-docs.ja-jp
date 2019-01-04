---
title: Azure Service Fabric でのバックアップの復元 | Microsoft Docs
description: Service Fabric の定期バックアップと復元機能を使用して、アプリケーション データのバックアップからデータを復元できます。
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730726"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>Azure Service Fabric でのバックアップの復元


Service Fabric の Reliable Stateful サービスと Reliable Actors では、要求と応答、または完全なトランザクションを超えて変更可能な信頼できる状態を維持できます。 災害が原因でステートフル サービスの長時間のダウンや情報の損失が発生した場合、再開後にサービスの提供を続行するためには、最新の受け入れられるバックアップの状態を復元する必要があります。

たとえば、次のような状況を防ぐために、サービスのデータをバックアップすることが望まれます。

- Service Fabric クラスター全体が永久に失われた場合。 **(ディザスター リカバリーの場合 - DR)**
- サービス パーティションのレプリカの過半数の完全な損失。 **(データ損失の場合)**
- 状態が誤って削除されたり、破損したりするなどの管理エラー。 例: 十分な権限を持つ管理者が誤ってサービスを削除した。 **(データ損失の場合)**
- データの破損を引き起こすサービスのバグ。 たとえば、データの破損は、サービス コードのアップグレードにより、Reliable Collection に不正なデータが書き込まれ始めると起こる可能性があります。 このような場合は、コードとデータの両方を、以前の状態に復元する必要があります。 **(データ破損の場合)**


## <a name="prerequisites"></a>前提条件
* 復元をトリガーするには、_Fault Analysis Service (FAS)_ がクラスターで有効になっている必要があります
* 復元するバックアップが、_バックアップ復元サービス (BRS)_ によって取得されている必要があります
* 復元は、パーティションでのみトリガーできます。

## <a name="triggering-restore"></a>復元をトリガーする

次のシナリオのいずれかで復元を実行できます。 
* "_ディザスター リカバリー_" (DR) の場合のデータ復元
* "_データ破損/データ損失_" の場合のデータ復元



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>"_ディザスター リカバリー_" (DR) の場合のデータ復元
Service Fabric クラスター全体が失われた場合は、Reliable Stateful サービスと Reliable Actors のパーティションのデータを別のクラスターに復元できます。 目的のバックアップは、[バックアップ ストレージ詳細での GetBackupAPI](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation) の列挙から選択できます。 アプリケーション、サービス、またはパーティションのバックアップを列挙できます。

失われたクラスターが、「[Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)」で説明されているクラスターとします。このクラスターは、`SampleApp` がデプロイされていて、バックアップ ポリシーが有効になっており、バックアップは Azure Storage で行われました。 


次の PowerShell スクリプトを実行して REST API を呼び出し、失われた Service Fabric クラスターの `SampleApp` アプリケーション内のすべてのパーティションに対して作成されたバックアップを列挙します。 列挙 API で使用可能なバックアップを列挙するには、アプリケーションのバックアップが格納されているストレージの情報が必要です。 

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
上記を実行したときのサンプル出力:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```



復元をトリガーするには、対象のバックアップを選択する必要があります。 現在のディザスター リカバリー (DR) の目的のバックアップを、次のバックアップにしましょう

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

復元 API に対して、__BackupId__ と __BackupLocation__ の詳細を提供する必要があります。 [パーティション構成](service-fabric-concepts-partitioning.md#get-started-with-partitioning)に従って、代わりのクラスターのパーティションを選択する必要があります。 失われた元のクラスターでのパーティション構成に従って、別のクラスターからバックアップを復元する先のパーティションを選択するのは、ユーザーの責任になります。

代替クラスターでのパーティション ID が `1c42c47f-439e-4e09-98b9-88b8f60800c6` であるものとします。これは、"_範囲パーティション分割 (UniformInt64Partition)_" に対する上位キーと下位キーを比較することによって、元のクラスターのパーティション ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` にマップします。

"_名前付きパーティション分割_" では、名前の値を比較して、代替クラスターの対象パーティションが識別されます。

復元は、次の [Restore API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition) によって、バックアップ クラスターのパーティションに対して要求されます

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
復元の進行状況は[追跡](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)できます

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>"_データ破損/データ損失_" の場合のデータ復元

Reliable Stateful サービスと Reliable Actors のパーティションの "_データ損失_" または "_データ破損_" の場合は、任意の選択したバックアップに復元できます。 次のケースは、「[Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)」で説明されているサンプルの継続です。パーティションではバックアップ ポリシーが有効になっており、Azure Storage で必要な頻度でバックアップされます。 

目的のバックアップを、[GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups) の出力から選択します。 このシナリオでは、バックアップは過去に同じクラスターから生成されます。
復元をトリガーするには、一覧から対象のバックアップを選択する必要があります。 現在の "_データ損失_ / _データ破損_" の対象バックアップを次のバックアップにしましょう

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

復元 API に対して、__BackupId__ と __BackupLocation__ の詳細を提供する必要があります。 クラスターでバックアップが有効になっているので、Service Fabric の "_バックアップ復元サービス (BRS)_" では、関連付けられているバックアップ ポリシーから適切なストレージの場所が識別されます。

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

復元の進行状況は[追跡](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)できます


## <a name="tracking-restore-progress"></a>復元の進行状況を追跡する

Reliable Stateful サービスまたは Reliable Actors のパーティションで受け付けられる復元要求は、一度に 1 つだけです。 別の要求は、現在の復元要求が完了した場合にのみ受け付けられます。 パーティションが異なれば、同時に複数の復元要求をトリガーできます。

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

復元要求は次の順序で進行します

1. __受け付け済み__ - "_受け付け済み_" の復元状態は、正しい要求パラメーターで要求が開始されたことを示します。
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __進行中__ - "_進行中_" の復元状態は、要求で指定されたバックアップでパーティションの復元が行われていることを示します。 パーティションは "_データ損失_" 状態を報告します。
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __成功__/ __失敗__/ __タイムアウト__ - 要求された復元は、次の状態のいずれかで完了する可能性があります。 各状態の重要性と応答の詳細は次のとおりです。
       
    * __成功__ - "_成功_" の復元状態は、パーティションの状態が回復されたことを示します。 応答では、パーティションの RestoreEpoch および RestordLSN と UTC 時刻が提供されます。 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __エラー__ - "_エラー_" の復元状態は、復元要求の失敗を示します。 エラーの原因は要求で示されています。
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __タイムアウト__ - "_タイムアウト_" の復元状態は、要求がタイムアウトしたことを示します。 さらに大きい [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) で新しく復元を要求することをお勧めします。既定のタイムアウトは 10 分です。 再度復元を要求する前に、パーティションがデータ損失状態ではなくなっていることを確認することをお勧めします。
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>自動復元

Service Fabric クラスター内の Reliable Stateful サービスおよび Reliable Actors 用のパーティションを、"_自動復元_" するように構成できます。 バックアップ ポリシーを作成するときに、ポリシーの `AutoRestore` を _true_ に設定できます。  パーティションの "_自動復元_" を有効にすると、データの損失が報告された場合、最新のバックアップからデータが復元されます。
 
 [バックアップ ポリシーでの自動復元の有効化](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[RestorePartition API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[GetPartitionRestoreProgress API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>次の手順
- [定期的なバックアップの構成について](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [バックアップと復元用の REST API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
