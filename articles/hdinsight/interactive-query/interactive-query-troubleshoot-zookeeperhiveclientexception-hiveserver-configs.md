---
title: Apache Hive Zeppelin インタープリター エラー - Azure HDInsight
description: Apache Zeppelin Hive JDBC インタープリターが、Azure HDInsight で間違った URL を指しています
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 04288fdde74da705f6127c2444b0dc7ac7a2a8dd
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290541"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で Apache Hive Zeppelin インタープリターから Zookeeper エラーが発生する

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive LLAP クラスターで、クエリを実行しようとすると Zeppelin インタープリターによって次のエラー メッセージが表示されます。

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>原因

The Zeppelin Hive JDBC Interpreter is pointing to the wrong URL. (Zeppelin Hive JDBC インタープリターが間違った URL を指しています。)

## <a name="resolution"></a>解決方法

1. Hive コンポーネントの概要に移動し、"Hive JDBC Url" をクリップボードにコピーします。

1. `https://clustername.azurehdinsight.net/zeppelin/#/interpreter` に移動します

1. JDBC の設定を編集する: hive.url の値を、手順 1 でコピーした Hive JDBC URL に更新します

1. 保存してから、クエリを再試行します

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]