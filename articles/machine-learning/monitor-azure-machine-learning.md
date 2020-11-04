---
title: Azure Machine Learning の監視 | Microsoft Docs
description: Azure Monitor を使用して Azure Machine Learning のメトリックを表示し、分析し、それに関するアラートを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/01/2020
ms.openlocfilehash: 3470f969034a051b17e762b685a89c0f910e0cbb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747119"
---
# <a name="monitor-azure-machine-learning"></a>Azure Machine Learning の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Machine Learning によって生成される監視データと、Azure Monitor でこのデータを分析してアラートを生成する方法について説明します。

> [!TIP]
> このドキュメントは、 *ワークスペース* レベルでの Azure Machine Learning の監視について説明するものであり、記載されている情報は主に管理者を対象としています。 データ サイエンティストまたは開発者の方で、 *モデルのトレーニング実行* に固有の情報を監視したい場合は、次のドキュメントを参照してください。
>
> * [トレーニングの実行の開始、監視、およびキャンセル](how-to-manage-runs.md)
> * [トレーニング実行のメトリックを記録する](how-to-track-experiments.md)
> * [MLflow を使用して実験を追跡する](how-to-use-mlflow.md)
> * [TensorBoard を使用して実行を視覚化する](how-to-monitor-tensorboard.md)

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Azure Machine Learning は [Azure Monitor](/azure/azure-monitor/overview) を使用して監視データを作成します。これは、Azure のフルスタック監視サービスです。 Azure Monitor には、Azure リソースを監視するための完全な機能セットが用意されています。 また、他のクラウドやオンプレミスのリソースも監視できます。

まず「[Azure Monitor を使用した Azure リソースの監視](/azure/azure-monitor/insights/monitor-azure-resource)」の記事にある次の概念の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事に基づき、Azure Machine Learning のために収集された特定のデータについて説明します。 これらのセクションでは、Azure ツールを使用してデータ収集を構成し、このデータを分析する例も紹介します。

> [!TIP]
> Azure Monitor に関連したコストを把握するには、[使用量と推定コスト](/azure/azure-monitor/platform/usage-estimated-costs)に関する記事を参照してください。 データが Azure Monitor に表示されるまでにかかる時間を把握するには、[ログ データのインジェスト時間](/azure/azure-monitor/platform/data-ingestion-time)に関する記事を参照してください。

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure Machine Learning の監視データ

Azure Machine Learning は、他の Azure リソースと同じ種類の監視データを収集します。これについては、[Azure リソースの監視データ](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources)に関する記事を参照してください。 

Azure Machine Learning によって作成されるログおよびメトリックの詳細なリファレンスについては、「[Azure Machine Learning 監視データのリファレンス](monitor-resource-reference.md)」を参照してください。

<a id="configuration"></a>

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal、CLI、または PowerShell を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](/azure/azure-monitor/platform/diagnostic-settings)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。 Azure Machine Learning のカテゴリは、「[Azure Machine Learning 監視データのリファレンス](monitor-resource-reference.md#resource-logs)」に記載されています。

> [!IMPORTANT]
> これらの設定を有効にするには、追加の Azure サービス (ストレージ アカウント、イベント ハブ、または Log Analytics) が必要であり、コストが増加する可能性があります。 推定コストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator)にアクセスしてください。

Azure Machine Learning の次のログを構成できます。

| カテゴリ | 説明 |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning コンピューティング クラスターからのイベント。 |
| AmlComputeClusterNodeEvent | Azure Machine Learning コンピューティング クラスター内のノードからのイベント。 |
| AmlComputeJobEvent | Azure Machine Learning コンピューティングで実行されているジョブからのイベント。 |

> [!NOTE]
> 診断設定でメトリックを有効にするとき、現在、ディメンション情報は、ストレージ アカウント、イベント ハブ、または Log Analytics に送信される情報の一部として含まれません。

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[Metrics]\(メトリック\)** を開き、Azure Machine Learning のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](/azure/azure-monitor/platform/metrics-getting-started)」を参照してください。

収集されるプラットフォーム メトリックの一覧については、「[Azure Machine Learning 監視データのリファレンス メトリック](monitor-resource-reference.md#metrics)」を参照してください。

Azure Machine Learning のすべてのメトリックは、 **Machine Learning Service ワークスペース** という名前空間にあります。

![Machine Learning Service ワークスペースが選択されているメトリックス エクスプローラー](./media/monitor-azure-machine-learning/metrics.png)

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](/azure/azure-monitor/platform/metrics-supported)の一覧を確認できます。

### <a name="filtering-and-splitting"></a>フィルター処理と分割

ディメンションをサポートするメトリックについては、ディメンション値を使用してフィルターを適用できます。 たとえば、`cpu-cluster`という **クラスター名** の **アクティブ コア** をフィルター処理します。 

また、メトリックをディメンションで分割して、メトリックのセグメントを互いに比較してどのように異なるかを視覚化することもできます。 たとえば、 **パイプラインのステップの種類** を分割して、パイプラインで使用されているステップの種類の数を確認します。

フィルター処理と分割の詳細については、[Azure Monitor の高度な機能](/azure/azure-monitor/platform/metrics-charts)に関する記事をご覧ください。

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>ログの分析

Azure Monitor Log Analytics を使用するには、診断構成を作成して、 __Log Analytics への情報の送信__ を有効にする必要があります。 詳細については、「[コレクションとルーティング](#collection-and-routing)」セクションを参照してください。

Azure Monitor のログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。 Azure Machine Learning は、次のテーブルにデータを格納します。

| テーブル | 説明 |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning コンピューティング クラスターからのイベント。 |
| AmlComputeClusterNodeEvent | Azure Machine Learning コンピューティング クラスター内のノードからのイベント。 |
| AmlComputeJobEvent | Azure Machine Learning コンピューティングで実行されているジョブからのイベント。 |

> [!IMPORTANT]
> Azure Machine Learning のメニューから **[ログ]** を選択すると、クエリのスコープが現在のワークスペースに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のデータベースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **Azure Monitor** のメニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

ログおよびメトリックの詳細なリファレンスについては、「[Azure Machine Learning 監視データのリファレンス](monitor-resource-reference.md)」を参照してください。

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> [サービス名] のメニューから **[ログ]** を選択すると、クエリのスコープが現在の Azure Machine Learning ワークスペースに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のワークスペースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

次に、Azure Machine Learning リソースの監視に使用できるクエリを示します。 

+ 過去 5 日間に失敗したジョブを取得する。

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 特定のジョブ名のレコードを取得する。

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ VM サイズが Standard_D1_V2 のクラスターについて、過去 5 日間のクラスター イベントを取得する。

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 過去 8 日間に割り当てられたノードを取得する。

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>アラート

Azure Machine Learning のアラートにアクセスするには、 **Azure Monitor** のメニューから **[アラート]** を開きます。 アラートの作成の詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](/azure/azure-monitor/platform/alerts-metric)」を参照してください。

次の表に、Azure Machine Learning の一般的および推奨されるメトリック アラート ルールを示します。

| アラートの種類 | 条件 | 説明 |
|:---|:---|:---|
| モデル デプロイ失敗 | 集計の種類: 合計、演算子: より大きい、しきい値: 0 | 1 つ以上のモデル デプロイが失敗した場合 |
| クォータ使用率 | 集計の種類: 平均、演算子: より大きい、しきい値: 90| クォータ使用率が 90% を超えている場合 |
| 使用できないノード | 集計の種類: 合計、演算子: より大きい、しきい値: 0 | 1 つ以上の使用できないノードがある場合 |

## <a name="next-steps"></a>次のステップ

- ログおよびメトリックのリファレンスについては、「[Azure Machine Learning 監視データのリファレンス](monitor-resource-reference.md)」を参照してください。
- Azure Machine Learning に関連したクォータの操作については、「[Azure リソースのクォータの管理と要求](how-to-manage-quotas.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](/azure/azure-monitor/insights/monitor-azure-resource)」を参照してください。
