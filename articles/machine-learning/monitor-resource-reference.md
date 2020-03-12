---
title: 監視データのリファレンス | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 用に収集され、かつ Azure Monitor で利用できるデータとリソースについて説明します。 Azure Monitor を使用すると、ご利用の Azure Machine Learning ワークスペースに関するデータを収集して表示できるほか、メトリックの表示、アラートの設定、ログ データの分析も行うことができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927557"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure Machine Learning 監視データのリファレンス

ご利用の Azure Machine Learning ワークスペースから Azure Monitor によって収集されるデータとリソースについて説明します。 監視データの収集と分析の詳細については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="resource-logs"></a>リソース ログ

次の表に、Azure Monitor ログまたは Azure Storage で Azure Machine Learning リソース ログが収集される場合のそれらのプロパティを示します。

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents テーブル

| プロパティ | 説明 |
|:--- |:---|
| TimeGenerated | ログ エントリが生成された時刻 |
| OperationName | ログ イベントに関連付けられた操作の名前 |
| カテゴリ | ログ イベントの名前、AmlComputeClusterNodeEvent |
| JobId | 送信されたジョブの ID |
| ExperimentId | 実験の ID |
| ExperimentName | 実験の名前 |
| CustomerSubscriptionId | 実験とジョブが送信された SubscriptionId |
| WorkspaceName | 機械学習ワークスペースの名前 |
| ClusterName | クラスターの名前 |
| ProvisioningState | ジョブの送信の状態 |
| ResourceGroupName | リソース グループの名前 |
| JobName | ジョブの名前 |
| ClusterId | クラスターの ID |
| EventType | ジョブ イベントの種類 (例: JobSubmitted、JobRunning、JobFailed、JobSucceeded など)。 |
| ExecutionState | ジョブ (実行) の状態 (例: 処理待ち、実行中、成功、失敗) |
| ErrorDetails | ジョブ エラーの詳細 |
| CreationApiVersion | ジョブの作成に使用される API バージョン |
| ClusterResourceGroupName | クラスターのリソース グループ名 |
| TFWorkerCount | TF worker の数 |
| TFParameterServerCount | TF パラメーター サーバーの数 |
| ToolType | 使用するツールの種類 |
| RunInContainer | ジョブをコンテナー内で実行する必要があるかどうかを示すフラグ |
| JobErrorMessage | ジョブ エラーの詳細なメッセージ |
| NodeId | ジョブが実行されている場所で作成されたノードの ID |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents テーブル

| プロパティ | 説明 |
|:--- |:--- |
| TimeGenerated | ログ エントリが生成された時刻 |
| OperationName | ログ イベントに関連付けられた操作の名前 |
| カテゴリ | ログ イベントの名前、AmlComputeClusterNodeEvent |
| ProvisioningState | クラスターのプロビジョニング状態 |
| ClusterName | クラスターの名前 |
| ClusterType | クラスターの種類 |
| CreatedBy | クラスターを作成したユーザー |
| CoreCount | クラスター内のコアの数 |
| VmSize | クラスターの VM サイズ |
| VmPriority | クラスターの内部で作成されたノードの優先度: Dedicated/LowPriority |
| ScalingType | クラスターのスケーリングの種類: 手動/自動 |
| InitialNodeCount | クラスターの初期のノード数 |
| MinimumNodeCount | クラスターの最小ノード数 |
| MaximumNodeCount | クラスターの最大ノード数 |
| NodeDeallocationOption | ノードの割り当てを解除する方法 |
| Publisher | クラスターの種類の発行元 |
| プラン | クラスターの作成に使用されるプラン |
| Sku | クラスター内に作成されたノード/VM の SKU |
| Version | ノード/VM の作成時に使用されるイメージのバージョン |
| SubnetId | クラスターの SubnetId |
| AllocationState | クラスター割り当ての状態 |
| CurrentNodeCount | クラスターの現在のノード数 |
| TargetNodeCount | スケール アップ/スケール ダウン中のクラスターのターゲット ノード数 |
| EventType | クラスター作成時のイベントの種類。 |
| NodeIdleTimeSecondsBeforeScaleDown | クラスターをスケール ダウン前のアイドル時間 (秒) |
| PreemptedNodeCount | クラスターにおける割り込まれたノード数 |
| IsResizeGrow | クラスターがスケール アップされていることを示すフラグ |
| VmFamilyName | クラスター内で作成できるノードの VM ファミリの名前 |
| LeavingNodeCount | クラスターの終了中のノード数 |
| UnusableNodeCount | クラスターの使用できないノードの数 |
| IdleNodeCount | クラスターのアイドル状態のノード数 |
| RunningNodeCount | クラスターの実行中のノード数 |
| PreparingNodeCount | クラスターの準備中のノード数 |
| QuotaAllocated | クラスターに割り当てられたクォータ |
| QuotaUtilized | クラスターの使用済みクォータ |
| AllocationStateTransitionTime | ある状態から別の状態への移行時間 |
| ClusterErrorCodes | クラスターの作成中またはスケーリング中に受信したエラー コード |
| CreationApiVersion | クラスターの作成時に使用される API バージョン |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents テーブル

| プロパティ | 説明 |
|:--- |:--- |
| TimeGenerated | ログ エントリが生成された時刻 |
| OperationName | ログ イベントに関連付けられた操作の名前 |
| カテゴリ | ログ イベントの名前、AmlComputeClusterNodeEvent |
| ClusterName | クラスターの名前 |
| NodeId | 作成されたクラスター ノードの ID |
| VmSize | ノードの VM サイズ |
| VmFamilyName | ノードが属している VM ファミリ |
| VmPriority | 作成されたノードの優先順位: Dedicated/LowPriority |
| Publisher | VM イメージの発行元 (例: microsoft-dsvm) |
| プラン | VM 作成に関連付けられているプラン |
| Sku | 作成されたノード/VM の SKU |
| Version | ノード/VM の作成時に使用されるイメージのバージョン |
| ClusterCreationTime | クラスターが作成された時刻 |
| ResizeStartTime | クラスターのスケール アップ/スケール ダウンが開始された時刻 |
| ResizeEndTime | クラスターのスケール アップ/スケール ダウンが終了した時刻 |
| NodeAllocationTime | ノードが割り当てられた時刻 |
| NodeBootTime | ノードが起動された時刻 |
| StartTaskStartTime | タスクがノードに割り当てられて開始された時刻 |
| StartTaskEndTime | ノードに割り当てられたタスクが終了した時刻 |
| TotalE2ETimeInSeconds | ノードがアクティブだった合計時間 |

### <a name="metrics"></a>メトリック

次の表に、Azure Machine Learning に対して収集されたプラットフォーム メトリックを示します。すべてのメトリックは、名前空間 **Azure Machine Learning ワークスペース**に格納されます。

**Model**

| メトリック | ユニット | 説明 |
| ----- | ----- | ----- |
| モデルのデプロイが失敗しました | Count | 失敗したモデルのデプロイの数。 |
| 開始されたモデル デプロイ | Count | 開始されたモデル デプロイの数。 |
| モデルのデプロイが成功しました | Count | 成功したモデル デプロイの数。 |
| モデルの登録が失敗しました | Count | 失敗したモデル登録の数。 |
| モデルの登録が成功しました | Count | 成功したモデル登録の数。 |

**[クォータ]**

クォータ情報は Azure Machine Learning のコンピューティングのみを対象としています。

| メトリック | ユニット | 説明 |
| ----- | ----- | ----- |
| アクティブなコア | Count | アクティブなコンピューティング コアの数。 |
| アクティブなノード | Count | アクティブなノードの数。 |
| アイドル状態のコア | Count | アイドル状態のコンピューティング コアの数。 |
| アイドル状態のノード | Count | アイドル状態のコンピューティング ノードの数。 |
| 終了中のコア | Count | 終了中のコアの数。 |
| 終了中のノード | Count | 終了中のノードの数。 |
| 割り込まれたコア | Count | 割り込まれたコアの数。 |
| Preempted Node (割り込まれたノード) | Count | 割り込まれたノードの数。 |
| クォータ使用率 | Percent | 使用されているクォータの割合。 |
| コア総数 | Count | コア総数。 |
| ノード総数 | Count | ノード総数。 |
| 使用できないコア | Count | 使用できないコアの数。 |
| 使用できないノード | Count | 使用できないノードの数。 |

クォータ メトリックをフィルター処理するのに使用できるディメンションを次に示します。

| Dimension | 利用可能なメトリック | 説明 |
| ---- | ---- | ---- |
| クラスター名 | すべてのクォータ メトリック | コンピューティング インスタンスの名前。 |
| VM ファミリ名 | クォータ使用率 | クラスターによって使用される VM ファミリの名前。 |
| VM 優先度 | クォータ使用率 | VM の優先度。

**[実行]**

トレーニングの実行に関する情報。

| メトリック | ユニット | 説明 |
| ----- | ----- | ----- |
| 完了した実行 | Count | 完了した実行の数。 |
| 失敗した実行 | Count | 失敗した実行の数。 |
| 開始された実行 | Count | 開始された実行の数。 |

実行メトリックをフィルター処理するのに使用できるディメンションを次に示します。

| Dimension | 説明 |
| ---- | ---- |
| ComputeType | 実行で使用されるコンピューティングの種類。 |
| PipelineStepType | 実行で使用される [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) の種類。 |
| PublishedPipelineId | 実行で使用される公開済みパイプラインの ID。 |
| RunType | 実行の種類。 |

RunType ディメンションの有効な値は次のとおりです。

| Value | 説明 |
| ----- | ----- |
| 実験 | パイプライン以外の実行。 |
| PipelineRun | StepRun の親であるパイプラインの実行。 |
| StepRun | パイプライン ステップの実行。 |
| ReusedStepRun | 前回の実行を再利用するパイプライン ステップの実行。 |

## <a name="see-also"></a>参照

- Azure Machine Learning の説明については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](/azure/azure-monitor/insights/monitor-azure-resource)」を参照してください。
