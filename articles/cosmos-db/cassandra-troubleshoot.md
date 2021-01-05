---
title: Azure Cosmos DB の Cassandra API の一般的なエラーのトラブルシューティング
description: このドキュメントでは、Azure Cosmos DB の Cassandra API で発生する一般的な問題のトラブルシューティング方法について説明します
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: f5f2cb5ac8c354df38310cdcb47b98e1da5b6cfa
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97517702"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB の Cassandra API の一般的な問題のトラブルシューティング
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra API は互換性レイヤーであり、広く普及しているオープンソースの Apache Cassandra データベースに[ワイヤプロトコルのサポート](cassandra-support.md)を提供し、[Azure Cosmos DB ](https://docs.microsoft.com/azure/cosmos-db/introduction)によって機能します。 フル マネージド クラウドネイティブ サービスとして、Azure Cosmos DB は、Cassandra API に[可用性、スループット、および整合性に対する保証](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)を提供します。 Apache Cassandra のレガシ実装では、これらの保証はできません。 また Cassandra API は、メンテナンス不要のプラットフォーム操作とダウンタイム不要の修正プログラム適用を容易にします。 したがって、そのバックエンド操作の多くが Apache Cassandra とは異なるので、一般的なエラーを回避するために特定の設定と方法をお勧めします。 

この記事では、Azure Cosmos DB の Cassandra API を使用するアプリケーションの一般的なエラーと解決策について説明します。

## <a name="common-errors-and-solutions"></a>一般的なエラーと解決

| エラー               |  説明             | 解決策  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | 使用される要求ユニットの合計数は、キースペースまたはテーブルでプロビジョニングされた要求ユニットよりも多くなります。 そのため、要求は調整されます。 | Azure portal からキースペースまたはテーブルに割り当てられたスループットのスケーリング (Cassandra API でのスケーリング操作については、[こちら](manage-scale-cassandra.md)をご覧ください) を検討するか、または再試行ポリシーを実装することもできます。 Java の場合は、[v3.x ドライバー](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)と [v4.x ドライバー](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)の再試行サンプルをご覧ください。 「[Azure Cosmos Cassandra Extensions for Java](https://github.com/Azure/azure-cosmos-cassandra-extensions)」もご覧ください |
| OverloadedException (Java) でも十分なスループットを確保 | 要求の量と消費された要求ユニット コストに対して十分なスループットがプロビジョニングされているにもかかわらず、システムは要求を調整しているように思われます  | Cassandra API は、スキーマレベルの操作 (CREATE TABLE、ALTER TABLE、DROP TABLE) に対してシステム スループット予算を実装します。 実稼働システムでのスキーマ操作には、この予算で十分です。 ただし、スキーマレベルの操作の数が多い場合は、この制限を超えている可能性があります。 この予算はユーザーによって制御されないため、実行するスキーマ操作の数を減らすことを検討する必要があります。 このアクションを実行しても問題が解決されない場合、またはワークロードに対して採算が合わない場合は、[Azure サポート要求を作成](../azure-portal/supportability/how-to-create-azure-support-request.md)してください。|
| ClosedConnectionException (Java) | 接続が成功してからアイドル時間が経過すると、アプリケーションが接続できなくなります| このエラーは、Azure LoadBalancers のアイドル タイムアウト (4 分) が原因で発生する可能性があります。 ドライバーのキープアライブ設定を行い (下記参照)、オペレーティング システムのキープアライブ設定を増やすか、または [Azure Load Balancer でアイドル タイムアウトを調整](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)します。 |
| 他の断続的な接続エラー (Java) | 接続が予想外に切断したりタイムアウトしたりします | Java 用 Apache Cassandra ドライバーには、`ExponentialReconnectionPolicy` と `ConstantReconnectionPolicy` の 2 つのネイティブ再接続ポリシーが用意されています。 既定値は、`ExponentialReconnectionPolicy` です。 ただし、Azure Cosmos DB の Cassandra API の場合は、2 秒の遅延で `ConstantReconnectionPolicy` を実行することをお勧めします。 Java v4.x のドライバーについては、[ドライバーのドキュメント](https://docs.datastax.com/developer/java-driver/4.9/manual/core/reconnection/)を、Java 3.x のガイダンスについては、[こちら](https://docs.datastax.com/developer/java-driver/3.7/manual/reconnection/)をご覧ください (以下の例もご覧ください)。|

エラーが上記に記載されておらず、[Cassandra API でサポートされる操作](cassandra-support.md)を実行するときにエラーが発生するが、*ネイティブの Apache Cassandra の使用時にはエラーが存在しない場合は*、[Azure サポート要求を作成します](../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Java ドライバーの ReconnectionPolicy の構成

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

## <a name="enable-keep-alive-for-java-driver"></a>Java ドライバーのキープアライブを有効にする

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

## <a name="next-steps"></a>次の手順

- Azure Cosmos DB の Cassandra API で[サポートされている機能](cassandra-support.md)について確認します。
- [Apache Cassandra から Azure Cosmos DB の Cassandra API にデータを移行する](cassandra-migrate-cosmos-db-databricks.md)方法について確認します

