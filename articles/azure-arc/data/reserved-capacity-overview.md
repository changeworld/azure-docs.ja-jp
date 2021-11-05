---
title: 予約容量を使用してコストを節約する
description: Azure Arc 対応 SQL Managed Instance の予約容量を購入してコストを削減する方法について説明します。
services: sql-database
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mikeray
ms.date: 10/27/2021
ms.openlocfilehash: 70a6f67fb5a7443e84c937c9a5bf56db5980022e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090061"
---
# <a name="reserved-capacity---azure-arc-enabled-sql-managed-instance"></a>予約容量 - Azure Arc 対応 SQL Managed Instance

Azure Arc 対応 SQL Managed Instance で、Azure Arc サービスの予約をコミットすることで、従量課金制の価格と比較してコストを削減できます。 予約容量を使用して、Azure Arc 対応 SQL Managed Instance の 1 年間または 3 年間の使用を確約することで、サービス料金の大幅な割引を受けることができます。 予約容量を購入するには、Azure リージョン、デプロイの種類、パフォーマンス レベル、および期間を指定する必要があります。

特定のデータベースまたはマネージド インスタンスに予約を割り当てる必要はありません。 一致する既に実行されている既存のデプロイや、新たにデプロイされたデプロイに対して、特典が自動的に適応されます。 予約を購入することで、1 年間または 3 年間の Azure Arc サービスの使用を確約したことになります。 予約を購入するとすぐに、予約の属性に一致するサービス料金は従量課金制で課金されなくなります。 

予約は Azure Arc のサービス コストにのみ適用され、SQL IP コストやその他の料金は含まれません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金がマネージド インスタンスに適用されます。 予約は自動更新されません。 価格の詳細については、[予約容量オファー](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。

予約容量は [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/reservationsBrowse) で購入できます。 予約の支払いは、[前払いまたは月払い](../../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。 予約容量を購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。 予約容量。

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)」をご覧ください。

## <a name="determine-correct-size-before-purchase"></a>購入前に正しいサイズを決定する

予約のサイズは、特定のリージョンの予約スコープ内で、既存のまたはすぐにデプロイされる予定のマネージド インスタンスによって使用される仮想コアで測定されたコンピューティング リソースの総量に基づく必要があります。

次の一覧は、リソースを予約する方法を計画するシナリオを示しています。 

* **現時点**: 
  - 仮想コアが 16 個の汎用マネージド インスタンス 1 つ
  - 仮想コアが 8 個のビジネス クリティカルなマネージド インスタンス 2 つ

* **次の年に以下を追加**: 
  - 仮想コアが 16 個の汎用マネージド インスタンスをもう 1 つ
  - 仮想コアが 32 個のビジネス クリティカルなマネージド インスタンスをもう 1 つ

* **以下の予約を購入**:
  - 汎用マネージド インスタンスのための 32 個 (2x16) の仮想コアの 1 年間の予約
  - ビジネス クリティカルなマネージド インスタンスのための 48 個 (2x8 + 32) の仮想コアの 1 年間の予約 

## <a name="buy-reserved-capacity"></a>予約容量をご購入ください

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. **[追加]** を選択し、 **[購入予約]** ウィンドウで **[SQL Managed Instance]** を選択して、Azure Arc 対応 SQL Managed Instance の新しい予約を購入します。
4. 必須フィールドに必要事項を入力します。 選択した属性と一致する既存の SQL Managed Instance リソースが、予約容量の割引を受けられます。 割引を受けるデータベースまたはマネージド インスタンスの実際の数は、選択したスコープと数量によって変わります。

    次の表で、必須フィールドについて説明します。
    
    | フィールド      | 説明|
    |------------|--------------|
    |サブスクリプション|容量予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、Enterprise Agreement (オファー番号: MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金 (オファー番号: MS-AZR-0003P または MS-AZR-0023P) の個別の契約である必要があります。 Enterprise サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|
    |Scope       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を仮想コアの予約のスコープにすることができます。 以下を選択した場合 <br/><br/>**共有** - 仮想コアの予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されているデータベースまたはマネージド インスタンスに適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。<br/><br/>**単一サブスクリプション** - 仮想コアの予約割引は、このサブスクリプションのデータベースまたはマネージド インスタンスに適用されます。 <br/><br/>**単一のリソース グループ** - 予約割引は、選択したサブスクリプション内のマネージド インスタンスおよびそのサブスクリプション内の選択されたリソース グループに適用されます。</br></br>**管理グループ** - 管理グループと課金スコープの両方の一部であるサブスクリプションの一覧のマネージド インスタンスに、予約割引が適用されます。|
    |リージョン      |容量予約の対象となる Azure リージョン。|
    |展開の種類|予約を購入する SQL リソースの種類。|
    |パフォーマンス レベル|データベースまたはマネージド インスタンスのサービス レベル。 |
    |期間        |1 年間または 3 年間。|
    |Quantity    |予約容量の予約内で購入されるコンピューティング リソース数。 この数量は、予約し、請求時に割り引きを受ける、選択された Azure リージョンとパフォーマンス レベルに含まれる仮想コアの数です。 たとえば、米国東部リージョンで、合計コンピューティング容量を Gen5 仮想コア 16 個とする複数のマネージド インスタンスを実行する場合、あるいは実行する予定の場合、すべてのデータベースを最大限に活用するため、数量に 16 を指定します。 |

1. 予約容量の予約コストについては、「**コスト**」セクションを参照してください。
1. **[購入]** を選択します。
1. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="vcore-size-flexibility"></a>仮想コアのサイズの柔軟性

同じパフォーマンス レベルとリージョン内であれば、予約容量のベネフィットを失うことなく、仮想コアのサイズを柔軟にスケールアップまたはスケールダウンできます。 予約に未適用バッファーを維持することにより、予算を超えることなく、パフォーマンスの急上昇を効果的に管理できます。

## <a name="limitation"></a>制限事項

予約容量の価格がサポートされるのは、一般公開の状態にある機能と製品についてのみです。 

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

仮想コアの予約割引は、容量予約の範囲と属性に一致するマネージド インスタンスの数に自動的に適用されます。 容量予約のスコープは、[Azure portal](https://portal.azure.com)、PowerShell、Azure CLI、または API で更新できます。

Azure Arc 対応 SQL Managed Instance のサービス レベルについては、「[Azure Arc 対応 SQL Managed Instance のサービス レベル](service-tiers.md)」を参照してください。

- 仮想コア モデルの Azure SQL Managed Instance サービス レベルの詳細については、「[Azure SQL Managed Instance - 仮想コア サービス レベルのコンピューティング ハードウェア](../../azure-sql/managed-instance/service-tiers-managed-instance-vcore.md)」 を参照してください

容量予約を管理する方法については、[予約容量の管理](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)に関するページを参照してください。

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure の予約の管理](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Azure の予約割引を理解する](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)
