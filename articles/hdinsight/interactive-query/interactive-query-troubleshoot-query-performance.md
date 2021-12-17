---
title: Azure HDInsight での Apache Hive LLAP クエリのパフォーマンスの低下
description: Azure HDInsight で、Apache Hive LLAP でのクエリの実行速度が予想より遅くなっています。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: e21b49d26e8b7570b7f70e30f04b44fd23341f03
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290613"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>シナリオ: Azure HDInsight での Apache Hive LLAP クエリのパフォーマンスの低下

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

既定のクラスター構成が、ワークロードに対して十分にチューニングされていません。 Hive LLAP でのクエリの実行速度が予想より遅くなっています。

## <a name="cause"></a>原因

これは、さまざまな理由で発生する可能性があります。

## <a name="resolution"></a>解決方法

LLAP は、結合と集計を含むクエリに対して最適化されています。 対話型 Hive クラスターでは、次のようなクエリはうまく動作しません。

```
select * from table where column = "columnvalue"
```

Hive LLAP でのポイント クエリのパフォーマンスを向上させるには、次の構成を設定します。

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

次のように構成を変更し、LLAP キャッシュの使用率を上げてパフォーマンスを向上させることもできます。

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]