---
title: Log Analytics を使用して Azure HDInsight クラスターを監視する
description: Azure Log Analytics を使って HDInsight クラスターで実行中のジョブを監視する方法を説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 990a836a405c7baf6327e625aa31a9828f217d9f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600976"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Azure Log Analytics を使用して Azure HDInsight クラスターを監視する

Azure Log Analytics を有効にして HDInsight で Hadoop クラスターの操作を監視する方法と、HDInisght 監視ソリューション追加する方法を説明します。

[Log Analytics](../log-analytics/log-analytics-overview.md) は、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持するサービスです。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* **Log Analytics ワークスペース**。 ワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Log Analytics 環境として考えることができます。 手順については、[Log Analytics ワークスペースの作成](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)に関するページをご覧ください。

* **Azure HDInsight クラスター**。 現在、Log Analytics は次の HDInsight クラスター タイプで使用することができます。

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  HDInsight クラスターの作成手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。

> [!NOTE]
> パフォーマンス向上のため、HDInsight クラスターと Log Analytics ワークスペースの両方を同じリージョンに配置することをお勧めします。 すべての Azure リージョンで Azure Log Analytics を利用することはできません。

## <a name="enable-log-analytics-by-using-the-portal"></a>ポータルを使用して Log Analytics を有効にする

ここでは、ジョブやデバッグ ログなどを監視するために、Azure Log Analytics ワークスペースを使用するよう既存の HDInsight Hadoop クラスターを構成します。

1. Azure Portal で HDInsight クラスターを開きます。
2. 左側のウィンドウで、**[監視]** を選択します。
3. 右側のウィンドウで **[有効化]** を選択し、既存の Log Analytics ワークスペースを選択して、**[保存]** を選択します。

    ![HDInsight クラスターの監視の有効化](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight クラスターの監視の有効化")

    設定を保存するまでしばらく時間がかかります。

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Azure PowerShell を使用して Log Analytics を有効にする

Azure PowerShell を使用して Log Analytics を有効にすることができます。 コマンドレットは次のとおりです。

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

「[Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0)」(Enable-AzureRmHDInsightOperationsManagementSuite) をご覧ください。

無効にするコマンドレットは次のとおりです。

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

「[Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0)」(Disable-AzureRmHDInsightOperationsManagementSuite) をご覧ください。

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight クラスター管理ソリューションをインストールする

HDInsight では、Azure Log Analytics に追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../log-analytics/log-analytics-add-solutions.md)を使用すると、Log Analytics に機能を追加して、追加のデータおよび分析ツールを提供できます。 これらのソリューションは、HDInsight クラスターから重要なパフォーマンス メトリックを収集し、メトリックを検索するツールを提供します。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。

利用可能な HDInsight ソリューションは次のとおりです。

* HDInsight Hadoop Monitoring
* HDInsight HBase の監視
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

管理ソリューションをインストールする手順については、「[Azure の管理ソリューション](../monitoring/monitoring-solutions.md#install-a-management-solution)」をご覧ください。 テスト用に HDInsight Hadoop Monotiring ソリューションをインストールします。 インストールが完了すると、**[概要]** の下に **HDInsightHadoop** タイルが表示されます。 **HDInsightHadoop** タイルを選択します。 HDInsightHadoop ソリューションは次のようになります。

![HDInsight OMS Hadoop 監視ソリューションのビュー](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

クラスターは新規のクラスターであるため、レポートにはアクティビティが表示されません。

## <a name="next-steps"></a>次の手順

* [Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)
