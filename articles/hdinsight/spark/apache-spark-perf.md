---
title: パフォーマンスのための Spark ジョブの最適化 - Azure HDInsight
description: Azure HDInsight で Apache Spark クラスターのパフォーマンスを最適にするための一般的な戦略を示します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: d808339dd842e88e74efce5d56d12bc5250eb238
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029296"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>HDInsight で Apache Spark ジョブを最適化する

この記事では、Azure HDInsight で Apache Spark ジョブを最適化するための戦略を概説します。

## <a name="overview"></a>概要

Apache Spark ジョブのパフォーマンスは、複数の要因によって決まります。 これらのパフォーマンス要因には、データの保存方法、クラスターの構成方法、およびデータの処理時に使用される操作が含まれます。

直面する可能性のある一般的な課題は、不適切なサイズの実行プログラムが原因のメモリ制約、実行時間の長い操作、デカルト演算が発生するタスクなどです。

また、キャッシュ、データ スキューの許可など、これらの課題を克服するのに役立つ多くの最適化もあります。

次の各記事では、Spark の最適化のさまざまな側面に関する情報を確認できます。

* [Apache Spark 用にデータ ストレージを最適化する](optimize-data-storage.md)
* [Apache Spark のデータ処理を最適化する](optimize-data-processing.md)
* [Apache Spark 用にメモリ使用量を最適化する](optimize-memory-usage.md)
* [Apache Spark の HDInsight クラスター構成を最適化する](optimize-cluster-configuration.md)

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight で実行される Apache Spark ジョブのデバッグ](apache-spark-job-debugging.md)
* [HDInsight 上の Apache Spark クラスターのリソースを管理する](apache-spark-resource-manager.md)
* [Apache Spark の設定を構成する](apache-spark-settings.md)
