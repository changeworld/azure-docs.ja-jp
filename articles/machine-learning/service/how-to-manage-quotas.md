---
title: Azure Machine Learning サービスのクォータを管理および要求する方法
description: この攻略ガイドでは、Azure Machine Learning のリソースにおけるさまざまなクォータと、さらに多くのクォータを表示および要求する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 9/24/2018
ms.openlocfilehash: 0ee0a29be6a313e09cd77051dc29bc0131b57a40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997813"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Azure リソースのクォータの管理と要求

他の Azure サービスと同様に、Azure Machine Learning サービスに関連付けられている特定のリソースにも制限があります。 これらの制限は、作成できるワークスペースの数の上限から、モデルのトレーニングや推論に使用される実際の基盤となるコンピューティングに対する制限まで、多岐にわたります。 この記事では、サブスクリプションのさまざまな Azure リソースに対して事前に構成された制限に関する詳細について説明します。また、リソースの種類ごとにクォータの拡張を要求する便利なリンクも含まれています。

Azure ML リソースの設計やスケールアップを行う際は、これらのクォータに留意してください。 たとえば、クラスターのノード数がターゲットとして指定した数に満たない場合は、サブスクリプションの Batch AI コアの制限に達している可能性があります。 Batch AI で実稼働ワークロードを実行する予定がある場合は、1 つまたは複数のクォータについて既定値から増やすことが必要になる場合があります。 制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、オンライン カスタマー サポートに申請 (無料) してください。 次の表に示されている上限の値を超える制限の引き上げはできません。 上限列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。 

## <a name="special-considerations"></a>特別な考慮事項

+ クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。

+ クォータは、Batch AI を除くサブスクリプション内のすべてのサービスで共有され、Azure Machine Learning もそのうちの 1 つです。 容量のニーズを評価する際には、すべてのサービスでのクォータ使用率を計算してください。

+ 既定の制限は、プラン カテゴリの種類 (無料試用版や従量課金制など) とシリーズ (Dv2、F、G など) によって異なります。

## <a name="default-resource-quotas"></a>既定のリソース クォータ

ここでは、Azure サブスクリプション内のさまざまなリソースの種類ごとのクォータ制限の内訳を示します。 

> [!Important]
> 制限は変更されることがあります。 最新の制限は、すべての Azure のサービス レベルのクォータ [ドキュメント](https://docs.microsoft.com/azure/azure-subscription-service-limits/)で見つかります。

### <a name="virtual-machines"></a>仮想マシン 
サービス全体で、またはスタンドアロンの方法で、Azure サブスクリプションでプロビジョニングできる仮想マシンの数には制限があります。 この制限はリージョン レベルで、コアの合計数とファミリ ベースの両方に適用されます。

仮想マシンのコアには、リージョンの合計の制限だけでなく、別に適用されるリージョンのサイズ シリーズ (Dv2、F など) ごとの制限もあることに注意してください。 たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションでは、30 の A1 VM、30 の D1 VM、または合計コア数が 30 を超えないこの 2 つの組み合わせ (例: 10 の A1 VM と 20 の D1 VM) のデプロイが許可されます。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

クォータ制限の詳細および最新のリストについては、[こちら](https://docs.microsoft.com/azure/azure-subscription-service-limits#subscription-limits-1)の Azure 全体のクォータの記事を確認してください。

### <a name="batch-ai-clusters"></a>Batch AI クラスター
Batch AI では、サブスクリプションのリージョンごとに許可されるコアの数とクラスターの数の両方に対して、既定のクォータ制限があります。 Batch AI のクォータは上記の VM コア クォータとは別で、コアの制限は 2 つのリソースの種類間で現在共有されていません。

使用可能なリソース:
+ リージョンあたりの専用コアの既定の制限は、10 - 24 です。  Batch AI サブスクリプションあたりの専用コアの数は増やすことができます。 このオプション値を増やす場合は、Azure サポートにお問い合わせください。

+ リージョンあたりの優先順位の低いコアの既定の制限は、10 - 24 です。  Batch AI サブスクリプションあたりの優先順位の低いコアの数は増やすことができます。 このオプション値を増やす場合は、Azure サポートにお問い合わせください。

+ リージョンごとのクラスターの既定の制限は 20 で、上限は 200 です。 この制限を超えた引き上げを要求する場合は、Azure サポートにお問い合わせください。

クォータ制限の詳細および最新のリストについては、[こちら](https://docs.microsoft.com/azure/azure-subscription-service-limits#batch-ai-limits)の Azure 全体のクォータの記事を確認してください。

### <a name="container-instances"></a>コンテナー インスタンス

特定の期間内 (時間で範囲指定) またはサブスクリプション全体で起動できるコンテナー インスタンスの数にも制限があります。

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

クォータ制限の詳細および最新のリストについては、[こちら](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits)の Azure 全体のクォータの記事を確認してください。

### <a name="storage"></a>Storage
リージョンあたりおよび特定のサブスクリプションでもストレージ アカウントの数に制限があります。 既定の制限は 200 で、Standard Storage アカウントと Premium Storage アカウントの両方が含まれます。 指定したリージョンで 200 個を超えるストレージ アカウントが必要な場合は、[Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)からリクエストを送信してください。 Azure Storage チームがビジネス ケースを確認します。承認された場合、指定したリージョンに対して最大 250 個のストレージ アカウントが与えられます。


## <a name="find-your-quotas"></a>クォータの検索

Azure portal を使用すると、仮想マシン、ストレージ、ネットワークなどのさまざまなリソースのクォータを簡単に表示できます。

1. 左側のウィンドウで、**[すべてのサービス]** を選択し、一般カテゴリの下で **[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、検索するクォータのサブスクリプションを選択します。

   Batch AI クォータの表示については、**1 つ注意点があります**。 前述のように、サブスクリプションではクォータはコンピューティング クォータとは切り離されています。 
   Batch AI の場合、**[すべてのサービス]** を選択してから、Batch AI を検索してサービスを開きます。

1. 現在のクォータ制限と使用状況を表示するには、**[設定]** の下で **[使用量 + クォータ]** を選択します。

1. クォータ制限を表示するサブスクリプションを選択します。 興味のあるサービスとリージョンをフィルター処理します。


## <a name="request-quota-increases"></a>クォータの増加を要求

制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、[オンライン カスタマー サポートに申請](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) (無料) してください。 

表に示されている上限値を超える制限の引き上げはできません。 上限が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。 クォータの増加プロセスについては、[こちらの](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)記事で詳しく説明されています。

クォータの増加を要求する場合は、クォータの増加を要求するサービスを選択する必要があります。これには、AzureML クォータ、Batch AI のクォータ、またはストレージ クォータなどのサービスを選択できます。 

> [!NOTE]
> [無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳細については、「[Azure 無料試用版を従量課金制にアップグレード](../../billing/billing-upgrade-azure-subscription.md)」と「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq)」を参照してください。
