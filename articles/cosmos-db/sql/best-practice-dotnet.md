---
title: Azure Cosmos DB の .NET SDK v3 のベスト プラクティス
description: Azure Cosmos DB .NET SDK v3 を使用する上でのベスト プラクティスについて説明します
author: StefArroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: esarroyo
ms.openlocfilehash: b07162b95419a73be4bdecc5ff3160ded1c2a0e5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219622"
---
# <a name="best-practices-for-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK のベスト プラクティス
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB .NET SDK を使用する上でのベスト プラクティスについて説明します。 これらのプラクティスに従うことで、待機時間の短縮と高可用性の実現が可能になり、全体的なパフォーマンスを向上させることができます。 

以下のビデオをご覧になり、Cosmos DB エンジニアから .NET SDK の使用方法を学んでください。


> [!VIDEO https://www.youtube.com/embed/McZIQhZpvew?start=118]
>

## <a name="checklist"></a>チェック リスト
|オン  | トピック  |詳細/リンク  |
|---------|---------|---------|
|<input type="checkbox"/> |    SDK バージョン    |   最適なパフォーマンスを実現するために、常に[最新バージョン](sql-api-sdk-dotnet-standard.md)の Cosmos DB SDK を使用します。     |
| <input type="checkbox"/>   |    シングルトン クライアント     |       [パフォーマンスを向上させる](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)には、ご自身のアプリケーションの有効期間中に `CosmosClient` の[単一インスタンス](/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet&preserve-view=true)を使用します。     |
| <input type="checkbox"/>  |     リージョン     |   待機時間を短縮するには、可能な限り、Azure Cosmos DB アカウントと同じ [Azure リージョン](../distribute-data-globally.md)でアプリケーションを実行してください。 2 から 4 のリージョンを有効にし、複数のリージョンでアカウントをレプリケートして、[可用性を最大限に高めます](../distribute-data-globally.md)。 実稼働ワークロードの場合は、[自動フェールオーバー](../how-to-manage-database-account.md#configure-multiple-write-regions)を有効にします。 この構成がない場合、リージョンに接続されていないため手動フェールオーバーは成功せず、書き込みリージョンの停止中は、アカウントで書き込みを利用できなくなる可能性があります。 .NET SDK を使用して複数のリージョンを追加する方法については、[こちら](tutorial-global-distribution-sql-api.md)を参照してください   |
| <input type="checkbox"/>   |   可用性とフェールオーバー     |  v3 SDK で [ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions?view=azure-dotnet&preserve-view=true) または [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion?view=azure-dotnet&preserve-view=true) を設定し、[優先リージョン リスト](./tutorial-global-distribution-sql-api.md?tabs=dotnetv3%2capi-async#preferred-locations)を使用して v2 SDK の [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet&preserve-view=true) を設定します。 フェールオーバー中に、書き込み操作が現在の書き込みリージョンに送信され、すべての読み取りが優先リージョン リスト内の最初のリージョンに送信されます。 リージョン内フェールオーバーのメカニズムの詳細については、[可用性に関するトラブルシューティング ガイド](troubleshoot-sdk-availability.md)を参照してください。 |
|  <input type="checkbox"/> |    CPU     |  クライアント マシン上のリソースが不足しているため、接続/可用性の問題に遭遇することがあります。 Azure Cosmos DB クライアントを実行しているノードで CPU 使用率を監視し、使用率が非常に高い場合はスケールアップ/スケールアウトします。      |
| <input type="checkbox"/>   |    Hosting      |   最適なパフォーマンスを実現するには、可能な限り [Windows 64 ビットのホスト](performance-tips.md#hosting)処理を使用します。       |
| <input type="checkbox"/> |    接続モード    |    最高のパフォーマンスを実現するには、[直接モード](sql-sdk-connection-modes.md)を使用します。  その方法については、[V3 SDK のドキュメント](performance-tips-dotnet-sdk-v3-sql.md#networking)または [V2 SDK のドキュメント](performance-tips.md#networking)を参照してください。|
|<input type="checkbox"/>  |    ネットワーク   | 仮想マシンを使用してアプリケーションを実行する場合は、VM で[高速ネットワーク](../../virtual-network/create-vm-accelerated-networking-powershell.md)を有効にして、高トラフィックが原因で発生するボトルネックを解消し、待機時間や CPU ジッターを減らします。 CPU の最大使用率が 70% 未満であるハイエンドな仮想マシンの使用も検討してください。    |
|<input type="checkbox"/> |  一時的なポートの不足      | スパースまたは散発的な接続の場合は、[`IdleConnectionTimeout`](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout?view=azure-dotnet&preserve-view=true) と [`PortReuseMode`](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode?view=azure-dotnet&preserve-view=true) を `PrivatePortPool` に設定します。 `IdleConnectionTimeout` プロパティ は、未使用の接続を閉じるまでの時間を制御するのに役立ちます。 こうすることで、未使用の接続の数が減少します。 既定では、アイドル状態の接続は無期限に開いた状態になります。 10 分以上の値を指定する必要があります。 推奨値は 20 分から 24 時間です。  `PortReuseMode` プロパティにより、SDK ではさまざまな Azure Cosmos DB の宛先エンドポイントに対して一時的なポートの小さなプールを使用できます。    |
|<input type="checkbox"/> |  Async/Await の使用     |  `Task.Result`、`Task.Wait`、`Task.GetAwaiter().GetResult()` のブロック呼び出しを避けます。 コール スタック全体を非同期にして、[async/await](/dotnet/csharp/programming-guide/concepts/async/) の組み合わせを有効活用する。 多くの同期的なブロッキング呼び出しを行うと、[スレッド プールの枯渇](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall)や応答時間の増加が起こります。 |
|<input type="checkbox"/>  |   エンドツーエンドのタイムアウト | エンドツーエンドのタイムアウトを実行するには、`RequestTimeout` と `CancellationToken` の両方のパラメーターを使用する必要があります。 Cosmos DB のタイムアウトの詳細については、[こちら](troubleshoot-dot-net-sdk-request-timeout.md)を参照してください |
|<input type="checkbox"/>  |   再試行ロジック      |   一時エラーとは、その基になる原因がすぐに自動的に解決されるエラーです。 データベースに接続するアプリケーションは、これらの一時エラーを想定して構築する必要があります。 これに対処するには、アプリケーション エラーとしてユーザーに表示するのではなく、コードに再試行ロジックを実装します。 SDK には、読み取りやクエリの操作など、再試行可能な要求でこれらの一時的なエラーを処理するロジックが組み込まれています。 書き込みはべき等ではないので、SDK では、一時的なエラーでは書き込みが再試行されません。 ユーザーは、SDK を使用して、スロットルの再試行ロジックを構成できます。 再試行の対象となるエラーの詳細については、[こちら](troubleshoot-dot-net-sdk.md#retry-logics)を参照してください |
|<input type="checkbox"/>  |     データベース/コレクション名のキャッシュ    |    構成からデータベースとコンテナーの名前を取得するか、開始時にそれらをキャッシュします。 `ReadDatabaseAsync` や `ReadDocumentCollectionAsync`、また `CreateDatabaseQuery` や `CreateDocumentCollectionQuery` ような呼び出しでは、サービスに対するメタデータ呼び出しが行われ、システムで予約されている RU 制限から消費されます。 また、`CreateIfNotExist` は、データベースの設定に 1 回だけ使用する必要があります。 一般に、これらの操作は、頻繁に実行しないでください。       |
|<input type="checkbox"/> |     一括サポート      |     待機時間を最適化する必要がないシナリオでは、大量のデータをダンプするために、[一括サポート](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/)を有効にすることをお勧めします。    |
| <input type="checkbox"/>  |     並列クエリ     |    Cosmos DB SDK では、クエリの待機時間とスループットを向上させる[クエリの並列実行](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)がサポートされています。  `QueryRequestsOptions` 内の `MaxConcurrency` プロパティ を、使用しているパーティションの数に設定することをお勧めします。 パーティションの数を認識していない場合は、`int.MaxValue` を使用して開始すると、最適な待機時間が得られます。 次に、高 CPU 使用率の問題を回避するために、環境のリソース制限に適合するまで数を減らします。 また、返される結果の予想数に `MaxBufferedItemCount` を設定して、プリフェッチされる結果の数 を制限します。 |
| <input type="checkbox"/> |     パフォーマンス テストのバックオフ      |    アプリケーションでテストを実行する場合は、[`RetryAfter`](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage) の間隔でバックオフを実装する必要があります。 バックオフにより、再試行までの待ち時間を最小限に抑えることができます。   |
|  <input type="checkbox"/>   |   インデックス作成     |   Azure Cosmos DB のインデックス作成ポリシーでは、インデックス作成パス (IndexingPolicy.IncludedPaths および IndexingPolicy.ExcludedPaths) を使用して、インデックス作成に含めたり除外したりするドキュメント パスも指定できます。  書き込みを高速化するには、インデックス作成から未使用のパスを除外ししてください。  SDK を使用してインデックスを作成する方法のサンプルについては、[こちら](performance-tips-dotnet-sdk-v3-sql.md#indexing-policy)を参照してください   |
|  <input type="checkbox"/>   |    ドキュメント サイズ  |    特定の操作の要求の料金は、ドキュメントのサイズに直接関係します。 サイズの大きいドキュメントの操作は、サイズの小さいドキュメントの操作よりもコストがかかるので、ドキュメントのサイズを小さくすることをお勧めします。      |
| <input type="checkbox"/>   |     スレッドまたはタスクの数を増やす    |    Azure Cosmos DB の呼び出しはネットワーク経由で行われるため、クライアント アプリケーションで要求間の待ち時間を最短にするために、要求のコンカレンシーの次数を変えることが必要な場合があります。 たとえば、[.NET のタスク並列ライブラリ](/dotnet/standard/parallel-programming/task-parallel-library-tpl)を使用する場合、Azure Cosmos DB に対する読み取りまたは書き込みのタスクを 100 件単位で作成してください。     |
|  <input type="checkbox"/> |    クエリ メトリックの有効化     |  バックエンド クエリの実行のログ記録を追加する場合は、.NET SDK を使用して SQL クエリ メトリックを有効にできます。 SQL クエリ メトリックを収集する方法については、[こちら](profile-sql-api-query.md)を参照してください    |
|  <input type="checkbox"/>    | SDK のログ   | SDK のログを使用して、追加の診断情報をキャプチャし、待機時間の問題をトラブルシューティングします。  V2 SDK で[診断文字列](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet&preserve-view=true)、または v3 SDK で [`Diagnostics`](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet&preserve-view=true) をログに記録し、サービスに対する現在の要求についての詳細な Cosmos 診断情報を取得します。 使用例として、`Diagnostics.ElapsedTime` が指定されたしきい値を超える場合、例外や完了した操作で診断をキャプチャします。 つまり、SLA が 10 秒の場合、`ElapsedTime` が 10 秒を超えたら診断をキャプチャします。 これらの診断は、パフォーマンス テスト中にのみ使用することをお勧めします。     |

## <a name="best-practices-when-using-gateway-mode"></a>ゲートウェイ モードを使用する場合のベスト プラクティス
ゲートウェイ モードを使用する場合は、ホストごとに `System.Net MaxConnections` を増やします。 ゲートウェイ モードを使用すると、Azure Cosmos DB の要求は HTTPS/REST を介して行われます。 それらは、ホスト名または IP アドレスごとの既定の接続数の上限に従います。 場合によっては、Azure Cosmos DB に対する複数の同時接続をクライアント ライブラリで使用できるよう、`MaxConnections` を高い値 (100 ～ 1,000) に増やす必要があります。 .NET SDK 1.8.0 以降では、`ServicePointManager.DefaultConnectionLimit` の既定値は 50 です。 値を変更するには、`Documents.Client.ConnectionPolicy.MaxConnectionLimit` を高い値に設定します。

## <a name="best-practices-for-write-heavy-workloads"></a>書き込み負荷の高いワークロードのベスト プラクティス
高負荷の作成ペイロードがあるワークロードでは、`EnableContentResponseOnWrite` 要求オプションを `false` に設定します。 サービスは、作成または更新されたリソースを SDK に返さなくなります。 通常、アプリケーションには作成済みのオブジェクトがあるため、サービスから返される必要はありません。 ヘッダー値には、まだ要求の料金と同様にアクセスできます。 応答コンテンツを無効にすると、SDK がメモリを割り当てたり応答の本文をシリアル化したりする必要がなくなるため、パフォーマンスを向上させることができます。 また、ネットワーク帯域幅の使用量も削減され、パフォーマンスがさらに向上します。

## <a name="next-steps"></a>次のステップ
少数のクライアント コンピューターでの高パフォーマンス シナリオで Azure Cosmos DB の評価に使用されるサンプル アプリケーションについては、「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。

スケーリングと高パフォーマンスのためのアプリケーションの設計について詳しくは、「[Azure Cosmos DB でのパーティション分割とスケーリング](../partitioning-overview.md)」をご覧ください。

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください
