---
title: Apache Hive から Apache Zookeeper への接続 - Azure HDInsight
description: Azure HDInsight の Apache Zookeeper の問題のために Apache Hive ビューにアクセスできない
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: cc7ad8d32e4d15b8e0671d162b1796619b4879c7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290721"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>シナリオ: Apache Hive から Azure HDInsight の Apache Zookeeper への接続を確立できない

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Hive ビューにアクセスできません。また、`/var/log/hive` のログに次のようなエラーが表示されます。

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (<zookeepername1>.cloud.wbmi.com:2181,<zookeepername2>.cloud.wbmi.com:2181,<zookeepername3>.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>原因

Hive からZookeeper への接続を確立できないことがあります。これにより、Hive ビューを起動できません。

## <a name="resolution"></a>解決方法

1. Zookeeper サービスが正常であることを確認します。

1. Zookeeper サービスに Hive Server2 の ZNode エントリがあるかどうかを確認します。 値が見つからないか、正しくありません。

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server <zookeepername1>
    [zk: <zookeepername1>(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 接続を再確立するには、Zookeeper ノードを再起動し、HiveServer2 を再起動します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]