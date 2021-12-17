---
title: クエリ結果からの Apache Hive ビューのタイムアウト - Azure HDInsight
description: Azure HDInsight でクエリ結果をフェッチしているときに Apache Hive ビューがタイムアウトになる
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 5b1ec7fec182d5b0b6f2d68467d6e3e84fdb5f3c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601408"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>シナリオ: Azure HDInsight でクエリ結果をフェッチしているときに Apache Hive ビューがタイムアウトになる

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive ビューから特定のクエリを実行しているときに、次のエラーが発生する可能性があります。

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>原因

Hive ビューの既定のタイムアウト値が、実行しているクエリに適していない可能性があります。 指定された時間が短すぎて、Hive ビューがクエリ結果をフェッチできません。

## <a name="resolution"></a>解決方法

1. `/etc/ambari-server/conf/ambari.properties` で **両方のヘッドノードに対して** 次のプロパティを設定して、Apache Ambari Hive ビューのタイムアウト値を増やしてください。
  ```
  views.ambari.request.read.timeout.millis=300000
  views.request.read.timeout.millis=300000
  views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
  ```
  `HIVE_VIEW_INSTANCE_NAME` の値は、Hive ビューの URL の末尾にあります。

2. 次を実行して、アクティブな Ambari サーバーを再起動します。 アクティブな Ambari サーバーではないというエラー メッセージが表示された場合は、次のヘッドノードに ssh 接続して、この手順を繰り返してください。
  ```
  sudo ambari-server restart
  ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
