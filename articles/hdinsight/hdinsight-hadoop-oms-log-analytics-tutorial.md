---
title: Azure Monitor ログを使用して Azure HDInsight クラスターを監視する
description: Azure Monitor ログを使って HDInsight クラスターで実行中のジョブを監視する方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurepowershell, references_regions
ms.date: 09/21/2021
ms.openlocfilehash: c4fc351105c82213549fdb357d19b480c5a51ed4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648033"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor ログを使用して HDInsight クラスターを監視する

Azure Monitor ログを有効にして、HDInsight で Hadoop クラスターの操作を監視する方法を説明します。 また、HDInsight 監視ソリューションを追加する方法について説明します。

[Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)は、クラウド環境とオンプレミス環境を監視する Azure Monitor サービスです。 この監視では、可用性とパフォーマンスを維持します。 クラウド、オンプレミス環境内にあるリソースによって生成されたデータと他の監視ツールのデータを収集します。 データは、複数のソースにわたる分析を提供するために使用されます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

#### <a name="new-azure-monitor-experience"></a>[新しい Azure Monitor エクスペリエンス](#tab/new)

> [!Important]
> 新しい Azure Monitor エクスペリエンスは、プレビュー機能として米国東部と西ヨーロッパでのみ使用できます。  
>

## <a name="prerequisites"></a>前提条件

* Log Analytics ワークスペース。 このワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Azure Monitor ログ環境と考えることができます。 手順については、[Log Analytics ワークスペースの作成](../azure-monitor/vm/monitor-virtual-machine.md)に関するページをご覧ください。

* Azure HDInsight クラスター。 現在、Azure Monitor ログは次の HDInsight クラスター タイプで使用できます。

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  HDInsight クラスターの作成手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。  

* PowerShell を使用している場合は、[AZ モジュール](/powershell/azure/)が必要になります。 最新バージョンを使用していることを確認します。 必要であれば、`Update-Module -Name Az` を実行します。

* Azure CLI を使用したいが、まだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

> [!NOTE]  
> 新しい Azure Monitor エクスペリエンスは、プレビュー機能として米国東部と西ヨーロッパでのみ使用できます。 パフォーマンス向上のため、HDInsight クラスターと Log Analytics ワークスペースの両方を同じリージョンに配置することをお勧めします。 Azure Monitor ログは、すべての Azure リージョンで使用できるわけではありません。
>

## <a name="enable-azure-monitor-using-the-portal"></a>ポータルを使用して Azure Monitor を有効にする

ここでは、ジョブやデバッグ ログなどを監視するために、Azure Log Analytics ワークスペースを使用するよう既存の HDInsight Hadoop クラスターを構成します。

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。 このクラスターは、新しいポータル ページで開かれます。

2. 左側の [監視] で  **[Monitor Integration]\(モニター統合\)** を選択します。 

3. メイン ビューの  **[Azure Monitor for HDInsight Clusters Integration]\(HDInsight クラスター統合の Azure Monitor\)** の下で  **[有効化]** を選択します。 

4.  **[ワークスペースを選択]**   ドロップダウン リストから、既存の Log Analytics ワークスペースを選択します。 

5.  **[保存]** を選択します。 設定を保存するまでしばらく時間がかかります。 

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-azure-monitor.png" alt-text="HDInsight クラスターの監視を有効にする":::

Azure Monitor を無効にする場合は、このポータルで同じことを行います。 

## <a name="enable-azure-monitor-using-azure-powershell"></a>Azure PowerShell を使用して Azure Monitor を有効にする

Azure PowerShell Az モジュールの [Enable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/enable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) コマンドレットを使用して Azure Monitor ログを有効にすることができます。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightAzureMonitor `
    -ResourceGroupName $resourceGroup `
    -ClusterName $cluster
```

無効にする場合は、[Disable-AzHDInsightAzureMonitor](/powershell/module/az.hdinsight/disable-azhdinsightazuremonitor?view=azps-6.2.1&preserve-view=true) コマンドレットを使用します。

```powershell
Disable-AzHDInsightAzureMonitor -ResourceGroupName $resourceGroup `
-ClusterName $cluster
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Azure CLI を使用して Azure Monitor を有効にする

Azure CLI [`az hdinsight azure-monitor enable`](/cli/azure/hdinsight/azure-monitor?view=azure-cli-latest&preserve-view=true) コマンドを使用して、Azure Monitor ログを有効にすることができます。

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight azure-monitor show --name $cluster --resource-group $resourceGroup
```

無効にするには、[`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) コマンドを使用します。

```azurecli
az hdinsight azure-monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="use-hdinsight-out-of-box-insights-to-monitor-a-single-cluster"></a>HDInsight の既定の分析情報を使用して 1 つのクラスターを監視する

HDInsight には、分析情報を迅速に得るのに役立つワークロード固有のブックが用意されています。 このブックによって、HDInsight クラスターから重要なパフォーマンス メトリックが収集され、最も一般的なシナリオ用に視覚化とダッシュボードが提供されます。 既定の分析情報から、リソース使用率やアプリケーションの状態など、1 つの HDInsight クラスターを完全に把握できます。

使用可能な HDInsight ブック:

- HDInsight Spark ブック
- HDInsight Kafka ブック
- HDInsight HBase ブック
- HDInsight Hive/LLAP ブック
- HDInsight Storm ブック

Spark ブックのスクリーンショット :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-spark-workbook.png" alt-text="Spark ブックのスクリーンショット":::

## <a name="use-at-scale-insights-to-monitor-multiple-clusters"></a>大規模分析情報を使用して複数のクラスターを監視する

Azure portal にログインして [監視] を選択できます。 **[分析情報]** セクションで **[Insights Hub]\(分析情報ハブ\)** を選択できます。 その後、HDInsight クラスターを見つけることができます。

このビューでは、1 つの場所で複数の HDInsight クラスターを監視できます。
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-monitor-insights.png" alt-text="クラスター モニター分析情報のスクリーンショット":::

監視するサブスクリプションと HDInsight クラスターを選択できます。 
 - **[Monitored Clusters]\(監視対象クラスター\)** に、Azure Monitor 統合を有効にしたクラスターの数が表示されます。
 - **[Unmonitored Cluster]\(監視対象外クラスター\)** に、Azure Monitor 統合を有効にしていないクラスターの数が表示されます。

クラスターの詳しい一覧は、各セクションで確認できます。 

**[Monitored Clusters]\(監視対象クラスター\)** の **[概要]** タブに、クラスターの種類、重要なアラート、リソース使用率が表示されます。
    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-cluster-alerts.png" alt-text="クラスター モニター アラートのスクリーンショット":::

また、Spark、HBase、Hive、Kafka、Storm など、ワークロードの各種類のクラスターを確認することもできます。

アクティブなノード マネージャー数や実行中のアプリケーション数など、ワークロードの種類ごとに主要なメトリックが表示されます。

:::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/spark-metrics.png" alt-text="クラスター モニターの Spark メトリック":::

## <a name="configuring-performance-counters"></a>パフォーマンス カウンターの構成

Azure Monitor では、クラスター内のノードのパフォーマンス メトリックの収集と分析がサポートされています。 詳細については、[Azure Monitor の Linux パフォーマンス データ ソース](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)に関する記事をご覧ください。

## <a name="cluster-auditing"></a>クラスターの監査

HDInsight では、次の種類のログをインポートすることによって、Azure Monitor ログを使用したクラスターの監査をサポートしています。

* `log_gateway_audit_CL` - このテーブルは、成功/失敗したサインイン試行を示すクラスター ゲートウェイ ノードの監査ログを提供します。
* `log_auth_CL` - このテーブルは、成功/失敗したサインイン試行が含まれる SSH のログを提供します。
* `log_ambari_audit_CL` - このテーブルは、Ambari からの監査ログを提供します。
* `log_ranger_audti_CL` - このテーブルは、ESP クラスター上の Apache Ranger からの監査ログを提供します。


#### <a name="classic-azure-monitor-experience"></a>[クラシック Azure Monitor エクスペリエンス](#tab/previous)

## <a name="prerequisites"></a>前提条件

* Log Analytics ワークスペース。 このワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Azure Monitor ログ環境と考えることができます。 手順については、[Log Analytics ワークスペースの作成](../azure-monitor/vm/monitor-virtual-machine.md)に関するページをご覧ください。

* ファイアウォールの背後にあるクラスターで Azure Monitor 統合を使用する予定の場合は、[ファイアウォールの背後にあるクラスターの前提条件](#oms-with-firewall)を満たす必要があります。

* Azure HDInsight クラスター。 現在、Azure Monitor ログは次の HDInsight クラスター タイプで使用できます。

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  HDInsight クラスターの作成手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。  

* PowerShell を使用している場合は、[AZ モジュール](/powershell/azure/)が必要になります。 最新バージョンを使用していることを確認します。 必要であれば、`Update-Module -Name Az` を実行します。

* Azure CLI を使用したいが、まだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

> [!NOTE]  
> パフォーマンス向上のため、HDInsight クラスターと Log Analytics ワークスペースの両方を同じリージョンに配置することをお勧めします。 Azure Monitor ログは、すべての Azure リージョンで使用できるわけではありません。

## <a name="enable-azure-monitor-using-the-portal"></a>ポータルを使用して Azure Monitor を有効にする

ここでは、ジョブやデバッグ ログなどを監視するために、Azure Log Analytics ワークスペースを使用するよう既存の HDInsight Hadoop クラスターを構成します。

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。 このクラスターは、新しいポータル ページで開かれます。

1. 左側の **[監視]** で **[Azure Monitor]** を選択します。

1. メイン ビューの **[Azure Monitor の統合]** で、 **[有効化]** を選択します。

1. **[ワークスペースを選択]** ドロップダウン リストから、既存の Log Analytics ワークスペースを選択します。

1. **[保存]** を選択します。  設定を保存するまでしばらく時間がかかります。

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="HDInsight クラスターの監視を有効にする":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Azure PowerShell を使用して Azure Monitor を有効にする

Azure PowerShell Az モジュールの [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) コマンドレットを使用して Azure Monitor ログを有効にすることができます。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

無効にする場合は、[Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) コマンドレットを使用します。

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Azure CLI を使用して Azure Monitor を有効にする

Azure CLI `[az hdinsight monitor enable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable) コマンドを使用して、Azure Monitor ログを有効にすることができます。

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

無効にするには、[`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) コマンドを使用します。

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```
## <a name=""></a><a name="oms-with-firewall">ファイアウォールの背後にあるクラスターの前提条件</a>

一部のお客様は、ファイアウォールの背後で HDInsight との Azure Monitor 統合を正常に設定するために、以下のエンドポイントを有効にする必要があります。

|エージェントのリソース | Port | Direction | バイパス HTTPS 検査 |
|---|---|---|---|
| \*.ods.opinsights.azure.com | ポート 443 | 送信 | はい |
| \*.oms.opinsights.azure.com |ポート 443 | 送信 | はい |
| \*.azure-automation.net | ポート 443 | 送信 | はい |

ワイルドカード ストレージ エンドポイントの有効化に関連してセキュリティ制限がある場合は、別のオプションがあります。 代わりに以下を実行できます。

1. 専用のストレージ アカウントを作成します
2. ログ分析ワークスペースで専用ストレージ アカウントを構成します
3. ファイアウォールで専用ストレージ アカウントを有効にします

### <a name="data-collection-behind-a-firewall"></a>ファイアウォール背後でのデータ収集
設定が成功したら、データ インジェストに必要なエンドポイントを有効にすることが重要です。 データ インジェストを成功させるため、\*blob.core.windows.net エンドポイントを有効にすることをお勧めします。


## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight クラスター管理ソリューションをインストールする

HDInsight では、Azure Monitor ログに追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../azure-monitor/insights/solutions.md)を使用すると、Azure Monitor ログに機能を追加して、さらにデータおよび分析ツールを提供できます。 これらのソリューションでは、HDInsight クラスターから重要なパフォーマンス メトリックが収集されます。 メトリックを検索するためのツールが提供されます。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。

利用可能な HDInsight ソリューションは次のとおりです。

* HDInsight Hadoop Monitoring
* HDInsight HBase の監視
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

管理ソリューションをインストールする手順については、[Azure の管理ソリューション](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)に関する記事をご覧ください。 テスト用に HDInsight Hadoop Monitoring ソリューションをインストールします。 インストールが完了すると、 **[概要]** の下に **HDInsightHadoop** タイルが表示されます。 **HDInsightHadoop** タイルを選択します。 HDInsightHadoop ソリューションは次のようになります。

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="HDInsight 監視ソリューションのビュー":::

クラスターは新規のクラスターであるため、レポートにはアクティビティが表示されません。

## <a name="configuring-performance-counters"></a>パフォーマンス カウンターの構成

Azure Monitor では、クラスター内のノードのパフォーマンス メトリックの収集と分析がサポートされています。 詳細については、[Azure Monitor の Linux パフォーマンス データ ソース](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)に関する記事をご覧ください。

## <a name="cluster-auditing"></a>クラスターの監査

HDInsight では、次の種類のログをインポートすることによって、Azure Monitor ログを使用したクラスターの監査をサポートしています。

* `log_gateway_audit_CL` - このテーブルは、成功/失敗したサインイン試行を示すクラスター ゲートウェイ ノードの監査ログを提供します。
* `log_auth_CL` - このテーブルは、成功/失敗したサインイン試行が含まれる SSH のログを提供します。
* `log_ambari_audit_CL` - このテーブルは、Ambari からの監査ログを提供します。
* `log_ranger_audti_CL` - このテーブルは、ESP クラスター上の Apache Ranger からの監査ログを提供します。

---

## <a name="update-the-log-analytics-oms-agent-used-by-hdinsight-azure-monitor-integration"></a>HDInsight の Azure Monitor 統合で使用される Log Analytics (OMS) エージェントを更新する

クラスターで Azure Monitor 統合が有効になっていると、Log Analytics エージェントや Operations Management Suite (OMS) エージェントは、クラスターにインストールされて、Azure Monitor 統合を無効にして再度有効にしない限り更新されません。 クラスター上の OMS エージェントを更新する必要がある場合は、下記の手順を完了します。 ファイアウォールの背後にいる場合は、[ファイアウォールの背後にあるクラスターの前提条件](#oms-with-firewall)を完了してから、これらの手順を完了する必要がある場合があります。

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。 このクラスターは、新しいポータル ページで開かれます。
1. 左側の **[監視]** で **[Azure Monitor]** を選択します。
1. 現在の Log Analytics ワークスペースの名前を書き留めます。
1. メイン ビューの **[Azure Monitor の統合]** で、トグルを無効にしてから **[保存]** を選択します。 
1. 設定の保存後、 **[Azure Monitor の統合]** トグルを再度有効にして、同じ Log Analytics ワークスペースが選択されていることを確認してから **[保存]** を選択します。

クラスターで Azure Monitor 統合を有効にしている場合は、OMS エージェントを更新すると Open Management Infrastructure (OMI) バージョンも更新されます。 次のコマンドを実行すると、クラスターでの OMI バージョンをチェックできます。 

```
 sudo /opt/omi/bin/omiserver –version
```

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Apache Ambari と Azure Monitor ログを使用してクラスターの可用性を監視する方法](./hdinsight-cluster-availability.md)
