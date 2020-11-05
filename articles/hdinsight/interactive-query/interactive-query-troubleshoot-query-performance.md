---
title: Azure HDInsight での Apache Hive LLAP クエリのパフォーマンスの低下
description: Azure HDInsight で、Apache Hive LLAP でのクエリの実行速度が予想より遅くなっています。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288872"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>シナリオ:Azure HDInsight での Apache Hive LLAP クエリのパフォーマンスの低下

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

既定のクラスター構成が、ワークロードに対して十分にチューニングされていません。 Hive LLAP でのクエリの実行速度が予想より遅くなっています。

## <a name="cause"></a>原因

これは、さまざまな理由で発生する可能性があります。

## <a name="resolution"></a>解像度

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]