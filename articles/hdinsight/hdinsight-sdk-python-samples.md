---
title: 'Azure HDInsight: Python のサンプル'
description: HDInsight SDK for Python を使用した一般的なタスクの Python サンプルは、GitHub でご覧いただけます。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 11/08/2019
ms.custom: devx-track-python
ms.openlocfilehash: f8b6f1f8b22435af33c9ae908bb0a3f6f3df8889
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535350"
---
# <a name="azure-hdinsight-python-samples"></a>Azure HDInsight: Python のサンプル

> [!div class="op_single_selector"]
> * [Python のサンプル](hdinsight-sdk-python-samples.md)
> * [.NET のサンプル](hdinsight-sdk-dotnet-samples.md)
> * [Java のサンプル](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

> [!Important]
> Python 2.7 は、2020 年 1 月 1 日に非推奨となります。 Python 2.7 をまだ使用している場合は、HDInsight Python SDK を使用するために 3.7 にアップグレードしてください。  

この記事では、次の内容について説明します。

* クラスター作成タスクのサンプルへのリンク
* その他の管理タスクのリファレンス コンテンツへのリンク

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

[Python 用の Azure HDInsight SDK](/python/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>クラスターの管理 - 作成

* [Apache Kafka クラスターの作成](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_kafka_cluster_sample.py)
* [Apache Spark クラスターの作成](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_spark_cluster_sample.py)
* [Azure Data Lake Storage Gen2 での Apache Spark クラスターの作成](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_hadoop_cluster_with_adls_gen2_sample.py)
* [Enterprise セキュリティ パッケージ (ESP) での Apache Spark クラスターの作成](https://github.com/Azure-Samples/hdinsight-python-sdk-samples/blob/master/samples/create_esp_cluster_sample.py)

これらの Python サンプルは、[hdinsight-python-sdk-samples](https://github.com/Azure-Samples/hdinsight-python-sdk-samples) GitHub リポジトリを複製することで入手できます。

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

ここに示した追加的な SDK 機能に関するコード スニペットは、[HDInsight SDK for Python のリファレンス ドキュメント](/python/api/overview/azure/hdinsight)でご覧いただけます。