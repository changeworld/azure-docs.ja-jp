---
title: パフォーマンスのための Spark ジョブの最適化 - Azure HDInsight
description: Azure HDInsight で Apache Spark クラスターのパフォーマンスを最適にするための一般的な戦略を示します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780109"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>HDInsight で Apache Spark ジョブを最適化する

この記事では、Azure HDInsight で Apache Spark ジョブを最適化するための戦略を概説します。

## <a name="overview"></a>概要

Apache Spark ジョブのパフォーマンスは、複数の要因によって決まります。 これらのパフォーマンス要因には、データの保存方法、クラスターの構成方法、およびデータの処理時に使用される操作が含まれます。

直面する可能性のある一般的な課題は、不適切なサイズの実行プログラムが原因のメモリ制約、実行時間の長い操作、デカルト演算が発生するタスクなどです。

また、キャッシュ、データ スキューの許可など、これらの課題を克服するのに役立つさまざまな戦略もあります。

次の各記事では、Spark の最適化のさまざまな側面について一般的な課題と解決策を確認できます。

* [データ ストレージを最適化する](optimize-data-storage.md)
* [データ処理を最適化する](optimize-data-processing.md)
* [メモリ使用量を最適化する](optimize-memory-usage.md)
* [クラスターの構成を最適化する](optimize-cluster-configuration.md)

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight で実行される Apache Spark ジョブのデバッグ](apache-spark-job-debugging.md)
* [HDInsight 上の Apache Spark クラスターのリソースを管理する](apache-spark-resource-manager.md)
* [Apache Spark の設定を構成する](apache-spark-settings.md)
