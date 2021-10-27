---
title: 事前購入プランを使用して Azure Synapse Analytics のコストを最適化する
description: Azure Synapse コミット ユニット (SCU) を 1 年間購入して、Azure Synapse Analytics のコストを節約する方法について説明します。
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/08/2021
ms.author: banders
ms.openlocfilehash: 031dc79bfabf03bfadf0c7e01171cbb72a15008e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007031"
---
# <a name="optimize-azure-synapse-analytics-costs-with-a-pre-purchase-plan"></a>事前購入プランを使用して Azure Synapse Analytics のコストを最適化する

Azure Synapse コミット ユニット (SCU) を 1 年間購入すると、Azure Synapse Analytics のコストを節約することができます。 購入済みの SCU は、購入期間中いつでも使用できます。 VM とは異なり、事前購入したユニットは 1 時間単位で期限切れにならず、購入期間中はいつでも使用できます。

Azure Synapse Analytics の使用状況はすべて、事前購入済み SCU から自動的に差し引かれます。 事前購入割引を受けるために、事前購入済みのプランを Azure Synapse Analytics ワークスペースに再デプロイしたり、割り当てたりする必要はありません。

## <a name="determine-the-right-size-to-buy"></a>購入する適切なサイズの判断

Synapse の事前購入は、Synapse のすべてのワークロードと層に適用されます。 事前購入プランは、前払いした Synapse コミット ユニットのプールと考えることができます。 ワークロードまたはレベルには関係なく、使用状況がプールから差し引かれます。 コンピューティング、ストレージ、およびネットワーキングなどの他の料金は別途課金されます。

Synapse の事前購入割引は、次の製品の使用状況に適用されます。

- Azure Synapse Analytics Dedicated SQL Pool
- Azure Synapse Analytics Managed VNET
- Azure Synapse Analytics Pipelines
- Azure Synapse Analytics Serverless SQL Pool
- Azure Synapse Analytics Serverless Apache Spark Pool - Memory Optimized
- Azure Synapse Analytics Data Flow - Basic
- Azure Synapse Analytics Data Flow - Standard

利用可能な SCU 層と価格割引の詳細については、次のセクションに示す予約購入エクスペリエンスをご利用ください。

## <a name="purchase-synapse-commit-units"></a>Synapse コミット ユニットを購入する

Synapse プランは、[Azure portal](https://portal.azure.com) で購入します。 事前購入プランを購入するには、少なくとも 1 つのエンタープライズ サブスクリプションに対して所有者ロールを所持している必要があります。

- 次に対する所有者ロールに属している必要があります: 少なくとも 1 つの Enterprise Agreement (プラン番号: MS-AZR-0017P または MS-AZR-0148P) または Microsoft Customer Agreement または従来課金制の個々のサブスクリプション (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。
- Enterprise Agreement (EA) サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。

### <a name="to-purchase"></a>購入方法:

1. [Azure ポータル](https://portal.azure.com/?synapse=true#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand/autoOpenSpecPicker//productType/Reservation)にアクセスします。
1. 必要に応じて、**[予約]** に移動し、ページの上部にある **[+ 追加]** を選択します。
1. [購入予約] ページで、**[Azure Synapse Analytics 事前購入プラン]** を選択します。
1. [購入する製品の選択] ページで、サブスクリプションを選択します。 **[サブスクリプション]** リストを使用して、予約容量の支払いに使用するサブスクリプションを選択します。 サブスクリプションの支払方法に対して、予約容量の初期コストが課金されます。 加入契約の Azure 前払い (旧称: 年額コミットメント) 残高から料金が差し引かれるか、超過料金として課金されます。
1. スコープを選択します。 **[スコープ]** リストを使用して、サブスクリプション スコープを選択します。
    - **単一のリソース グループ スコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
    - **単一サブスクリプション スコープ** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
    - **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。
    - **管理グループ** - 管理グループと課金スコープの両方の一部であるサブスクリプションの一覧にある一致するリソースに、予約割引を適用します。
1. 購入する Azure Synapse コミット ユニット数を選択して、購入を完了します。  
    :::image type="content" source="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" alt-text="Azure Synapse Analytics 事前購入プランの製品エクスペリエンスの選択を示すスクリーンショット。" lightbox="./media/synapse-analytics-pre-purchase-plan/buy-synapse-analytics-pre-purchase-plan.png" :::

## <a name="change-scope-and-ownership"></a>スコープと所有権の変更

購入後に予約に次の種類の変更を加えることができます。

- 予約スコープの更新
- Azure ロールベースのアクセス制御 (Azure RBAC)

Synapse コミット ユニットの事前購入プランを分割またはマージすることはできません。 予約の管理について詳しくは、[購入後の予約の管理](manage-reserved-vm-instance.md)に関する記事をご覧ください。

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

Synapse の事前購入プランでは、キャンセルと交換はサポートされていません。 すべての購入は確定です。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](save-compute-costs-reservations.md)
- [Azure の予約の管理](manage-reserved-vm-instance.md)
- [Azure の予約割引を理解する](understand-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)