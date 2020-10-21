---
title: リソースとクォータの管理
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のリソースにおけるクォータと、さらに多くのクォータを要求する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 10/13/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4, contperfq2
ms.openlocfilehash: a81af14992c8557c245ab3a1073f031a6c505084
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019394"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Azure Machine Learning を使用してリソースのクォータを管理し増やす

Azure では、不正による予算超過を防ぎ、Azure の容量の制約を尊重するために制限とクォータを使用しています。 運用環境のワークロードに合わせてスケーリングするときは、これらの制限事項について考慮してください。 この記事では、次の内容について説明します。

> [!div class="checklist"]
> + [Azure Machine Learning](overview-what-is-azure-ml.md) に関連する Azure リソースの既定の制限事項。
> + クォータと制限事項を表示する。
> + ワークスペース レベルのクォータを作成する。
> + クォータの引き上げを依頼する。
> + プライベート エンドポイントと DNS クォータ。

クォータの管理に加えて、[Azure Machine Learning のコストを計画して管理する](concept-plan-manage-cost.md)方法についても説明します。

## <a name="special-considerations"></a>特別な考慮事項

+ クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、[Azure サポートに連絡してクォータを引き上げてください](#request-quota-increases)。

+ クォータは、Azure Machine Learning を含め、サブスクリプション内のすべてのサービスで共有されます。 容量を評価するときは、すべてのサービスの使用量を計算します。
    + Azure Machine Learning コンピューティングは例外であり、コア コンピューティング クォータから切り離されたクォータがあります。 

+ 既定の制限は、プラン カテゴリの種類 (無料試用版や従量課金制など) と仮想マシン (VM) シリーズ (Dv2、F、G など) によって異なります。

## <a name="default-resource-quotas"></a>既定のリソース クォータ

このセクションでは、次のリソースの既定値とクォータの上限について説明します。

+ 仮想マシン
+ Azure Machine Learning コンピューティング
+ Azure Machine Learning パイプライン
+ Container Instances
+ 記憶域

> [!IMPORTANT]
> 制限は変更されることがあります。 最新の制限は、すべての Azure のサービス レベルのクォータ [ドキュメント](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)で見つかります。

### <a name="virtual-machines"></a>仮想マシン
各 Azure サブスクリプションには、すべてのサービスにわたる仮想マシン数の制限があります。 仮想マシン コアには、リージョンの合計の制限とサイズシリーズ (Dv2、F など) あたりのリージョンの制限があります。 どちらの制限も個別に適用されます。

たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションを使用すると、30 個の A1 VM、30 個の D1 VM、または合計 30 コアを超えないこれら 2 つの組み合わせをデプロイできます。

仮想マシンの制限は、次の表に示す値を超えて引き上げることはできません。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning コンピューティング
[Azure Machine Learning コンピューティング](concept-compute-target.md#azure-machine-learning-compute-managed)には、サブスクリプションのリージョンごとに許可されるコアの数と一意のコンピューティング リソースの数の両方に対して、既定のクォータ制限があります。 このクォータは、前のセクションの VM コア クォータとは別のものです。

[クォータの引き上げを依頼](#request-quota-increases)して、このセクションの制限を表に示されている「**上限**」まで引き上げます。

使用可能なリソース:
+ **リージョンあたりの専用コア**には、サブスクリプション プランの種類に応じて、24 から 300 の既定の制限があります。  サブスクリプションあたりの専用コアの数は VM ファミリごとに増やすことができます。 NCv2、NCv3、ND シリーズなど、特殊な VM ファミリは、ゼロ コアの既定から開始されます。

+ **リージョンあたりの低優先度のコア**には、サブスクリプション プランの種類に応じて、100 から 3000 の既定の制限があります。 サブスクリプションあたりの優先順位の低いコアの数は増やすことができ、VM ファミリ全体で 1 つの値になります。

+ **リージョンあたりのクラスター**の既定の制限は 200 です。 これらは、トレーニング クラスターとコンピューティング インスタンス (クォータ目的で単一ノード クラスターとみなされる) 間で共有されます。

次の表は、超過できない追加の制限を示しています。

| **リソース** | **上限** |
| --- | --- |
| リソース グループあたりのワークスペース数 | 800 |
| 1 つの Azure Machine Learning コンピューティング (AmlCompute) リソース内のノード数 | 100 ノード |
| ノードあたりの GPU MPI プロセス数 | 1-4 |
| ノードあたりの GPU worker 数 | 1-4 |
| ジョブの有効期間 | 21 日<sup>1</sup> |
| 優先順位の低いノードでのジョブの有効期間 | 7 日<sup>2</sup> |
| ノードあたりのパラメーター サーバー数 | 1 |

<sup>1</sup> 最大有効期間とは、実行が開始されてから終了するまでの期間を指します。 完了した実行は無期限に保持されます。 最大有効期間内に完了しなかった実行のデータにはアクセスできません。
<sup>2</sup> 優先順位の低いノード上のジョブは、容量の制約があるときはいつでも横取りできます。 ジョブにチェックポイントを実装することをお勧めします。

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning パイプライン
[Azure Machine Learning パイプライン](concept-ml-pipelines.md)には次の制限事項があります。

| **リソース** | **制限** |
| --- | --- |
| パイプラインのステップ | 30,000 |
| リソース グループあたりのワークスペース数 | 800 |

### <a name="container-instances"></a>コンテナー インスタンス

詳細については、「[Container Instances の制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)」を参照してください。

### <a name="storage"></a>記憶域
Azure Storage アカウントには、リージョンあたり、サブスクリプションあたりのストレージ アカウント数が 250 個という制限があります。 これには、Standard および Premium Storage アカウントの両方が含まれます。

上限を引き上げるには [Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)を通じて依頼してください。 Azure Storage チームがお客様のケースを確認します。1 つのリージョンに対して最大 250 個のストレージ アカウントを承認する場合があります。


## <a name="workspace-level-quota"></a>ワークスペース レベル クォータ

同じサブスクリプション内の複数の[ワークスペース](concept-workspace.md)間の Azure Machine Learning コンピューティング ターゲットの割り当てを管理するには、ワークスペース レベル クォータを使用します。

既定では、すべてのワークスペースが VM ファミリのサブスクリプション レベル クォータと同じクォータを共有しています。 ただし、サブスクリプション内のワークスペース上の個々の VM ファミリに最大クォータを設定できます。 これにより、容量を共有し、リソース競合の問題を回避できます。

1. サブスクリプション内の任意のワークスペースに移動します。
1. 左側のペインで、 **[使用量 + クォータ]** を選択します。
1. **[クォータの構成]** タブを選択して、クォータを表示します。
1. VM ファミリを展開します。
1. その VM ファミリの下に表示されているワークスペースにクォータ制限を設定します。

負の値またはサブスクリプション レベルのクォータよりも大きい値は設定できません。

[![Azure Machine Learning ワークスペース レベルのクォータ](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> ワークスペース レベルでクォータを設定するには、サブスクリプション レベルのアクセス許可が必要です。

## <a name="view-your-usage-and-quotas"></a>使用量とクォータの表示

仮想マシン、ストレージ、ネットワークなどのさまざまな Azure リソースのクォータを表示するには、Azure portal を使用します。

1. 左側のウィンドウで、 **[すべてのサービス]** を選択し、一般カテゴリの下で **[サブスクリプション]** を選択します。

2. サブスクリプションの一覧から、検索するクォータのサブスクリプションを選択します。

3. 現在のクォータ制限と使用状況を表示するには、 **[使用量 + クォータ]** を選択します。 フィルターを使用して、プロバイダーと場所を選択します。 

サブスクリプションの Azure Machine Learning コンピューティング クォータは、他の Azure のクォータとは別に管理されます。 

1. **Azure portal** で Azure Machine Learning ワークスペースに移動します。

2. 左側のペインにある **[サポート + トラブルシューティング] セクション**で **[使用量 + クォータ]** を選択して、現在のクォータ制限と使用状況を表示します。

3. クォータ制限を表示するサブスクリプションを選択します。 必ずフィルター処理を行い、対象とするリージョンに絞ります。

4. サブスクリプション レベル ビューとワークスペース レベル ビューを切り替えることができます。

## <a name="request-quota-increases"></a>クォータの増加を要求

制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、[オンライン カスタマー サポートに申請](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) (無料) してください。

上の表に示されている上限値を超える制限の引き上げはできません。 上限がない場合、リソースの制限を調整することはできません。

クォータの引き上げを依頼する場合は、クォータの引き上げを依頼するサービスを選択します。 たとえば Azure Machine Learning、Container Instances、Storage などです。Azure Machine Learning コンピューティングの場合、上記の手順に従ってクォータを表示している間に、 **[クォータの要求]** ボタンを選択する方法もあります。

> [!NOTE]
> [無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳細については、「[Azure 無料試用版を従量課金制にアップグレード](../billing/billing-upgrade-azure-subscription.md)」と「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq)」を参照してください。

## <a name="private-endpoint-and-private-dns-quota-increases"></a>プライベート エンドポイントとプライベート DNS クォータの引き上げ

1 つのサブスクリプションで作成できるプライベート エンドポイントとプライベート DNS ゾーンの数には制限があります。

Azure Machine Learning の場合、(お客様の) サブスクリプションでリソースが作成されますが、Microsoft が所有するサブスクリプションでリソースが作成されるシナリオがいくつかあります。

 次のシナリオでは、場合によっては Microsoft が所有するサブスクリプションでクォータの割り当てを依頼する必要があります。

* __カスタマーマネージド キー (CMK) を使用する Private Link 対応ワークスペース__
* __仮想ネットワークの背後にあるワークスペースの Azure Container Registry__
* __Private Link 対応の Azure Kubernetes Service クラスターのワークスペースへのアタッチ__。

このようなシナリオの許容を依頼するには、次の手順を実行します。

1. [Azure サポート リクエストを作成し](/azure/azure-portal/supportability/how-to-create-azure-support-request#create-a-support-request)、 __[基本]__ セクションから次のオプションを選択します。

    | フィールド | 選択 |
    | ----- | ----- |
    | 問題の種類 | 技術面 |
    | サービス | 使用中のサービス。 ドロップダウン リストから __[Machine Learning]__ を選択します。 |
    | 問題の種類 | ワークスペースのセットアップ、SDK、CLI |
    | 問題のサブタイプ | ワークスペースのプロビジョニングまたは管理に関する問題 |

2. __[詳細]__ セクションから、 __[説明]__ フィールドを使用して、使用する Azure リージョンと使用する予定のシナリオを指定します。 複数のサブスクリプションについてクォータの引き上げを依頼する必要がある場合は、このフィールドにサブスクリプション ID も列挙します。

3. __[作成]__ を選択してリクエストを作成します。

## <a name="next-steps"></a>次のステップ

+ [Azure Machine Learning のコストを計画して管理する](concept-plan-manage-cost.md)
