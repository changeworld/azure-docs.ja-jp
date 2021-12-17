---
title: Azure Cosmos DB .NET SDK の遅延要求をトラブルシューティングする
description: Azure Cosmos DB .NET SDK を使用する場合の遅延要求を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 06/15/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 16ec94b60288215b55e0332239ba3d603b210673
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114140"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-slow-requests"></a>Azure Cosmos DB .NET SDK の遅延要求を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB の遅延要求は、要求の調整やアプリケーションの設計方法など、複数の理由で発生する可能性があります。 この記事では、この問題のさまざまな根本原因について説明します。 

## <a name="request-rate-too-large-429-throttles"></a>要求率が大きすぎる (429 スロットル)

要求の調整は、遅延要求の最も一般的な理由です。 Azure Cosmos DB では、要求は、データベースまたはコンテナーに割り当てられた RU を超えた場合に調整されます。 SDK には、これらの要求を再試行するロジックが組み込まれています。 [要求レートが大きすぎる場合](troubleshoot-request-rate-too-large.md#how-to-investigate)のトラブルシューティングに関する記事では、要求が調整されるかどうかを確認する方法と、今後これらの問題を回避するためにアカウントをスケーリングする方法について説明しています。

## <a name="application-design"></a>アプリケーションの設計

アプリケーションが SDK のベスト プラクティスに従っていない場合、要求の遅延や失敗の原因となるさまざまな問題が発生する可能性があります。 最適なパフォーマンスを実現するには、[.NET SDK のベスト プラクティス](performance-tips-dotnet-sdk-v3-sql.md)に従ってください。

アプリケーションを開発する場合は、次の点を考慮してください。
* アプリケーションが Azure Cosmos DB アカウントと同じリージョンに存在している。
* SDK インスタンスのシングルトン インスタンス。 SDK には、初期化する必要があるキャッシュが複数ありますが、これらによって最初のいくつかの要求の速度が低下する可能性があります。 
* 直接 + TCP 接続モードを使用する
* 高 CPU 使用率を避ける。 平均ではなく最大 CPU 使用率を確認してください。これは、ほとんどのログ システムの既定値です。 約 40% を超える場合は、待機時間が長くなる可能性があります。


## <a name="capture-the-diagnostics"></a><a name="capture-diagnostics"></a>診断をキャプチャする

`CosmosException` を含む SDK のすべての応答には、Diagnostics プロパティがあります。 このプロパティでは、再試行または一時的な失敗があるかどうかなど、1 つの要求に関連するすべての情報が記録されます。 

診断は文字列として返されます。 文字列は、さまざまなシナリオのトラブルシューティングに対応するために改良されるので、バージョンによって異なります。 SDK の各バージョンでは、文字列の書式設定に重大な変更が加えられる予定です。 破壊的変更を避けるために、文字列を解析しないでください。 次のコード サンプルは、.NET SDK を使用して診断ログを読み取る方法を示しています。

```c#
try
{
    ItemResponse<Book> response = await this.Container.CreateItemAsync<Book>(item: testItem);
    if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan)
    {
        // Log the diagnostics and add any additional info necessary to correlate to other logs 
        Console.Write(response.Diagnostics.ToString());
    }
}catch(CosmosException cosmosException){
    // Log the full exception including the stack trace 
    Console.Write(cosmosException.ToString());
    // The Diagnostics can be logged separately if required.
    Console.Write(cosmosException.Diagnostics.ToString());
}

ResponseMessage response = await this.Container.CreateItemStreamAsync(partitionKey, stream);
if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan || IsFailureStatusCode(response.StatusCode))
{
    // Log the diagnostics and add any additional info necessary to correlate to other logs 
    Console.Write(response.Diagnostics.ToString());
}
```


## <a name="diagnostics-in-version-319-and-higher"></a>バージョン 3.19 以降の診断
JSON 構造体では、SDK のバージョンごとに重大な変更が加えられています。 このため、これを解析するのは安全ではありません。 JSON では、SDK を通過する要求がツリー構造で表されます。 これには、次に示すような重要な項目がいくつか含まれています。

### <a name="cpu-history"></a><a name="cpu-history"></a>CPU の履歴
高い CPU 使用率は、要求遅延の最も一般的な理由です。 最適な待機時間を実現するには、CPU 使用率は 40% ほどである必要があります。 CPU 使用率の (平均ではなく) 最大値を監視するには、間隔として 10 秒を使用します。 CPU スパイクは、要求で 1 つのクエリに対して複数の接続を実行する可能性があるクロスパーティション クエリでは、より一般的です。

エラーに `TransportException` 情報が含まれている場合は、`CPU History` も含まれている可能性があります。

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* CPU 使用率が 70% を超える場合、タイムアウトの原因は CPU の枯渇である可能性があります。 この場合の解決策は、CPU 使用率が高いソースを調査してそれを減らすか、マシンをより大きなリソース サイズにスケーリングすることです。
* CPU 測定が 10 秒ごとに行われていない場合、間隔または測定時間は測定間の時間が長いことを示しています。 このような場合、原因はスレッドの枯渇です。 解決策は、スレッドの枯渇 (ロックされている可能性のあるスレッド) のソースを調査するか、マシンをより大きなリソース サイズにスケーリングすることです。

#### <a name="solution"></a>解答:
SDK を使用するクライアント アプリケーションをスケールアップまたはスケールアウトする必要があります。


### <a name="httpresponsestats"></a><a name="httpResponseStats"></a>HttpResponseStats
HttpResponseStats は、[ゲートウェイ](sql-sdk-connection-modes.md)に送信する要求です。 直接モードでも、SDK ではゲートウェイからすべてのメタデータ情報が取得されます。

要求が遅い場合は、まず、前述のすべての提案事項でも結果が変わらないことを確認します。

それでも遅い場合は、パターンによってさまざまな問題が示されます。

1 つの要求に対する単一ストアの結果

| [要求の数] | シナリオ | 説明 | 
|----------|-------------|-------------|
| 1 つ - すべて | 要求タイムアウトまたは HttpRequestExceptions | [SNAT ポートの枯渇](troubleshoot-dot-net-sdk.md#snat)または時間内に要求を処理するマシン上のリソースの不足を示しています。 |
| 1 つまたは少ない割合 (SLA に違反していない) | すべて | 1 つまたは少ない割合の遅延要求は、いくつかの一時的な問題が原因で発生する可能性があり、これは予想されたものです。 | 
| すべて | すべて | インフラストラクチャまたはネットワークに関する問題を示しています。 |
| SLA 違反 | アプリケーションと SLA に対する変更は削除されない | Azure Cosmos DB サービスに関する問題を示しています。 |

```json
"HttpResponseStats": [
    {
        "StartTimeUTC": "2021-06-15T13:53:09.7961124Z",
        "EndTimeUTC": "2021-06-15T13:53:09.7961127Z",
        "RequestUri": "https://127.0.0.1:8081/dbs/347a8e44-a550-493e-88ee-29a19c070ecc/colls/4f72e752-fa91-455a-82c1-bf253a5a3c4e",
        "ResourceType": "Collection",
        "HttpMethod": "GET",
        "ActivityId": "e16e98ec-f2e3-430c-b9e9-7d99e58a4f72",
        "StatusCode": "OK"
    }
]
```

### <a name="storeresult"></a><a name="storeResult"></a>StoreResult
StoreResult は、TCP プロトコルによる直接モードを使用した、Azure Cosmos DB への 1 つの要求を表します。

それでも遅い場合は、パターンによってさまざまな問題が示されます。

1 つの要求に対する単一ストアの結果

| [要求の数] | シナリオ | 説明 | 
|----------|-------------|-------------|
| 1 つ - すべて | StoreResult に TransportException が含まれている | [SNAT ポートの枯渇](troubleshoot-dot-net-sdk.md#snat)または時間内に要求を処理するマシン上のリソースの不足を示しています。 |
| 1 つまたは少ない割合 (SLA に違反していない) | すべて | 1 つまたは少ない割合の遅延要求は、いくつかの一時的な問題が原因で発生する可能性があり、これは予想されたものです。 | 
| すべて | すべて | インフラストラクチャまたはネットワークに関する問題。 |
| SLA 違反 | 要求に 410 のような失敗エラー コードが複数含まれていて、IsValid が true である | Cosmos DB サービスに関する問題を示しています |
| SLA 違反 | 要求に 410 のような失敗エラー コードが複数含まれていて、IsValid が false である | マシンに関する問題を示しています |
| SLA 違反 | StorePhysicalAddress は同じで、エラー状態コードがない | Cosmos DB サービスに問題がある可能性があります |
| SLA 違反 | StorePhysicalAddress のパーティション ID は同じである一方で、レプリカ ID は異なり、エラー状態コードがない | Cosmos DB サービスに問題がある可能性があります |
| SLA 違反 | StorePhysicalAddress はランダムで、エラー状態コードがない | マシンに関する問題を示しています |

1 つの要求に対して複数の StoreResults:

* 強固な有界整合性制約の整合性には、常に少なくとも 2 つのストア結果があります
* 各 StoreResult の状態コードを確認します。 SDK では、複数の異なる[一時的な障害](troubleshoot-dot-net-sdk-request-timeout.md)で自動的に再試行されます。 SDK は、より多くのシナリオに対応するため、頻繁に改良されています。 

### <a name="rntbdrequeststats"></a><a name="rntbdRequestStats"></a>RntbdRequestStats 
トランスポート層で要求を送受信するさまざまな段階の時間を表示します。

* ChannelAcquisitionStarted: 新しい接続を取得または作成する時間。 多数の異なるリージョンに対して新しい接続を作成できます。 たとえば、接続が予期せず閉じられたり、既存の接続を介して送信された要求が多すぎたりする場合に、新しい接続が作成されます。 
* パイプライン時間は、場合によっては大きな要求を示す大きなポイントです。
* 転送時間が大きく、ネットワークの問題が発生します。 この数値を `BELatencyInMs` と比較します。 BELatencyInMs が小さい場合、時間は Azure Cosmos DB サービスではなく、ネットワーク上で費やされています。
* 受信時間が大きい場合、これはスレッドの枯渇に関する問題を示しています。 これは、応答を受け取ってから結果を返すまでの時間です。

```json
"StoreResult": {
    "ActivityId": "a3d325c1-f4e9-405b-820c-bab4d329ee4c",
    "StatusCode": "Created",
    "SubStatusCode": "Unknown",
    "LSN": 1766,
    "PartitionKeyRangeId": "0",
    "GlobalCommittedLSN": -1,
    "ItemLSN": -1,
    "UsingLocalLSN": false,
    "QuorumAckedLSN": 1765,
    "SessionToken": "-1#1766",
    "CurrentWriteQuorum": 1,
    "CurrentReplicaSetSize": 1,
    "NumberOfReadRegions": 0,
    "IsClientCpuOverloaded": false,
    "IsValid": true,
    "StorePhysicalAddress": "rntbd://127.0.0.1:10253/apps/DocDbApp/services/DocDbServer92/partitions/a4cb49a8-38c8-11e6-8106-8cdcd42c33be/replicas/1p/",
    "RequestCharge": 11.05,
    "BELatencyInMs": "7.954",
    "RntbdRequestStats": [
        {
            "EventName": "Created",
            "StartTime": "2021-06-15T13:53:10.1302477Z",
            "DurationInMicroSec": "6383"
        },
        {
            "EventName": "ChannelAcquisitionStarted",
            "StartTime": "2021-06-15T13:53:10.1366314Z",
            "DurationInMicroSec": "96511"
        },
        {
            "EventName": "Pipelined",
            "StartTime": "2021-06-15T13:53:10.2331431Z",
            "DurationInMicroSec": "50834"
        },
        {
            "EventName": "Transit Time",
            "StartTime": "2021-06-15T13:53:10.2839774Z",
            "DurationInMicroSec": "17677"
        },
        {
            "EventName": "Received",
            "StartTime": "2021-06-15T13:53:10.3016546Z",
            "DurationInMicroSec": "7079"
        },
        {
            "EventName": "Completed",
            "StartTime": "2021-06-15T13:53:10.3087338Z",
            "DurationInMicroSec": "0"
        }
    ],
    "TransportException": null
}
```

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>エラー率が Azure Cosmos DB の SLA に違反している
[Azure サポート](https://aka.ms/azure-support)にお問い合わせください。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。
