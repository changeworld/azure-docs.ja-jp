---
title: Azure Machine Learning データの監視のリファレンス | Microsoft Docs
description: Azure Machine Learning の監視に関するリファレンス ドキュメント。 Azure Monitor で収集して利用できるデータとリソースについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/21/2021
ms.openlocfilehash: a4af35e4eded2a715ada162f565ee265c0c2be74
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560140"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Azure Machine Learning データの監視のリファレンス

ご利用の Azure Machine Learning ワークスペースから Azure Monitor によって収集されるデータとリソースについて説明します。 監視データの収集と分析の詳細については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。

## <a name="metrics"></a>メトリック

このセクションには、Azure Machine Learning 用に自動的に収集されたすべてのプラットフォーム メトリックが一覧表示されています。 これらのメトリックのリソース プロバイダーは、[Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) です。

**Model**

| メトリック | ユニット | 説明 |
|--|--|--|
| モデル登録成功 | Count | このワークスペースで成功したモデル登録の数 |
| モデル登録失敗 | Count | このワークスペースで失敗したモデル登録の数 |
| モデル デプロイ開始 | Count | このワークスペースで開始されたモデル デプロイの数 |
| モデル デプロイが成功しました | Count | このワークスペースで成功したモデル デプロイの数 |
| モデル デプロイ失敗 | Count | このワークスペースで失敗したモデル デプロイの数 |

**[クォータ]**

クォータ情報は Azure Machine Learning のコンピューティングのみを対象としています。

| メトリック | ユニット | 説明 |
|--|--|--|
| ノード総数 | Count | ノードの合計数。 この合計には、アクティブ ノード、アイドル状態のノード、使用できないノード、割り込まれたノード、終了中のノードなどが含まれます |
| アクティブなノード | Count | アクティブなノードの数。 ジョブをアクティブに実行しているノード。 |
| アイドル状態のノード | Count | アイドル状態のノードの数。 アイドル状態のノードは、ジョブを実行していないノードですが、使用可能な場合は新しいジョブを受け入れることができます。 |
| 使用できないノード | Count | 使用できないノードの数。 使用できないノードは、いくつかの問題が解決されていないため、機能していません。 これらのノードは Azure によってリサイクルされます。 |
| 割り込まれたノード | Count | 割り込まれたノードの数。 これらのノードは低優先度のノードであり、使用可能なノード プールから外されます。 |
| 終了中のノード | Count | 終了中のノードの数。 終了中のノードは、ジョブの処理を完了したばかりで、アイドル状態になるノードです。 |
| コアの合計 | Count | コアの合計数 |
| アクティブなコア | Count | アクティブなコアの数 |
| アイドル状態のコア | Count | アイドル状態のコアの数 |
| 使用できないコア | Count | 使用できないコアの数 |
| 割り込まれたコア | Count | 割り込まれたコアの数 |
| 終了中のコア | Count | 終了中のコアの数 |
| クォータ使用率 | Count | クォータ使用率 (%) |

**リソース**

| メトリック| ユニット | 説明 |
|--|--|--|
| CpuUtilization | Count | CPU ノードの使用率 (%)。 使用率は 1 分間隔で報告されます。 |
| GpuUtilization | Count | GPU ノードの使用率 (%)。 使用率は 1 分間隔で報告されます。 |
| GpuMemoryUtilization | Count | GPU ノードのメモリ使用率 (%)。 使用率は 1 分間隔で報告されます。 |
| GpuEnergyJoules | Count | GPU ノードでのコンセントのエネルギーの間隔 (ジュール単位)。 エネルギーは 1 分間隔で報告されます。 |

**[実行]**

ワークスペースのトレーニング実行に関する情報。

| メトリック | ユニット | 説明 |
|--|--|--|
| Cancelled Runs (取り消された実行数) | Count | このワークスペースに対して取り消された実行の数。 実行が正常に取り消されたときに、カウントが更新されます。 |
| Cancel Requested Runs (キャンセルが要求された実行数) | Count | このワークスペースに対してキャンセルが要求された実行の数。 実行のキャンセル要求が受信されたときに、カウントが更新されます。 |
| 完了した実行数 | Count | このワークスペースに対して正常に完了した実行の数。 実行が完了し、出力が収集されたときに、カウントが更新されます。 |
| 失敗した実行 | Count | このワークスペースに対して失敗した実行の数。 実行に失敗すると、カウントが更新されます。 |
| Finalizing Runs (終了処理中の実行数) | Count | このワークスペースに対して終了処理状態になった実行の数。 実行は完了しているものの、出力の収集がまだ進行中の場合に、カウントが更新されます。 | 
| Not Responding Runs (応答していない実行数) | Count | このワークスペースに対して応答していない実行の数。 実行が応答していない状態になったときに、カウントが更新されます。 |
| Not Started Runs (未開始の実行数) | Count | このワークスペースに対して未開始状態の実行の数。 実行を作成するために要求が受信されたものの、実行情報がまだ設定されていない場合に、カウントが更新されます。 |
| Preparing Runs (準備中の実行数) | Count | このワークスペースに対して準備中の実行の数。 実行環境の準備中に実行が準備状態になると、カウントが更新されます。 |
| Provisioning Runs (プロビジョニング中の実行数) | Count | このワークスペースに対してプロビジョニング中の実行の数。 実行でのコンピューティング先の作成の待機中、またはプロビジョニング中に、カウントが更新されます。 |
| Queued Runs (キューに入れられた実行数) | Count | このワークスペースに対してキューに入れられた実行の数。 コンピューティング先で実行がキューに入れられたときに、カウントが更新されます。 必要なコンピューティング ノードの準備が整うまで待機しているときに発生する場合があります。 |
| 開始した実行数 | Count | このワークスペースに対して実行されている実行の数。 必要なリソースに対して実行が開始されたときに、カウントが更新されます。 |
| Starting Runs (開始中の実行数) | Count | このワークスペースに対して開始された実行の数。 実行の作成要求の後、および実行 ID などの実行情報が設定された後に、カウントが更新されます |
| エラー | Count | このワークスペースの実行エラーの数。 実行時にエラーが発生するたびに、カウントが更新されます。 |
| 警告 | Count | このワークスペースの実行警告の数。 実行時に警告が発生するたびに、カウントが更新されます。 |

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。

Azure Machine Learning では、次のディメンションがメトリックに関連付けられています。

| Dimension | 説明 |
| ---- | ---- |
| クラスター名 | コンピューティング クラスター リソースの名前。 すべてのクォータ メトリックで使用できます。 |
| VM ファミリ名 | クラスターによって使用される VM ファミリの名前。 クォータ使用率で使用できます。 |
| VM 優先度 | VM の優先度。 クォータ使用率で使用できます。
| CreatedTime | CpuUtilization と GpuUtilization でのみ使用できます。 |
| deviceId | デバイスの ID (GPU)。 GpuUtilization でのみ使用できます。 |
| NodeId | ジョブが実行されている場所で作成されたノードの ID。 CpuUtilization と GpuUtilization でのみ使用できます。 |
| RunId | 実行/ジョブの ID。 CpuUtilization と GpuUtilization でのみ使用できます。 |
| ComputeType | 実行で使用されるコンピューティングの種類。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |
| PipelineStepType | 実行で使用される [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) の種類。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |
| PublishedPipelineId | 実行で使用される公開済みパイプラインの ID。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |
| RunType | 実行の種類。 完了した実行、失敗した実行、および開始された実行に対してのみ使用できます。 |

RunType ディメンションの有効な値は次のとおりです。

| 値 | 説明 |
| ----- | ----- |
| 実験 | パイプライン以外の実行。 |
| PipelineRun | StepRun の親であるパイプラインの実行。 |
| StepRun | パイプライン ステップの実行。 |
| ReusedStepRun | 前回の実行を再利用するパイプライン ステップの実行。 |

## <a name="activity-log"></a>アクティビティ ログ

次の表は、アクティビティ ログで作成される可能性がある Azure Machine Learning に関連する操作を示しています。

| 操作 | 説明 |
|:---|:---|
| Machine Learning ワークスペースを作成または更新します | ワークスペースが作成または更新されました |
| CheckComputeNameAvailability | コンピューティング名が既に使用されているかどうかを確認します |
| コンピューティング リソースを作成または更新します | コンピューティング リソースが作成または更新されました |
| コンピューティング リソースを削除します | コンピューティング リソースが削除されました |
| シークレットのリスト | Machine Learning ワークスペースの操作のシークレットのリスト |

## <a name="resource-logs"></a>リソース ログ

このセクションでは、Azure Machine Learning ワークスペース用に収集できるリソース ログの種類を示します。

リソース プロバイダーと種類:[Microsoft.MachineLearningServices/workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces)。

| カテゴリ | 表示名 |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |
| ModelsChangeEvent | ModelsChangeEvent |
| ModelsReadEvent | ModelsReadEvent |
| ModelsActionEvent | ModelsActionEvent |
| DeploymentReadEvent | DeploymentReadEvent |
| DeploymentEventACI | DeploymentEventACI |
| DeploymentEventAKS | DeploymentEventAKS |
| InferencingOperationAKS | InferencingOperationAKS |
| InferencingOperationACI | InferencingOperationACI |
| EnvironmentChangeEvent | EnvironmentChangeEvent |
| EnvironmentReadEvent | EnvironmentReadEvent |
| DataLabelChangeEvent | DataLabelChangeEvent |
| DataLabelReadEvent | DataLabelReadEvent |
| ComputeInstanceEvent | ComputeInstanceEvent |
| DataStoreChangeEvent | DataStoreChangeEvent |
| DataStoreReadEvent | DataStoreReadEvent |
| DataSetChangeEvent | DataSetChangeEvent |
| DataSetReadEvent | DataSetReadEvent |
| PipelineChangeEvent | PipelineChangeEvent |
| PipelineReadEvent | PipelineReadEvent |
| RunEvent | RunEvent |
| RunReadEvent | RunReadEvent |


## <a name="schemas"></a>スキーマ

次のスキーマは、Azure Machine Learning によって使用されています

### <a name="amlcomputejobevent-table"></a>AmlComputeJobEvent テーブル

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
| EventType | ジョブ イベントの種類。 たとえば、JobSubmitted、JobRunning、JobFailed、JobSucceeded など。 |
| ExecutionState | ジョブ (実行) の状態。 たとえば、キューに登録済み、実行中、成功、失敗など |
| ErrorDetails | ジョブ エラーの詳細 |
| CreationApiVersion | ジョブの作成に使用される API バージョン |
| ClusterResourceGroupName | クラスターのリソース グループ名 |
| TFWorkerCount | TF worker の数 |
| TFParameterServerCount | TF パラメーター サーバーの数 |
| ToolType | 使用するツールの種類 |
| RunInContainer | ジョブをコンテナー内で実行する必要があるかどうかを示すフラグ |
| JobErrorMessage | ジョブ エラーの詳細なメッセージ |
| NodeId | ジョブが実行されている場所で作成されたノードの ID |

### <a name="amlcomputeclusterevent-table"></a>AmlComputeClusterEvent テーブル

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

### <a name="amlcomputeclusternodeevent-table"></a>AmlComputeClusterNodeEvent テーブル

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
| Publisher | VM イメージの発行元。 たとえば、microsoft-dsvm など |
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

### <a name="amlcomputeinstanceevent-table"></a>AmlComputeInstanceEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlComputeInstanceEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| CorrelationId | 関連するイベントのセットをグループ化するために使用される GUID (該当する場合)。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlComputeInstanceName | "ログエントリに関連付けられているコンピューティング インスタンスの名前。 |

### <a name="amldatalabelevent-table"></a>AmlDataLabelEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlDataLabelEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| CorrelationId | 関連するイベントのセットをグループ化するために使用される GUID (該当する場合)。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlProjectId | AML プロジェクトの一意識別子。 |
| AmlProjectName | AML プロジェクトの名前。 |
| AmlLabelNames | プロジェクトに対して作成されるラベル クラス名。 |
| AmlDataStoreName | プロジェクトのデータが格納されているデータ ストアの名前。 |

### <a name="amldatasetevent-table"></a>AmlDataSetEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlDataSetEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| AmlWorkspaceId | AML ワークスペースの GUID と一意の ID。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlDatasetId | AML データ セットの ID。 |
| AmlDatasetName | AML データ セットの名前。 |

### <a name="amldatastoreevent-table"></a>AmlDataStoreEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlDataStoreEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| AmlWorkspaceId | AML ワークスペースの GUID と一意の ID。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlDatastoreName | AML データ ストアの名前。 |

### <a name="amldeploymentevent-table"></a>AmlDeploymentEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlDeploymentEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlServiceName | AML サービスの名前。 |

### <a name="amlinferencingevent-table"></a>AmlInferencingEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlInferencingEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlServiceName | AML サービスの名前。 |

### <a name="amlmodelsevent-table"></a>AmlModelsEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlModelsEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| ResultSignature | イベントの HTTP 状態コード。 一般的な値は、200、201、202 などです。 |
| AmlModelName | AML モデルの名前。 |

### <a name="amlpipelineevent-table"></a>AmlPipelineEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlPipelineEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| AmlWorkspaceId | AML ワークスペースの GUID と一意の ID。 |
| AmlWorkspaceId | AML ワークスペースの名前。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlModuleId | モジュールの GUID と一意の ID。|
| AmlModelName | AML モデルの名前。 |
| AmlPipelineId | AML パイプラインの ID。 |
| AmlParentPipelineId | 親 AML パイプラインの ID (複製の場合)。 |
| AmlPipelineDraftId | AML パイプライン ドラフトの ID。 |
| AmlPipelineDraftName | AML パイプライン ドラフトの名前。 |
| AmlPipelineEndpointId | AML パイプライン エンドポイントの ID。 |
| AmlPipelineEndpointName | AML パイプライン エンドポイントの名前。 |


### <a name="amlrunevent-table"></a>AmlRunEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlRunEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| ResultType | イベントの状態。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| AmlWorkspaceId | AML ワークスペースの GUID と一意の ID。 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| RunId | 実行の一意の ID。 |

### <a name="amlenvironmentevent--table"></a>AmlEnvironmentEvent テーブル

| プロパティ | 説明 |
|:--- |:--- |
| Type | ログ イベントの名前、AmlEnvironmentEvent |
| TimeGenerated | ログ エントリが生成された時刻 (UTC) |
| Level | イベントの重大度レベル。 Informational、Warning、Error、Critical のいずれかである必要があります。 |
| OperationName | ログ エントリに関連付けられた操作の名前 |
| ID | 操作を実行したユーザーまたはアプリケーションの ID。 |
| AadTenantId | 操作が送信された AAD テナント ID。 |
| AmlEnvironmentName | AML 環境構成の名前。 |
| AmlEnvironmentVersion | AML 環境構成バージョンの名前。 |


## <a name="see-also"></a>関連項目

- Azure Machine Learning の説明については、「[Azure Machine Learning の監視](monitor-azure-machine-learning.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
