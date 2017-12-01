---
title: "Log Analytics を使用して Azure HDInsight クラスターを監視する | Microsoft Docs"
description: "Azure Log Analytics を使って HDInsight クラスターで実行中のジョブを監視する方法を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6677b0b3ed047ce011bfbb72c25e45195859830a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Azure Log Analytics を使用して Azure HDInsight クラスターを監視する

Azure Log Analytics を使って HDInsight でHadoop クラスターの操作を監視する方法を説明します。

[Log Analytics](../log-analytics/log-analytics-overview.md) は、[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) のサービスであり、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持します。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。 

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 このチュートリアルを開始する前に、Azure サブスクリプションが必要です。 「[無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free)」をご覧ください。

* **Azure HDInsight クラスター**。 現在、Azure Operations Management Suite は次の HDInsight クラスター タイプで使用することができます。

    * Hadoop
    * HBase
    * Interactive Query
    * Kafka
    * Spark
    * Storm

    HDInsight クラスターの作成手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。

* **Log Analytics ワークスペース**。 ワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Log Analytics 環境として考えることができます。 Azure HDInsight クラスターと関連付けることができる、このようなクラスターを少なくとも 1 つ作成しておく必要があります。 手順については、[Log Analytics ワークスペースの作成](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)に関するページを参照してください。

## <a name="configure-hdinsight-cluster-to-use-log-analytics"></a>Log Analytics を使用するように HDInsight クラスターを構成する

ここでは、ジョブやデバッグ ログなどを監視するために、Azure Log Analytics ワークスペースを使用するよう既存の HDInsight Hadoop クラスターを構成します。

1. Azure Portal で HDInsight クラスターを開きます。
2. 左側のウィンドウで、**[監視]** をクリックします。
3. 右側のウィンドウで **[有効化]** をクリックし、既存の Log Analytics ワークスペースを選択し、**[保存]** をクリックします。

    ![HDInsight クラスターの監視の有効化](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight クラスターの監視の有効化")

    設定を保存するまでしばらく時間がかかります。  設定完了後、上部に **[OMS ダッシュ ボードを開く]** ボタンが表示されます。 

    ![Operations Management Suite ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "OMS ダッシュボードを開く")

5. **[OMS ダッシュ ボードを開く]** をクリックします。
6. メッセージが表示されたら、Azure の資格情報を入力します。

    ![Operations Management Suite ポータル](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite ポータル")

## <a name="next-steps"></a>次のステップ
* [Log Analytics に HDInsight クラスター管理ソリューションを追加する](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
