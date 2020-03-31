---
title: Azure Data Explorer 割増を前払いしてコストを削減する
description: Azure Data Explorer の予約容量を購入して、Azure Data Explorer のコストを節約する方法について説明します。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969277"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Azure Data Explorer 予約容量を使用して Azure Data Explorer 割増ユニットを前払いする

Azure Data Explorer では、割増ユニットを前払いすると、従量課金制の料金よりもコストを節約できます。 Azure Data Explorer の予約容量を使用して、Azure Data Explorer を 1 年間または 3 年間使用することを確約すると、Azure Data Explorer の割増コストが大幅に割引されます。 Azure Data Explorer の予約容量を購入するために必要なことは、期間の指定のみです。これはすべてのリージョンの Azure Data Explorer のすべてのデプロイに適用されます。

予約を購入すると、割増コストを 1 年間または 3 年間分前払いすることになります。 予約を購入するとすぐに、予約の属性に一致する Azure Data Explorer 割増料金は従量課金制で課金されなくなります。 既に実行されている、または新しくデプロイされた Azure Data Explorer クラスターには、この特典が自動的に適用されます。 クラスターに関連付けられているコンピューティング、ネットワーク、またはストレージの料金は、この予約の対象になりません。 これらのリソースの予約容量は、別途購入する必要があります。 予約期間が終了すると、課金特典の有効期限が切れ、Azure Data Explorer 割増ユニットは従量課金制の料金で課金されます。 予約は自動更新されません。 価格情報については、[Azure Data Explorer の価格に関するページ](https://azure.microsoft.com/pricing/details/data-explorer/)を参照してください。

Azure Data Explorer の予約容量は [Azure portal](https://portal.azure.com) で購入できます。 Azure Data Explorer の予約容量を購入するには:

* 少なくとも 1 つのエンタープライズまたは従量課金制のサブスクリプションの所有者である必要があります。
* Enterprise サブスクリプションの場合、**EA ポータル**で [[予約インスタンスを追加します]](https://ea.azure.com) を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
* クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Data Explorer の予約容量を購入できます。

企業のお客様と従量課金制のお客様が予約を購入した場合の課金方法については、以下を参照してください。
* [エンタープライズ加入契約に適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-markup-usage-before-purchase"></a>購入前に適切に割増の使用量を判断する

予約のサイズは、既存の、またはすぐにデプロイする予定の Azure Data Explorer クラスターに使用する Azure Data Explorer 割増ユニットの総数に基づく必要があります。 割増ユニットの数は、運用環境の Azure Data Explorer エンジン クラスター コア数と同じです (開発/テスト SKU は含みません)。 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Azure Data Explorer の予約容量を購入する

1. [Azure portal](https://portal.azure.com) にサインインする
1. **[すべてのサービス]**  >  **[予約]**  >  **[今すぐ購入]** を選択します。 **[追加]** を選択します。
1. **[製品の種類を選択してください]** ウィンドウで **[Azure Data Explorer]** を選択し、Azure Data Explorer 割増ユニットの新しい予約を購入します。 
1. **[購入]** を選択します
1. 必須フィールドに必要事項を入力します。 

    ![[購入] ページ](media/pricing-reserved-capacity/purchase-page.png)

1. **[コスト]** セクションで Azure Data Explorer 割増の予約容量の予約コストを確認します。
1. **[購入]** を選択します。
1. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

Azure Data Explorer の予約容量の予約を取り消す必要がある場合は、12% の中途解約料がかかる可能性があります。 払い戻し額は、購入価格または予約の現在の価格のいずれかの最低価格に基づいています。 払い戻し額の上限は年額 50,000 ドルです。 日割り計算によって算出された金額から 12% の中途解約料を差し引いた金額が払い戻されます。 キャンセルを要求するには、Azure portal で予約に移動し、 **[返金]** を選択して、サポート要求を作成します。

Azure Data Explorer の予約容量の予約を別の期間に変更する必要がある場合は、値が等しいかそれ以上の別の予約に交換できます。 新しい予約期間の開始日は、元の予約からは引き継がれません。 1 年または 3 年の予約期間は、新しい予約を作成した時点から始まります。 交換を要求するには、Azure portal で予約に移動し、 **[交換]** を選択して、サポート要求を作成します。

予約を交換または返金する方法の詳細については、[予約の交換と返金](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)に関するページを参照してください。

## <a name="manage-your-reserved-capacity-reservation"></a>予約容量の予約を管理する

Azure Data Explorer 割増ユニットの予約割引は、Azure Data Explorer の予約容量の予約範囲と属性と一致する割増ユニット数に自動的に適用されます。 


> [!NOTE]
> * [Azure portal](https://portal.azure.com)、PowerShell、CLI、または API を使用して、Azure Data Explorer の予約容量の予約の範囲を更新できます。
> * Azure Data Explorer の予約容量の予約を管理する方法については、[Azure Data Explorer の予約容量の管理](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

* [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure の予約の管理](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure の予約割引を理解する](../cost-management-billing/reservations/understand-reservation-charges.md)
* [従量課金制サブスクリプションの予約使用量について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [エンタープライズ加入契約の予約使用量について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。
