---
title: Azure Cosmos DB の Cassandra API の一般的なエラーをトラブルシューティングする
description: この記事では、Azure Cosmos DB の Cassandra API の一般的な問題とそのトラブルシューティング方法について説明します。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: e81ab2539527c9d5c5cf2c6bff77fd19887103cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967355"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB の Cassandra API の一般的な問題をトラブルシューティングする

[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

[Azure Cosmos DB](./introduction.md) の Cassandra API は、オープンソースの Apache Cassandra データベースに[ワイヤ プロトコルのサポート](cassandra-support.md)を提供する互換性レイヤーです。

この記事では、Azure Cosmos DB の Cassandra API を使用するアプリケーションの一般的なエラーと解決策について説明します。 お客様のエラーが記載されておらず、[Cassandra でサポートされる操作](cassandra-support.md)を実行するときにエラーが発生するが、ネイティブの Apache Cassandra の使用時にはエラーが存在しない場合は、[Azureサポート リクエストを作成してください](../azure-portal/supportability/how-to-create-azure-support-request.md)。

>[!NOTE]
>フル マネージド クラウドネイティブ サービスとして、Azure Cosmos DB は、Cassandra API に[可用性、スループット、および整合性に対する保証](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)を提供します。 また Cassandra API は、メンテナンス不要のプラットフォーム操作とダウンタイム不要の修正プログラム適用を容易にします。
>
>Apache Cassandra の以前の実装では、これらの保証はできないため、Cassandra API バックエンド操作の多くは Apache Cassandra とは異なります。 一般的なエラーを回避するために、特定の設定とアプローチをお勧めします。

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

このエラーは最上位レベルのラッパー例外であり、考えられる原因と内部例外が多数あり、その多くがクライアントに関連している可能性があります。

一般的な原因と解決策:

- **Azure LoadBalancers のアイドル タイムアウト**: この問題は、`ClosedConnectionException` のように発生する場合もあります。 この問題を解決するには、ドライバーのキープアライブ設定を行い (「[Java ドライバーのキープアライブを有効にする](#enable-keep-alive-for-the-java-driver)」を参照)、お使いのオペレーティング システムのキープアライブ設定値を引き上げるか、または [Azure Load Balancer でアイドル タイムアウトを調整します](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。

- **クライアント アプリケーション リソースの枯渇**: クライアント マシンに要求を完了するための十分なリソースがあることを確認します。

## <a name="cant-connect-to-a-host"></a>ホストに接続できない

"Cannot connect to any host, scheduling retry in 600000 milliseconds." (どのホストにも接続できません。600000 ミリ秒で再試行をスケジュールします) というエラーが表示されることがあります。

このエラーは、クライアント側でのソース ネットワーク アドレス変換 (SNAT) の枯渇が原因である可能性があります。 この問題を解決するには、[アウトバウンド接続での SNAT](../load-balancer/load-balancer-outbound-connections.md) に関するページの手順に従ってください。

また、このエラーは、Azure ロード バランサーで既定で 4 分間のアイドル タイムアウトが発生する、アイドル タイムアウトの問題である場合もあります。 [ロード バランサーのアイドル タイムアウト](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)に関するページを参照してください。 [Java ドライバーのキープアライブを有効にし](#enable-keep-alive-for-the-java-driver)、オペレーティング システムの `keepAlive` 間隔を 4 分未満に設定してください。

## <a name="overloadedexception-java"></a>OverloadedException (Java)

使用された要求ユニットの合計数が、キースペースまたはテーブルでプロビジョニングされた要求ユニットの数よりも多いため、要求が調整されています。

Azure portal からキースペースまたはテーブルに割り当てられたスループットのスケーリング (「[Azure Cosmos DB Cassandra API アカウントをエラスティックにスケーリングする](manage-scale-cassandra.md)」を参照) または再試行ポリシーの実装を検討してください。

Java の場合は、[v3.x ドライバー](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)と [v4.x ドライバー](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)の再試行サンプルをご覧ください。 「[Azure Cosmos Cassandra Extensions for Java](https://github.com/Azure/azure-cosmos-cassandra-extensions)」もご覧ください。

### <a name="overloadedexception-despite-sufficient-throughput"></a>スループットが十分であるにもかかわらず発生する OverloadedException

要求の量または消費された要求ユニット コストに対して十分なスループットがプロビジョニングされているにもかかわらず、システムは要求を調整しているように見えます。 原因として、次の 2 つが考えられます。

- **スキーマ レベルの操作**: Cassandra API は、スキーマレベルの操作 (CREATE TABLE、ALTER TABLE、DROP TABLE) に対してシステム スループット予算を実装します。 実稼働システムでのスキーマ操作には、この予算で十分です。 ただし、スキーマ レベルの操作の数が多い場合は、この制限を超える可能性があります。

  この予算はユーザーが制御できるものではないため、実行するスキーマ操作の数を減らすことを検討してください。 そのアクションで問題が解決されない場合、またはワークロードに対して実行可能ではない場合は、[Azure サポート リクエストを作成](../azure-portal/supportability/how-to-create-azure-support-request.md)してください。

- **データ スキュー**: スループットが Cassandra API でプロビジョニングされている場合は、物理パーティション間で均等に分割され、各物理パーティションに上限があります。 1 つの特定のパーティションから大量のデータを挿入または照会する場合、そのテーブルに大量の全体的なスループット (要求ユニット) をプロビジョニングしているにもかかわらず、レートが制限される可能性があります。

  データ モデルを確認し、ホット パーティションを発生させる可能性のある過度なスキューがないことを確認してください。

## <a name="intermittent-connectivity-errors-java"></a>断続的な接続エラー (Java)

接続が予想外に切断したりタイムアウトしたりします。

Java 用 Apache Cassandra ドライバーには、`ExponentialReconnectionPolicy` と `ConstantReconnectionPolicy` の 2 つのネイティブ再接続ポリシーが用意されています。 既定では、 `ExponentialReconnectionPolicy`です。 ただし、Azure Cosmos DB の Cassandra API の場合は、2 秒の遅延で `ConstantReconnectionPolicy` を実行することをお勧めします。

[Java 4.x ドライバーのドキュメント](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)、[Java 3.x ドライバーのドキュメント](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)、または「[Java ドライバーの ReconnectionPolicy の構成](#configure-reconnectionpolicy-for-the-java-driver)」の例を参照してください。

## <a name="error-with-load-balancing-policy"></a>負荷分散ポリシーのエラー

次のようなコードを使用して、Java Datastax ドライバーの v3.x に負荷分散ポリシーを実装している場合があります。

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

`withLocalDc()` の値がコンタクト ポイントのデータセンターと一致しない場合、断続的なエラーが発生することがあります: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)`。

[CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) を実装してください。 これを機能させるには、次のコードを使用して DataStax をアップグレードする必要が生じる可能性があります。

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>大きなテーブルでカウントが失敗する

多数の行で `select count(*) from table` または同様のものを実行すると、サーバーがタイムアウトになります。

ローカルの CQLSH クライアントを使用している場合は、`--connect-timeout` または `--request-timeout` の設定を変更してください。 「[cqlsh: the CQL shell](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)」を参照してください。

カウントがまだタイムアウトになる場合は、Azure portal の [メトリック] タブに移動し、メトリック `document count` を選択して、データベースまたはコレクションのフィルターを追加することによって、Azure Cosmos DB バックエンド テレメトリからレコードのカウントを取得できます (Azure Cosmos DB のテーブルのアナログ)。 その後、レコード数をカウントする特定の時点のグラフにマウス ポインターを合わせることができます。

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="メトリック ビュー":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>Java ドライバーの ReconnectionPolicy を構成する

### <a name="version-3x"></a>バージョン 3.x

Java ドライバーのバージョン 3.x では、クラスター オブジェクトの作成時に再接続ポリシーを構成します。

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>バージョン 4.x

Java ドライバーのバージョン 4.x では、`reference.conf` ファイルの設定をオーバーライドして再接続ポリシーを構成します。

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-the-java-driver"></a>Java ドライバーのキープアライブを有効にする

### <a name="version-3x"></a>バージョン 3.x

Java ドライバーのバージョン 3.x では、クラスター オブジェクトの作成時にキープアライブを設定し、キープアライブが[オペレーティング システムで有効になっていること](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)を確認します。

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>バージョン 4.x

Java ドライバーのバージョン 4.x では、`reference.conf` の設定をオーバーライドしてキープアライブを設定し、キープアライブが[オペレーティング システムで有効になっていること](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)を確認します。

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の Cassandra API で[サポートされている機能](cassandra-support.md)について確認します。
- [ネイティブの Apache Cassandra から Azure Cosmos DB の Cassandra API に移行する](cassandra-migrate-cosmos-db-databricks.md)方法について確認します。
