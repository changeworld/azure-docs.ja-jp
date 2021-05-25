---
title: コストの管理を計画する
titleSuffix: Azure Machine Learning
description: Azure portal のコスト分析を使用して、Azure Machine Learning のコストを計画および管理します。 ML モデルを構築する場合のコストを削減するための、さらなるコスト節約のヒントについて説明します。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 0ad5c432cb9ba5742ae4fa2ee2e41dc2ff2b5936
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109713502"
---
# <a name="plan-to-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning のコストの管理を計画する

この記事では、Azure Machine Learning のコストを計画して管理する方法について説明します。 リソースを追加する前に、まず、コストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。 最後に、マネージド Azure Machine Learning コンピューティング クラスターでモデルをトレーニングするときに、コスト節約のヒントを使用します。

Azure Machine Learning リソースを使い始めた後は、コスト管理機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を明らかにして、対処した方がよい部分を識別します。

Azure Machine Learning のコストは、Azure の月額請求料金の一部でしかないことを理解してください。 Azure の他のサービスを使用している場合は、サードパーティのサービスを含め、お使いの Azure サブスクリプションで使用されている Azure のすべてのサービスとリソースに対して課金されます。 この記事では、Azure Machine Learning のコストを計画および管理する方法について説明します。 Azure Machine Learning のコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理します。

機械学習モデルをトレーニングするときは、マネージド Azure Machine Learning コンピューティング クラスターを使用して、さらにコストを節約するためのヒントを活用します。

* 自動スケーリングされるようにトレーニング クラスターを構成する
* サブスクリプションとワークスペースにクォータを設定する
* トレーニングの実行に終了ポリシーを設定する
* 優先順位の低い仮想マシン (VM) を使用する
* Azure 予約 VM インスタンスを使用する

## <a name="prerequisites"></a>前提条件

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 

コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Azure Machine Learning を使用する前にコストを見積もる

- Azure Machine Learning ワークスペースにリソースを作成する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。
左側で、 **[AI + 機械学習]** を選択してから、 **[Azure Machine Learning]** を選択して始めます。  

次のスクリーンショットでは、計算ツールを使用したコストの見積もりを示します。

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 料金計算ツールの推定コストを示す例。":::

ワークスペースに新しいリソースを追加したら、この計算ツールに戻り、同じリソースをここに追加して、コストの見積もりを更新します。

詳細については、「[Azure Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Azure Machine Learning の詳細な課金モデルを理解する

Azure Machine Learning は、新しいリソースをデプロイするときに Azure Machine Learning と共にコストが発生する Azure インフラストラクチャ上で実行されます。 追加のインフラストラクチャでコストが発生する可能性があることを理解しておくことが重要です。 デプロイされたリソースに変更を加える場合は、このコストを管理する必要があります。 






### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Azure Machine Learning で通常発生するコスト

Azure Machine Learning ワークスペースのリソースを作成すると、他の Azure サービスのリソースも作成されます。 これらは次のとおりです。

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Basic アカウント
* [Azure ブロック BLOB ストレージ](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (General Purpose v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>リソースの削除後にコストが発生する可能性がある

Azure portal または Azure CLI で Azure Machine Learning ワークスペースを削除した後も、次のリソースは引き続き存在します。 これらを削除するまで、これらのコストは発生し続けます。

* Azure Container Registry
* Azure ブロック BLOB ストレージ
* Key Vault
* Application Insights

これらの依存リソースと共にワークスペースを削除するには、SDK を使用します。

```python
ws.delete(delete_dependent_resources=True)
```

ワークスペースに Azure Kubernetes Service (AKS) を作成する場合、またはワークスペースにコンピューティング リソースをアタッチする場合は、[Azure portal](https://portal.azure.com) で個別に削除する必要があります。

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure Machine Learning で Azure 前払いクレジットを使用する

Azure Machine Learning の料金は、Azure 前払いのクレジットで支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

<!-- Note for Azure service writer: If your service shows estimated costs when a user is creating resources in the Azure portal, at a minimum, insert this section as a brief walkthrough that steps through creating a Azure Machine Learning resource where the estimated cost is shown to the user, updated for your service. Add a screenshot where the estimated costs or subscription credits are shown.

If your service doesn't show costs as they create a resource or if estimated costs aren't shown to users before they use your service, then omit this section.

For example, you might start with the following (modify for your service):
-->

Azure Machine Learning のコンピューティング リソースを作成すると、推定コストが表示されます。

*コンピューティング インスタンス* を作成し、推定価格を表示するには、次の操作を行います。

1. [Azure Machine Learning スタジオ](https://ml.azure.com)にサインインします
1. 左側にある **[コンピューティング]** を選択します。
1. 上部のツール バーで、 **[+新規]** を選択します。
1. 使用可能な仮想マシンのサイズごとに表示される推定価格を確認します。
1. リソースの作成を完了します。


:::image type="content" source="media/concept-plan-manage-cost/create-resource.png" alt-text="コンピューティング インスタンス作成中に推定コストが表示される例。" lightbox="media/concept-plan-manage-cost/create-resource.png" :::

Azure サブスクリプションに使用制限がある場合は、Azure により、クレジット額を超える支出が防止されます。 Azure リソースを作成して使用するときに、クレジットが使用されます。 クレジットの上限に達すると、その請求期間の残りの期間は、デプロイしたリソースが無効にされます。 クレジットの上限は変更できませんが、上限を取り除くことは可能です。 使用制限の詳細については、「[Azure の使用制限](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。

## <a name="monitor-costs"></a>コストを監視する

Azure Machine Learning で Azure リソースを使用すると、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Azure Machine Learning の使用が開始されるとすぐにコストが発生し、[コスト分析](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

コスト分析を使用すると、さまざまな期間について、Azure Machine Learning のコストがグラフや表で表示されます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

Azure Machine Learning のコストをコスト分析で表示するには、次の操作を行います。

1. Azure portal にサインインします。
2. Azure portal でスコープを開き、メニューから **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。
3. 既定では、最初のドーナツ グラフにサービスのコストが表示されます。 グラフの [Azure Machine Learning] というラベルが付いた領域を選択します。

最初にコスト分析を開いたときに、実際の月額コストが表示されます。 月単位のすべての使用コストを示す例を次に示します。

:::image type="content" source="media/concept-plan-manage-cost/all-costs.png" alt-text="サブスクリプションの累積コストを示す例。" lightbox="media/concept-plan-manage-cost/all-costs.png" :::


Azure Machine Learning などの 1 つのサービスのコストに絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[Azure Machine Learning]** を選択します。

Azure Machine Learning のみのコストが表示される例を次に示します。

:::image type="content" source="media/concept-plan-manage-cost/vm-specific-cost.png" alt-text="ServiceName の累積コストを示す例。" lightbox="media/concept-plan-manage-cost/vm-specific-cost.png" :::

<!-- Note to Azure service writer: The image shows an example for Azure Storage. Replace the example image with one that shows costs for your service. -->

前の例では、サービスの現在のコストが表示されます。 Azure リージョン (場所) 別およびリソース グループ別の Azure Machine Learning のコストも表示されます。 ここでは、コストを自分で調べることができます。
## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときのフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Azure Machine Learning のコストを管理および削減するその他の方法

機械学習のコンピューティング リソースのコストを抑えるには、次のヒントを参考にしてください。

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning コンピューティング クラスター (AmlCompute) を使用する

データは絶えず変化するので、モデルのトレーニングと再トレーニングを短時間で実行できるように合理化し、正確なモデルを維持する必要があります。 ただし、継続的にトレーニングを行うとコストがかかり、GPU 上でのディープ ラーニング モデルの場合は特にそうです。 

Azure Machine Learning のユーザーは、マネージド Azure Machine Learning コンピューティング クラスター (AmlCompute とも呼ばれます) を使用できます。 AmlCompute では、GPU と CPU のさまざまなオプションがサポートされています。 AmlCompute は、Azure Machine Learning によってサブスクリプションの代わりに内部でホストされます。 Azure IaaS クラウド スケールで、同じエンタープライズ レベルのセキュリティ、コンプライアンス、ガバナンスが提供されます。

これらのコンピューティング プールは Azure の IaaS インフラストラクチャ内にあるため、インフラストラクチャの他の部分と同じセキュリティ要件とコンプライアンス要件で、トレーニングをデプロイ、スケーリング、管理できます。  これらのデプロイは、お客様のサブスクリプションで実行され、ガバナンス ルールに従います。 詳しくは、「[Azure Machine Learning コンピューティング](how-to-create-attach-compute-cluster.md)」をご覧ください。

### <a name="configure-training-clusters-for-autoscaling"></a>自動スケーリングされるようにトレーニング クラスターを構成する

ワークロードの要件に基づいてクラスターを自動スケーリングすると、必要なものだけが使用されるようにして、コストを削減できます。

AmlCompute クラスターは、ワークロードに基づいて動的にスケーリングするように設計されています。 クラスターは、構成するノードの最大数までスケールアップできます。 実行が完了するたびに、クラスターによってノードが解放され、構成されている最小ノード数にスケーリングされます。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

また、スケールダウンまでのノードのアイドル時間を構成することもできます。 既定では、スケールダウンまでのアイドル時間は 120 秒に設定されます。

+ 反復の少ない実験を実行する場合は、この時間を短くしてコストを節約します。
+ 反復の多い開発またはテストの実験を実行する場合は、トレーニング スクリプトまたは環境を変更するたびに、常にスケールアップとスケールダウンの料金が発生しないように、時間を長くする必要がある場合があります。

AmlCompute クラスターは、[AmlCompute SDK クラス](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)、[AmlCompute CLI](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute)、[REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable) を使用して、Azure portal の変化するワークロード要件に合わせて構成できます。

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>リソースにクォータを設定する

AmlCompute には、[クォータ (制限) の構成](how-to-manage-quotas.md#azure-machine-learning-compute)が付随します。 このクォータは VM ファミリ (たとえば、Dv2 シリーズ、NCv3 シリーズ) 別であり、各サブスクリプションのリージョンによって異なります。 サブスクリプションは小さい既定値で開始されますが、この設定を使用して、サブスクリプションでのスピンアップに使用できる AmlCompute リソースの量を制御できます。 

また、サブスクリプション内の各ワークスペースに対し、[VM ファミリ別のワークスペース レベル クォータ](how-to-manage-quotas.md#workspace-level-quotas)を構成します。 それにより、各ワークスペースで発生する可能性があるコストをより細かく制御し、特定の VM ファミリを制限できます。 

ワークスペース レベルでクォータを設定するには、[Azure portal](https://portal.azure.com) で始めます。  サブスクリプション内の任意のワークスペースを選択し、左側のペインで **[Usages + quotas]\(使用量 + クォータ\)** を選択します。 次に、 **[クォータの構成]** タブを選択して、クォータを表示します。 このクォータは、複数のワークスペースに影響する設定であるため、設定するにはサブスクリプション スコープの特権が必要です。

### <a name="set-run-autotermination-policies"></a>実行の自動終了ポリシーを設定する 

場合によっては、期間を制限したり、早期に終了したりするようにトレーニングの実行を構成する必要があります。 たとえば、Azure Machine Learning の組み込みのハイパーパラメーターのチューニングや自動機械学習を使用している場合などです。

使用できるオプションを次に示します。
* 選択したコンピューティング (ローカルまたはリモートのクラウド コンピューティング) で実行を延長できる最長期間を制御するには、RunConfiguration で `max_run_duration_seconds` という名前のパラメーターを定義します。
* [ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md#early-termination)の場合は、バンディット ポリシー、中央値の停止ポリシー、または切り捨て選択ポリシーから、早期終了ポリシーを定義します。 ハイパーパラメーター スイープをさらに制御するには、`max_total_runs` や `max_duration_minutes` などのパラメーターを使用します。
* [自動機械学習](how-to-configure-auto-train.md#exit)の場合は、`enable_early_stopping` フラグを使用して同様の終了ポリシーを設定します。 また、`iteration_timeout_minutes` や `experiment_timeout_minutes` などのプロパティを使用して、実行や実験全体の最長期間を制御します。

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 優先順位の低い VM を使用する

Azure では、仮想マシン スケール セット、Batch、および Machine Learning service 間で、過剰な未使用容量を低優先順位の VM として使用できます。 これらの割り当ては先取り可能ですが、専用の VM と比較して低価格で提供されます。 一般に、Batch ワークロードには優先順位の低い VM を使用することをお勧めします。 また、再送信 (Batch 推論の場合) または再起動 (チェックポイントを使用したディープ ラーニング トレーニングの場合) によって中断を回復できる場合にもそれらを使用します。

低優先順位の VM には、専用のクォータ値とは別に、VM ファミリ別の 1 つのクォータがあります。 詳しくは、[AmlCompute のクォータ](how-to-manage-quotas.md)に関する記事をご覧ください。

 コンピューティング インスタンスは、対話型のノートブック エクスペリエンスをサポートする必要があるため、低優先順位の VM では機能しません。

### <a name="use-reserved-instances"></a>予約インスタンスを使用する

コンピューティング リソースのコストを削減するもう 1 つの方法は、Azure 予約 VM インスタンスです。 このオファリングでは、1 年間または 3 年間の契約を結びます。 これらの割引は、従量課金制料金の最大 72% までの範囲で、毎月の Azure の請求に直接適用されます。

Azure Machine Learning コンピューティングでは、予約インスタンスがもともとサポートされています。 1 年間または 3 年間の予約インスタンスを購入すると、Azure Machine Learning のマネージド コンピューティングに自動的に割引が適用されます。


## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。
