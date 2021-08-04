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
ms.date: 06/08/2021
ms.openlocfilehash: 01c985b0554fe5955010c1c8c286f81f8de6d3ee
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006007"
---
# <a name="plan-to-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning のコストの管理を計画する

この記事では、Azure Machine Learning のコストを計画して管理する方法について説明します。 リソースを追加する前に、まず、コストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。 

Azure Machine Learning リソースを使い始めた後は、コスト管理機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を明らかにして、対処した方がよい部分を識別します。

Azure Machine Learning のコストは、Azure の月額請求料金の一部でしかないことを理解してください。 Azure の他のサービスを使用している場合は、サードパーティのサービスを含め、お使いの Azure サブスクリプションで使用されている Azure のすべてのサービスとリソースに対して課金されます。 この記事では、Azure Machine Learning のコストを計画および管理する方法について説明します。 Azure Machine Learning のコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理します。

コストの最適化の詳細については、[Azure Machine Learning でのコストの管理および最適化方法](how-to-manage-optimize-cost.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 

コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

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

Azure Machine Learning で Azure リソースを使用すると、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Azure Machine Learning の使用が開始されるとすぐにコストが発生し、[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

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

次のヒントを使用して、コンピューティング リソースのコストを管理して最適化します。

- 自動スケーリングされるようにトレーニング クラスターを構成する
- サブスクリプションとワークスペースにクォータを設定する
- トレーニングの実行に終了ポリシーを設定する
- 優先順位の低い仮想マシン (VM) を使用する
- Azure 予約 VM インスタンスを使用する
- ローカルでトレーニングする
- トレーニングを並列化する
- データ保持と削除のポリシーを設定する
- リソースを同じリージョンにデプロイする

詳細については、[Azure Machine Learning でのコストの管理と最適化](how-to-manage-optimize-cost.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Machine Learning でコストを管理して最適化する](how-to-manage-optimize-cost.md)。
- [組織規模での Azure Machine Learning の予算、コスト、クォータを管理する](/azure/cloud-adoption-framework/ready/azure-best-practices/optimize-ai-machine-learning-cost)
- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。