---
title: Azure Cosmos DB .NET SDK の使用時の問題を診断しトラブルシューティングする
description: クライアント側のログや他のサード パーティ製ツールなどの機能を使って、.NET SDK 使用時の Azure Cosmos DB の問題を特定、診断、およびトラブルシューティングします。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/06/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 55c462795b29cd678a5fd7816211bce720d554e1
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170360"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK の使用時の問題を診断しトラブルシューティングする

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [非同期の Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

この記事では、Azure Cosmos DB SQL API アカウントで [.NET SDK](sql-api-sdk-dotnet.md) を使用するときの一般的な問題、回避策、診断手順、およびツールについて説明します。
.NET SDK には、Azure Cosmos DB SQL API にアクセスするためのクライアント側の論理表現が用意されています。 この記事では、問題が発生した場合に役立つツールとアプローチについて説明します。

## <a name="checklist-for-troubleshooting-issues"></a>問題をトラブルシューティングするためのチェックリスト
運用環境にアプリケーションを移行する前に、次のチェックリストを検討してください。 チェックリストを使用すればと、発生する可能性のある一般的な問題のいくつかを防止できます。 問題が発生したときにもすばやく診断できます。

*    最新の [SDK](sql-api-sdk-dotnet-standard.md) を使用します。 プレビュー SDK は運用環境で使用しないでください。 これにより、既に修正されている既知の問題の発生が防止されます。
*    [パフォーマンスに関するヒント](performance-tips.md)を確認し、推奨される方法に従います。 これは、スケーリング、待機時間、その他のパフォーマンス上の問題の防止に役立ちます。
*    問題のトラブルシューティングに役立つ SDK のログ機能を有効にします。 ログ機能を有効にするとパフォーマンスに影響することがあるため、問題のトラブルシューティング時にのみ有効にすることをお勧めします。 次のログを有効にできます。
    *    Azure portal を使用して[メトリックを記録します](monitor-accounts.md)。 ポータルのメトリックは Azure Cosmos DB テレメトリを表示します。これは、問題が Azure Cosmos DB に対応したものか、クライアント側から発生したものかを判断する場合に役立ちます。
    *    ポイント操作応答から、V2 SDK では[診断文字列](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)、V3 SDK では[診断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)をログに記録します。
    *    すべてのクエリ応答からの [SQL クエリ メトリック](sql-api-query-metrics.md)を記録します 
    *    [SDK ログ記録]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)の設定に従います

この記事の[一般的な問題と対処法](#common-issues-workarounds)のセクションを確認します。

アクティブに監視されている [GitHub の問題セクション](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)を調べます。 回避策が既に提出済みの同様の問題がないか確認します。 ソリューションが見つからなかった場合は、GitHub の問題を提出してください。 緊急の問題のサポートのティックを開くことができます。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>一般的な問題と対処法

### <a name="general-suggestions"></a>一般的な推奨事項
* 可能なときはいつでも、Azure Cosmos DB アカウントと同じ Azure リージョンでアプリを実行します。 
* クライアント マシン上のリソースが不足しているため、接続/可用性の問題に遭遇することがあります。 Azure Cosmos DB クライアントを実行しているノードの CPU 使用率を監視し、高負荷で実行している場合にはスケール アップ/アウトを実行することをお勧めします。

### <a name="check-the-portal-metrics"></a>ポータルのメトリックを確認する
[ポータルのメトリック](monitor-accounts.md)を確認すると、クライアント側の問題であるか、サービスに問題があるかの判断に役立ちます。 たとえば、要求が調整されていることを意味する、高いレートのレート制限された要求 (HTTP 状態コード 429) がメトリックに含まれていれば、[要求率が大きすぎる] セクションを確認してください。 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>要求タイムアウト
RequestTimeout は、通常ダイレクト/TCP を使用するときに発生しますが、ゲートウェイ モードで起きることがあります。 以下のエラーは、一般的な既知の原因と、問題の解決方法に関する推奨事項です。

* CPU 使用率が高くなっているため、待ち時間または要求タイムアウト、あるいはその両方が発生しています。 顧客がホスト マシンをスケールアップしてリソースを増やすか、複数のマシンに負荷を分散できます。
* ソケット・ポートの可用性が低下している可能性があります。 Azure で実行されている場合、.NET SDK を使用しているクライアントで Azure SNAT (PAT) ポートの枯渇が発生する可能性があります。 この問題が発生する可能性を低くするには、最新バージョンの 2.x または 3.x の .NET SDK を使用します。 これが、常に最新の SDK バージョンを実行することをお勧めする理由のひとつです。
* 複数の DocumentClient インスタンスを作成すると、接続の競合およびタイムアウトの問題を招くおそれがあります。 [パフォーマンスに関するヒント](performance-tips.md)に従って、プロセス全体で単一の DocumentClient インスタンスを使用します。
* コレクションのプロビジョニングが不十分であり、バックエンドが要求を調整し、クライアントが内部的に再試行するので、ユーザーは待ち時間または要求タイムアウトの増大を経験することがあります。 [ポータルのメトリック](monitor-accounts.md)を確認します。
* Azure Cosmos DB は、プロビジョニングされたスループット全体を物理パーティション間で均等に分散します。 ポータルのメトリックを調べて、ワークロードでホットな[パーティション キー](partition-data.md)が発生しているかどうかを確認します。 これにより、消費済みスループットの集計 (RU/秒) がプロビジョニングされた RU より低く表示されますが、単一パーティションの消費済みスループット (RU/秒) はプロビジョニングされたスループットを超過します。 
* さらに、2.0 SDK は、ダイレクト/TCP 接続にチャネル セマンティクスを追加します。 同時に複数の要求に 1 つの TCP 接続が使用されます。 これは特定の場合に 2 つの問題を招くことがあります。
    * 同時実行の程度が高くなると、チャネル上で競合が発生することがあります。
    * 要求数や応答数が増大すると、同時実行の程度が比較的低い場合でも、チャネル上でヘッドオブライン ブロッキングが発生し、競合が悪化する可能性があります。
    * これらの 2 つのカテゴリのどちらかに当てはまる場合 (または高い CPU 使用率が疑われる場合)、以下の軽減策が考えられます。
        * アプリケーションのスケール アップ/アウトを試してください。
        * さらに、[トレース リスナー](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md)を通じて SDK ログをキャプチャし、詳細を取得できます。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>長いネットワーク待ち時間
長いネットワーク待ち時間を識別するには、V2 SDK では[診断文字列](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet)、V3 SDK では[診断](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)を使用します。

[タイムアウト](#request-timeouts)が存在せず、`ResponseTime` と `RequestStartTime` の差に長い待ち時間が明らかに存在する単一の要求が診断で示される場合、次のようになります (この例では >300 ミリ秒)。

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

この待ち時間には、以下のような複数の原因が考えられます。

* アプリケーションが Azure Cosmos DB アカウントと同じリージョンで実行されていない。
* [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) または [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 構成が不正確であり、アプリが現在実行されている別のリージョンに接続しようとしている。
* 大量のトラフィックが原因で、ネットワーク インターフェイスにボトルネックが発生している可能性がある。 アプリケーションが Azure Virtual Machines で実行されている場合は、次のような回避策が考えられます。
    * [高速ネットワークを有効にした仮想マシン](../virtual-network/create-vm-accelerated-networking-powershell.md)を使用することを検討します。
    * [既存の仮想マシンで高速ネットワーク](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)を有効にします。
    * [よりハイエンドな仮想マシン](../virtual-machines/windows/sizes.md)を使用することを検討します。

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) ポート不足

[パブリック IP アドレスを使用しない Azure Virtual Machines](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) にアプリをデプロイした場合、既定では、[Azure SNAT ポート](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)によって VM 外の任意のエンドポイントへの接続が確立されます。 VM から Azure Cosmos DB エンドポイントへの許可される接続の数は、[Azure SNAT 構成](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)によって制限されます。 このような状況では、接続の調整、接続の終了、または上記の[要求タイムアウト](#request-timeouts)が発生する可能性があります。

 Azure SNAT ポートが使用されるのは、プライベート IP アドレスを持つ VM がパブリック IP アドレスに接続しようとしている場合に限られます。 Azure SNAT の制限を回避するには、次の 2 つの回避策があります (アプリケーション全体で 1 つのクライアント インスタンスを既に使用している場合)。

* Azure Virtual Machines 仮想ネットワークのサブネットに Azure Cosmos DB サービス エンドポイントを追加します。 詳細については、[Azure 仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。 

    サービス エンドポイントが有効になると、要求はパブリック IP から Azure Cosmos DB に送信されなくなります。 代わりに、仮想ネットワークとサブネット ID が送信されます。 この変更により、パブリック IP のみが許可された場合はファイアウォール ドロップが発生することがあります。 ファイアウォールを使用している場合、サービス エンドポイントを有効にするときに、[Virtual Network ACL](../virtual-network/virtual-networks-acl.md) を使用してファイアウォールにサブネットを追加します。
* [Azure VM にパブリック IP](../load-balancer/troubleshoot-outbound-connection.md#assignilpip) を割り当てます。

### <a name="http-proxy"></a>HTTP プロキシ
HTTP プロキシを使用する場合は、SDK `ConnectionPolicy` で構成されている接続の数をサポートできることを確認します。
できない場合、接続の問題が発生します。

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>要求率が大きすぎる
"要求率が大きすぎる" またはエラー コード 429 は、消費済みスループット (RU/秒) が [プロビジョニング スループット](set-throughput.md)を超過したため、要求が調整されていることを示します。 SDK は、指定された[再試行ポリシー](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)に基づいて要求を自動的に再試行します。 このエラーが頻繁に発生する場合、コレクションでスループットを増やすことを検討してください。 [ポータルのメトリック](use-metrics.md)を調べて、429 エラーが発生しているかどうかを確認してください。 [パーティション キー](partitioning-overview.md#choose-partitionkey)を調べて、ストレージおよび要求ボリュームが均等に分散されていることを確認してください。 

### <a name="slow-query-performance"></a>低速なクエリ パフォーマンス
[クエリ メトリック](sql-api-query-metrics.md)は、クエリがほとんどの時間を費やしている箇所を特定する場合に役立ちます。 クエリ メトリックから、バックエンドとクライアントでどれだけの時間が費やされているかを確認できます。
* バックエンド クエリが短時間で返され、クライアント上で多くの時間が費やされた場合は、マシン上の負荷を調べてください。 十分なリソースがなく、応答の処理にリソースが利用できるようになるまで SDK が待機している可能性があります。
* バックエンド クエリが低速である場合、[クエリを最適化](optimize-cost-queries.md)して、現在の[インデックス作成ポリシー](index-overview.md)を調べてみてください 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401:HTTP 要求で見つかった MAC 署名は、計算された署名と同じではありません
次の 401 エラー メッセージが表示された場合:"HTTP 要求で見つかった MAC 署名は、計算された署名と同じではありません。" これは、次のシナリオで発生する可能性があります。

1. キーがローテーションされたが、[ベスト プラクティス](secure-access-to-data.md#key-rotation)に従っていなかった。 通常は、これが該当します。 Cosmos DB アカウントのキーのローテーションには、Cosmos DB アカウントのサイズに応じて、数秒から数日かかる場合があります。
   1. 401 MAC 署名は、キーのローテーションの直後に見られ、最終的には変更なしに停止します。 
2. アプリケーションでキーが正しく構成されていないため、キーがアカウントと一致しない。
   1. 401 MAC 署名の問題は、すべての呼び出しで一貫して発生します
3. コンテナーの作成で競合状態がある。 コンテナーの作成が完了する前に、アプリケーション インスタンスがコンテナーにアクセスしようとしています。 これに対する最も一般的なシナリオは、アプリケーションが実行されている場合に、アプリケーションの実行中にコンテナーが削除され、同じ名前で再作成されるというものです。 SDK は新しいコンテナーを使用しようとしますが、コンテナーの作成はまだ進行中であるため、キーがありません。
   1. 401 MAC 署名の問題は、コンテナーの作成直後に見られ、コンテナーの作成が完了するまでしか発生しません。
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>HTTP エラー 400。 要求ヘッダーのサイズが長すぎます。
 ヘッダーのサイズが大きくなりすぎて、最大許容サイズを超えています。 常に最新の SDK を使用することをお勧めします。 少なくともバージョン [3.x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) または [2.x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md) を使用してください。これらは、ヘッダー サイズのトレースを例外メッセージに追加します。

原因:
 1. セッション トークンが大きくなりすぎた。 セッション トークンは、コンテナー内のパーティション数が増加するにつれて増大します。
 2. 継続トークンが大きくなりすぎた。 クエリごとに、継続トークンのサイズは異なります。
 3. セッション トークンと継続トークンの組み合わせが原因で発生した。

解決方法:
   1. [パフォーマンスのヒント](performance-tips.md)に従い、直接 + TCP の接続モードにアプリケーションを変換します。 直接 + TCP には、HTTP のようなヘッダー サイズ制限がないため、この問題を回避できます。
   2. セッション トークンが原因である場合、一時的な軽減策は、アプリケーションを再起動することです。 アプリケーション インスタンスを再起動すると、セッション トークンがリセットされます。 再起動後に例外が止まった場合は、セッション トークンが原因であることが確認できます。 最終的には、例外の原因となるサイズに戻ります。
   3. アプリケーションを直接 + TCP に変換できず、セッション トークンが原因である場合は、クライアントの[整合性レベル](consistency-levels.md)を変更することで軽減策を実行できます。 セッション トークンは、Cosmos DB の既定値であるセッションの整合性にのみ使用されます。 その他の整合性レベルでは、セッション トークンは使用されません。 
   4. 直接 + TCP にアプリケーションを変換できず、継続トークンが原因である場合は、ResponseContinuationTokenLimitInKb オプションを設定してみてください。 このオプションは、v2 の場合は FeedOptions に、v3 では QueryRequestOptions にあります。

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[要求率が大きすぎる]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
