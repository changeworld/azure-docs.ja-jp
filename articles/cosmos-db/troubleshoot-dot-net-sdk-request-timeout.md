---
title: .NET SDK の Azure Cosmos DB HTTP 408 または要求タイムアウトに関する問題のトラブルシューティング
description: .NET SDK の要求タイムアウト例外を診断して修正する方法
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09442e01fa160d3851169a51230fa4cbef7e0980
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118571"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Azure Cosmos DB .NET SDK の要求タイムアウトを診断してトラブルシューティングする
HTTP 408 エラーは、タイムアウト制限が発生する前に SDK が要求を完了できなかった場合に発生します。

## <a name="customizing-the-timeout-on-the-azure-cosmos-net-sdk"></a>Azure Cosmos .NET SDK でタイムアウトをカスタマイズする

この SDK には、タイムアウトを制御する方法が 2 つあります。いずれもスコープが異なります。

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout` (または、SDK V2 の場合は `ConnectionPolicy.RequestTimeout`) 構成では、個々のネットワーク要求に影響を与えるタイムアウトを設定できます。  ユーザー開始した操作は複数のネットワーク要求にまたがることがあります (たとえば、調整があります)。この構成は、再試行時、ネットワーク要求ごとに適用されます。 これはエンドツーエンドの操作要求タイムアウトではありません。

### <a name="cancellationtoken"></a>CancellationToken

SDK のすべての非同期操作にオプションの CancellationToken パラメーターがあります。 この [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) は、ネットワーク要求全体かつ操作全体で使用されます。 ネットワーク要求の間、CancellationToken が確認され、関連トークンの有効期間が切れている場合、操作が取り消されます。 CancellationToken は、操作スコープに予想されるおよそのタイムアウトを定義する目的で使用してください。

> [!NOTE]
> CancellationToken は、[取り消しによって無効な状態が引き起こされない](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/)ときに、ライブラリによって取り消しが確認されるメカニズムです。 取り消しに定義されている時間がきた瞬間に操作が取り消されるとは限らず、むしろ、その時間が過ぎた後で、取り消して問題ないときに取り消されます。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、要求タイムアウト例外の既知の原因と解決方法が含まれています。

### <a name="1-high-cpu-utilization-most-common-case"></a>1.CPU 使用率が高い (最も一般的なケース)
最適な待機時間を実現するには、CPU の使用率を約 40% にすることをお勧めします。 CPU 使用率の (平均ではなく) 最大値を監視するには、間隔として 10 秒を使用することをお勧めします。 CPU スパイクは、1 つのクエリに対して複数の接続を実行する可能性があるクロス パーティション クエリでは、より一般的です。

#### <a name="solution"></a>解決方法:
SDK を使用するクライアント アプリケーションをスケールアップ/スケールアウトする必要があります。

### <a name="2-socket--port-availability-might-be-low"></a>2.ソケット/ポートの可用性が低下している可能性がある
Azure で実行されている場合、.NET SDK を使用しているクライアントで Azure SNAT (PAT) ポートの枯渇が発生する可能性があります。

#### <a name="solution-1"></a>解決策 1:
Azure VM で実行している場合は、[SNAT ポートの枯渇に関するガイド](troubleshoot-dot-net-sdk.md#snat)を参照してください。

#### <a name="solution-2"></a>解決策 2:
Azure App Service で実行している場合は、[接続エラーのトラブルシューティングのガイド](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause)に従って、[App Service の診断を利用](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html)してください。

#### <a name="solution-3"></a>解決策 3: 
Azure Functions で実行している場合は、必要なすべてのサービス (Cosmos DB を含む) に対してシングルトン/静的クライアントを管理するための [Azure Functions の推奨事項](../azure-functions/manage-connections.md#static-clients)に従っていることを確認し、関数アプリのホスティングの種類とサイズに基く[サービスの制限](../azure-functions/functions-scale.md#service-limits)を確認してください。

#### <a name="solution-4"></a>解決策 4:
HTTP プロキシを使用する場合は、SDK `ConnectionPolicy` で構成されている接続の数をサポートできることを確認します。
できない場合、接続の問題が発生します。

### <a name="3-creating-multiple-client-instances"></a>3.複数のクライアント インスタンスの作成
複数のクライアント インスタンスを作成すると、接続の競合とタイムアウトの問題を招くおそれがあります。

#### <a name="solution"></a>解決方法:
[パフォーマンスに関するヒント](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)に従って、プロセス全体で単一の CosmosClient インスタンスを使用します。

### <a name="4-hot-partition-key"></a>4.ホット パーティション キー
Azure Cosmos DB は、プロビジョニングされたスループット全体を物理パーティション間で均等に分散します。 ホット パーティションが存在すると、ある物理パーティション上の 1 つ以上の論理パーティション キーによってすべての物理パーティションの RU/秒が消費され、その一方で他の物理パーティション上の RU/秒は未使用のままになります。 症状としては、消費済み RU/秒の合計は、データベースまたはコンテナー上にプロビジョニングされている全体的な RU/秒よりも少なくなりますが、ホット論理パーティション キーに対する要求では、帯域幅調整 (429) が見られます。 [正規化された RU 消費量メトリック](monitor-normalized-request-units.md) を使用して、ワークロードでホット パーティションが発生しているかどうかを確認します。 

#### <a name="solution"></a>解決方法:
要求のボリュームと記憶域を均等に分散する適切なパーティション キーを選択します。 [パーティション キーの変更](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)方法を参照してください。

### <a name="5-high-degree-of-concurrency"></a>5.同時実行の程度が高い
アプリケーションで高レベルの同時実行が行われています。これにより、チャネル上で競合が発生する可能性があります。

#### <a name="solution"></a>解決方法:
SDK を使用するクライアント アプリケーションをスケールアップ/スケールアウトする必要があります。

### <a name="6-large-requests-andor-responses"></a>6.大量の要求または応答、あるいはその両方
要求数や応答数が増大すると、同時実行の程度が比較的低い場合でも、チャネル上でヘッドオブライン ブロッキングが発生し、競合が悪化する可能性があります。

#### <a name="solution"></a>解決方法:
SDK を使用するクライアント アプリケーションをスケールアップ/スケールアウトする必要があります。

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7.エラー率が Cosmos DB SLA に収まっている
アプリケーションでは、一時的なエラーの処理と必要に応じた再試行を実行できる必要があります。 パスの作成時にサービスによって項目が作成されたか否かを知ることはできないため、408 例外は再試行されません。 作成のために同じ項目を再度送信すると、競合例外が発生します。 ユーザー アプリケーションのビジネス ロジックに、競合を処理するためのカスタム ロジックが含まれている場合があります。これにより、既存の項目のあいまいさが解消されたり、作成の再試行と競合したりすることがあります。

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8.エラー率が Cosmos DB SLA に違反している
Azure サポートにお問い合わせください。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK の使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインについて学習する
