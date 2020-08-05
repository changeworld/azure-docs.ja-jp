---
title: .NET SDK の Azure Cosmos DB HTTP 408 または要求タイムアウトに関する問題のトラブルシューティング
description: .NET SDK の要求タイムアウト例外を診断して修正する方法
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 29b0c6237ae04ea5da9ec496498fc7c20890b173
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293906"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Azure Cosmos DB .NET SDK の要求タイムアウトを診断してトラブルシューティングする
HTTP 408 エラーは、タイムアウト制限が発生する前に SDK が要求を完了できなかった場合に発生します。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、要求タイムアウト例外の既知の原因と解決方法が含まれています。

### <a name="1-high-cpu-utilization-most-common-case"></a>1.CPU 使用率が高い (最も一般的なケース)
最適な待機時間を実現するには、CPU の使用率を約 40% にすることをお勧めします。 CPU 使用率の (平均ではなく) 最大値を監視するには、間隔として 10 秒を使用することをお勧めします。 CPU スパイクは、1 つのクエリに対して複数の接続を実行する可能性があるクロス パーティション クエリでは、より一般的です。

#### <a name="solution"></a>解決方法:
SDK を使用するクライアント アプリケーションをスケールアップ/スケールアウトする必要があります。

### <a name="2-socket--port-availability-might-be-low"></a>2.ソケット/ポートの可用性が低下している可能性がある
Azure で実行されている場合、.NET SDK を使用しているクライアントで Azure SNAT (PAT) ポートの枯渇が発生する可能性があります。

#### <a name="solution-1"></a>解決策 1:
[SNAT ポート枯渇時のガイド](troubleshoot-dot-net-sdk.md#snat)に従ってください。

#### <a name="solution-2"></a>解決策 2:
HTTP プロキシを使用する場合は、SDK `ConnectionPolicy` で構成されている接続の数をサポートできることを確認します。
できない場合、接続の問題が発生します。

### <a name="3-creating-multiple-client-instances"></a>3.複数のクライアント インスタンスの作成
複数のクライアント インスタンスを作成すると、接続の競合とタイムアウトの問題を招くおそれがあります。

#### <a name="solution"></a>解決方法:
[パフォーマンスに関するヒント](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)に従って、プロセス全体で単一の CosmosClient インスタンスを使用します。

### <a name="4-hot-partition-key"></a>4.ホット パーティション キー
Azure Cosmos DB は、プロビジョニングされたスループット全体を物理パーティション間で均等に分散します。 ホット パーティションが存在すると、ある物理パーティション上の 1 つ以上の論理パーティション キーによってすべての物理パーティションの RU/秒が消費され、その一方で他の物理パーティション上の RU/秒は未使用のままになります。 症状としては、消費済み RU/秒の合計は、データベースまたはコンテナー上にプロビジョニングされている全体の RU/秒よりも少なくなりますが、ホット論理パーティション キーに対する要求では、引き続き帯域幅調整 (429) が確認されます。 [正規化された RU 消費量メトリック](monitor-normalized-request-units.md)を使用して、ワークロードでホット パーティションが検出されているかどうかを確認します。 

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
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインを確認する