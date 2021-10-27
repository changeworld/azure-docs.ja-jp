---
title: Azure Cosmos DB API for Cassandra のレート制限エラーを回避する
description: SSR (サーバー側の再試行) 機能を使用して、Azure Cosmos DB API for Cassandra の操作がレート制限エラーに達しないようにします
author: dileepraotv-github
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/11/2021
ms.author: turao
ms.openlocfilehash: 2e110aeb7cf5395a9aea8d0efae9c0f97b6e74ce
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003877"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-cassandra-operations"></a>Azure Cosmos DB API for Cassandra の操作のレート制限エラーを回避する
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

> [!IMPORTANT]
> Cassandra の Cosmos DB API のサーバー側の再試行を有効にして、レート制限エラーを防止する方法はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

すべてのデータベース操作のコストは Azure Cosmos DB によって正規化され、要求ユニット (RU) によって表されます。 要求ユニットは、Azure Cosmos DB によってサポートされるデータベース操作を実行するために必要な CPU、IOPS、メモリなどのシステム リソースを抽象化する、パフォーマンスの通貨です。

Azure Cosmos DB Cassandra API の操作は、テーブルのスループット制限 (RU 数) を超えると、レート制限 (OverloadedException/429) エラーで失敗することがあります。 これは、[Cassandra API の推奨事項に関するドキュメント](https://devblogs.microsoft.com/cosmosdb/cassandra-api-java/#retry-policy)の再試行ポリシーで説明されているように、クライアント側で処理できます。 レート制限エラーによる障害を処理するためのクライアント再試行ポリシーを実装できない場合は、サーバー側の再試行 (SSR) 機能を使用して、テーブルのスループット制限を超える操作を、短い遅延の後で自動的に再試行できます。 これはアカウント レベルの設定であり、アカウントのすべてのキー スペースとテーブルに適用されます。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. Azure Cosmos DB API for Cassandra アカウントに移動します。

3. **[設定]** セクションの下にある **[機能]** ペインにアクセスします。

4. **[Server-Side Retry]\(サーバー側の再試行\)** を選択します。

5. **[有効化]** をクリックして、アカウント内のすべてのコレクションに対してこの機能を有効にします。

:::image type="content" source="./media/prevent-rate-limiting-errors/prevent-rate-limiting-errors.png" alt-text="Azure Cosmos DB API for Cassandra のサーバー側の再試行機能のスクリーンショット":::

## <a name="use-the-azure-cli"></a>Azure CLI の使用

1. ご利用のアカウントで SSR が既に有効になっているかどうかを確認します。

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

2. ご利用のデータベース アカウント内のすべてのテーブルに対して SSR を **有効にします**。 この変更が有効になるまで、最大 15 分かかる場合があります。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra DisableRateLimitingResponses
   ```

3. 次のコマンドは、機能の一覧から `DisableRateLimitingResponses` を削除することによって、データベース アカウント内のすべてのテーブルに対してサーバー側の再試行を **無効** にします。 この変更が有効になるまで、最大 15 分かかる場合があります。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra
   ```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-are-requests-retried"></a>要求はどのように再試行されますか。

60 秒のタイムアウトに達するまで、要求は継続的に (繰り返し) 再試行されます。 タイムアウトに達すると、クライアントは、読み取りまたは書き込みどちらか該当するタイムアウト エラーを受信します

### <a name="when-is-ssr-most-beneficial"></a>SSR が最も役に立つのはどのようなときですか?

サーバー側の再試行 (SSR) は、1 分未満の短い期間に急激なスパイクが発生し、調整エラーを回避できる場合に最も効果的です。 ワークロードが増加し、指定された RU を常に超えている場合は、SSR はあまり役に立ちません。 RU を適切に増やすことをお勧めします。

### <a name="suggested-client-side-settings"></a>クライアント側ではどのような設定が推奨されますか?

SSR を有効にしたら、クライアント アプリで読み取りタイムアウトをサーバーの再試行の 60 秒より長くする必要があります。 念のため、90 秒にすることをお勧めします。

コード サンプル Driver3
```java
SocketOptions socketOptions = new SocketOptions()
    .setReadTimeoutMillis(90000); 
```
コード サンプル Driver4  
```java
ProgrammaticDriverConfigLoaderBuilder configBuilder = DriverConfigLoader.programmaticBuilder()
    .withDuration(DefaultDriverOption.REQUEST_TIMEOUT, Duration.ofSeconds(90)); 
```

### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>サーバー側の再試行の影響はどのように監視するのですか。

[Cosmos DB メトリック] ウィンドウでは、サーバー側で再試行されたレート制限エラー (429) を確認できます。 SSR が有効になっている場合、これらのエラーはサーバー側で処理され再試行されるため、クライアントには返されません。

[Cosmos DB リソース ログ](../cosmosdb-monitor-resource-logs.md)内で、*estimatedDelayFromRateLimitingInMilliseconds* を含むログ エントリを検索することができます。

### <a name="will-server-side-retry-affect-my-consistency-level"></a>サーバー側の再試行を適用すると、整合性レベルに影響がありますか。

サーバー側の再試行を設定しても、整合性レベルに影響はありません。 要求は、レート制限を受けた場合 (エラー 429)、サーバー側で再試行されます。

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>クライアントに返される可能性のある種類のエラーは、サーバー側の再試行の影響を受けますか。

いいえ。サーバー側の再試行の影響を受けるのは、レート制限エラー (429) のみであり、これらはサーバー側で再試行されます。 この機能を使用すると、クライアント アプリケーション内でレート制限エラーを処理しなくてもよくなります。 [他のエラー](troubleshoot-common-issues.md)はすべて、クライアントに返されます。

## <a name="next-steps"></a>次の手順

一般的なエラーのトラブルシューティングの詳細については、次の記事を参照してください。

* [Azure Cosmos DB の Cassandra 用 API での一般的な問題のトラブルシューティング](troubleshoot-common-issues.md)


Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [Azure Cosmos DB における要求ユニットとスループット](../request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](../how-to-provision-throughput-cassandra.md) 
* [パーティション キーのベスト プラクティス](../cassandra-partitioning.md)

