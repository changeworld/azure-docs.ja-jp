---
title: コストの管理と最適化
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の機械学習モデルを構築する際のコストを最適化するためのヒントについて学習する
author: luisquintanilla
ms.author: luquinta
ms.custom: subject-cost-optimization
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 47f10b542bd2c4eb7965e6dfc25349661ca3c3d8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805242"
---
# <a name="manage-and-optimize-azure-machine-learning-costs"></a>Azure Machine Learning のコストを管理して最適化する

Azure Machine Learning に対して機械学習モデルをトレーニングして、デプロイする際のコストを管理して最適化する方法について説明します。

次のヒントを使用して、コンピューティング リソースのコストを管理して最適化します。

- 自動スケーリングされるようにトレーニング クラスターを構成する
- サブスクリプションとワークスペースにクォータを設定する
- トレーニングの実行に終了ポリシーを設定する
- 優先順位の低い仮想マシン (VM) を使用する
- 自動的にシャットダウンおよび起動するようにコンピューティング インスタンスのスケジュールを設定する
- Azure 予約 VM インスタンスを使用する
- ローカルでトレーニングする
- トレーニングの並列化
- データ保持と削除のポリシーを設定する
- リソースを同じリージョンにデプロイする

コストの計画と監視については、「[Azure Machine Learning のコストの管理を計画する](concept-plan-manage-cost.md)」ガイドを参照してください。

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning コンピューティング クラスター (AmlCompute) を使用する

データは絶えず変化するので、モデルのトレーニングと再トレーニングを短時間で実行できるように合理化し、正確なモデルを維持する必要があります。 ただし、継続的にトレーニングを行うとコストがかかり、GPU 上でのディープ ラーニング モデルの場合は特にそうです。 

Azure Machine Learning のユーザーは、マネージド Azure Machine Learning コンピューティング クラスター (AmlCompute とも呼ばれます) を使用できます。 AmlCompute では、GPU と CPU のさまざまなオプションがサポートされています。 AmlCompute は、Azure Machine Learning によってサブスクリプションの代わりに内部でホストされます。 Azure IaaS クラウド スケールで、同じエンタープライズ レベルのセキュリティ、コンプライアンス、ガバナンスが提供されます。

これらのコンピューティング プールは Azure の IaaS インフラストラクチャ内にあるため、インフラストラクチャの他の部分と同じセキュリティ要件とコンプライアンス要件で、トレーニングをデプロイ、スケーリング、管理できます。  これらのデプロイは、お客様のサブスクリプションで実行され、ガバナンス ルールに従います。 詳しくは、「[Azure Machine Learning コンピューティング](how-to-create-attach-compute-cluster.md)」をご覧ください。

## <a name="configure-training-clusters-for-autoscaling"></a>自動スケーリングされるようにトレーニング クラスターを構成する

ワークロードの要件に基づいてクラスターを自動スケーリングすると、必要なものだけが使用されるようにして、コストを削減できます。

AmlCompute クラスターは、ワークロードに基づいて動的にスケーリングするように設計されています。 クラスターは、構成するノードの最大数までスケールアップできます。 実行が完了するたびに、クラスターによってノードが解放され、構成されている最小ノード数にスケーリングされます。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

また、スケールダウンまでのノードのアイドル時間を構成することもできます。 既定では、スケールダウンまでのアイドル時間は 120 秒に設定されます。

+ 反復の少ない実験を実行する場合は、この時間を短くしてコストを節約します。
+ 反復の多い開発またはテストの実験を実行する場合は、トレーニング スクリプトまたは環境を変更するたびに、常にスケールアップとスケールダウンの料金が発生しないように、時間を長くする必要がある場合があります。

AmlCompute クラスターは、[AmlCompute SDK クラス](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)、[AmlCompute CLI](/cli/azure/ml(v1)/computetarget/create#az_ml_v1__computetarget_create_amlcompute)、[REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable) を使用して、Azure portal の変化するワークロード要件に合わせて構成できます。

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>リソースにクォータを設定する

AmlCompute には、[クォータ (制限) の構成](how-to-manage-quotas.md#azure-machine-learning-compute)が付随します。 このクォータは VM ファミリ (たとえば、Dv2 シリーズ、NCv3 シリーズ) 別であり、各サブスクリプションのリージョンによって異なります。 サブスクリプションは小さい既定値で開始されますが、この設定を使用して、サブスクリプションでのスピンアップに使用できる AmlCompute リソースの量を制御できます。 

また、サブスクリプション内の各ワークスペースに対し、[VM ファミリ別のワークスペース レベル クォータ](how-to-manage-quotas.md#workspace-level-quotas)を構成します。 それにより、各ワークスペースで発生する可能性があるコストをより細かく制御し、特定の VM ファミリを制限できます。 

ワークスペース レベルでクォータを設定するには、[Azure portal](https://portal.azure.com) で始めます。  サブスクリプション内の任意のワークスペースを選択し、左側のペインで **[Usages + quotas]\(使用量 + クォータ\)** を選択します。 次に、 **[クォータの構成]** タブを選択して、クォータを表示します。 このクォータは、複数のワークスペースに影響する設定であるため、設定するにはサブスクリプション スコープの特権が必要です。

## <a name="set-run-autotermination-policies"></a>実行の自動終了ポリシーを設定する 

場合によっては、期間を制限したり、早期に終了したりするようにトレーニングの実行を構成する必要があります。 たとえば、Azure Machine Learning の組み込みのハイパーパラメーターのチューニングや自動機械学習を使用している場合などです。

使用できるオプションを次に示します。
* 選択したコンピューティング (ローカルまたはリモートのクラウド コンピューティング) で実行を延長できる最長期間を制御するには、RunConfiguration で `max_run_duration_seconds` という名前のパラメーターを定義します。
* [ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md#early-termination)の場合は、バンディット ポリシー、中央値の停止ポリシー、または切り捨て選択ポリシーから、早期終了ポリシーを定義します。 ハイパーパラメーター スイープをさらに制御するには、`max_total_runs` や `max_duration_minutes` などのパラメーターを使用します。
* [自動機械学習](how-to-configure-auto-train.md#exit)の場合は、`enable_early_stopping` フラグを使用して同様の終了ポリシーを設定します。 また、`iteration_timeout_minutes` や `experiment_timeout_minutes` などのプロパティを使用して、実行や実験全体の最長期間を制御します。

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 優先順位の低い VM を使用する

Azure では、仮想マシン スケール セット、Batch、および Machine Learning service 間で、過剰な未使用容量を低優先順位の VM として使用できます。 これらの割り当ては先取り可能ですが、専用の VM と比較して低価格で提供されます。 一般に、Batch ワークロードには優先順位の低い VM を使用することをお勧めします。 また、再送信 (Batch 推論の場合) または再起動 (チェックポイントを使用したディープ ラーニング トレーニングの場合) によって中断を回復できる場合にもそれらを使用します。

低優先順位の VM には、専用のクォータ値とは別に、VM ファミリ別の 1 つのクォータがあります。 詳しくは、[AmlCompute のクォータ](how-to-manage-quotas.md)に関する記事をご覧ください。

 コンピューティング インスタンスは、対話型のノートブック エクスペリエンスをサポートする必要があるため、低優先順位の VM では機能しません。

## <a name="schedule-compute-instances"></a>コンピューティング インスタンスのスケジュールを設定する

[コンピューティング インスタンス](concept-compute-instance.md)を作成する場合、VM はそのまま維持されるため、ご自身の作業に使用できます。  コンピューティング インスタンスが自動的に開始および停止されるように (プレビュー) [スケジュールを設定](how-to-create-manage-compute-instance.md#schedule)して、使用する予定がないときのコストを節約します。

## <a name="use-reserved-instances"></a>予約インスタンスを使用する

コンピューティング リソースのコストを削減するもう 1 つの方法は、Azure 予約 VM インスタンスです。 このオファリングでは、1 年間または 3 年間の契約を結びます。 これらの割引は、従量課金制料金の最大 72% までの範囲で、毎月の Azure の請求に直接適用されます。

Azure Machine Learning コンピューティングでは、予約インスタンスがもともとサポートされています。 1 年間または 3 年間の予約インスタンスを購入すると、Azure Machine Learning のマネージド コンピューティングに自動的に割引が適用されます。

## <a name="train-locally"></a>ローカルでトレーニングする

お使いのローカル コンピューターで実行できるくらい小さなトレーニング ジョブのプロトタイプを作成して実行する場合は、ローカルでのトレーニングを検討してください。 Python SDK を使用して、コンピューティング先を `local` に設定すると、スクリプトがローカルで実行されます。 詳細については、「[トレーニングの実行を構成して送信する](how-to-set-up-training-targets.md#select-a-compute-target)」を参照してください。

Visual Studio Code では、機械学習アプリケーションを開発するためのフル機能を備えた環境を用意できます。 Azure Machine Learning の Visual Studio Code 拡張機能と Docker を使用すると、ローカルで実行してデバッグできます。 詳細については、「[Visual Studio Code を使用した対話型デバッグ](how-to-debug-visual-studio-code.md)」を参照してください。

## <a name="parallelize-training"></a>トレーニングの並列化

コストとパフォーマンスを最適化する主な方法の 1 つは、ワークロードの Azure Machine Learning で並列実行ステップを使用してワークロードを並列化しすることです。 この手順では、多数の小さなノードを使用してタスクを並列実行できます。そのため、水平方向にスケーリングできます。 並列化にはオーバーヘッドがあります。 ワークロードと、実現できる並列処理の程度によっては、これがオプションになる場合と使用できない場合があります。 詳細については、[ParallelRunStep](xref:azureml.contrib.pipeline.steps.ParallelRunStep) のドキュメントを参照してください。

## <a name="set-data-retention--deletion-policies"></a>データ保持と削除のポリシーを設定する

パイプラインが実行されるたび、各ステップで中間データセットが生成されます。 時間が経過すると、これらの中間データセットはストレージ アカウントの領域を使用します。 データセットをアーカイブおよび削除するには、ライフサイクル全体を通してデータを管理するポリシーを設定することを検討します。 詳細については、「[Azure Blob Storage アクセス層の自動化によるコストの最適化](../storage/blobs/lifecycle-management-overview.md)」を参照してください。

## <a name="deploy-resources-to-the-same-region"></a>リソースを同じリージョンにデプロイする

異なるリージョンにあるコンピューティングでは、ネットワークの待ち時間が発生し、データ転送コストが増加する可能性があります。 Azure ネットワーク コストは、Azure データ センターからの送信帯域幅から発生します。 ネットワーク コストを削減するには、リージョン内のすべてのリソースをデプロイします。 データと同じリージョンにある Azure Machine Learning ワークスペースと依存リソースをプロビジョニングすると、コストを削減し、パフォーマンスを向上させることができます。

ExpressRoute を使用するハイブリッド クラウドのシナリオでは、すべてのリソースを Azure に移動してネットワークのコストと待ち時間を最適化する方がコスト効率が高くなる場合があります。

## <a name="next-steps"></a>次のステップ

- [Azure Machine Learning のコストの管理を計画する](concept-plan-manage-cost.md)
- [組織規模での Azure Machine Learning の予算、コスト、クォータを管理する](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)