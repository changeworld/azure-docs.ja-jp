---
title: Azure VMware Solution の予約インスタンスを使用してコストを節約する
description: Azure VMware Solution の予約インスタンスを購入する方法について説明します。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: fa354d4fbfef868ea1e6783656be7871669f200d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951419"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Azure VMware Solution でコストを節約する

Azure VMware Solution の予約インスタンスにコミットすると、コストを削減することができます。 予約割引は、予約スコープと属性に一致する実行中の Azure VMware Solution ホストの数に対して自動的に適用されます。 割引を取得するために、専用ホストに予約を割り当てる必要はありません。 予約インスタンスの購入では、使用量のコンピューティング部分のみが対象となり、ソフトウェア ライセンスのコストも含まれます。  [Azure VMware Solution の概要](introduction.md)に関する記事を参照してください。

## <a name="purchase-restriction-considerations"></a>購入の制限に関する考慮事項

予約インスタンスの利用についてはいくつかの例外があります。

-   **クラウド** - 予約は、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)」ページの一覧にあるリージョンでのみ使用できます。

-   **クォータの不足** - 単一サブスクリプションまたは共有サブスクリプションをスコープとする予約の場合、新しい予約インスタンスに割り当てることのできるホスト クォータがそのサブスクリプションに存在していることが必要です。  [クォータ引き上げ要求を作成](enable-azure-vmware-solution.md)して、この問題を解決できます。

-   **オファーの適格性** - Microsoft との  [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) が必要です。

-   **容量制限** - まれに、リージョンの容量が低下していることが原因で、Azure VMware Solution ホスト SKU に対する新しい予約の購入が、Azure によって制限されることがあります。

## <a name="buy-a-reservation"></a>予約の購入

Azure VMware Solution ホスト インスタンスの予約インスタンスは、 [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) で購入できます。

予約の支払いは、 [前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。

予約専用ホスト インスタンスの購入には、次の要件が適用されます。

-   少なくとも 1 つの EA サブスクリプションまたは従量課金制料金のサブスクリプションの所有者ロールである必要があります。

-   EA サブスクリプションの場合、 [EA ポータル](https://ea.azure.com/)で  **[予約インスタンスを追加します]**  オプションを有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。

インスタンスを購入する場合、次のことが必要です。

1.  [Azure portal](https://portal.azure.com/) にサインインします。

2.  **[すべてのサービス]**  > **[予約]** を選択します。

3.  **[追加]**  を選択して新しい予約を購入してから、 **[Azure VMware Solution]** を選択します。

4. 必須フィールドに入力します。 選択した属性と一致する Azure VMware Solution ホストを実行することで、予約割引を受けることができます。 割引を受ける Azure VMware Solution ホストの実際の数は、選択したスコープと数量によって変わります。

   EA 契約を結んでいる場合、 **[Add more option]\(さらにオプションを追加\)**  を使用してインスタンスをすばやく追加することができます。 このオプションは、他のサブスクリプションの種類では使用できません。

   | フィールド        |  説明 |
   | ------------ | ------------ |
   | サブスクリプション | 予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約のコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または Microsoft Customer Agreement または従来課金制の個々のサブスクリプション (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 年額コミットメント残高から料金が差し引かれるか(使用可能な場合)、超過料金として課金されます。 従量課金制料金のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。 |
   | Scope        | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合:<br><ul><li><b>単一のリソース グループのスコープ — 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。</li><li><b>単一サブスクリプション — 選択されたサブスクリプションの一致するリソースに予約割引を適用します。</li><li><b>共有スコープ — 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 EA の顧客の場合、課金コンテキストは課金です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。</li></ul>       |
   | リージョン       | 予約の対象となる Azure リージョン。   |
   | ホストのサイズ    | AV36    |
   | 期間         | 1 年間または 3 年間。  |
   | Quantity     | 予約内で購入しているインスタンス数。 この数量は、課金の割引を受けられる実行中の Azure VMware Solution ホストの数です。    |

## <a name="usage-data-and-reservation-utilization"></a>使用状況データと予約の使用率

使用状況データには、予約割引が適用される使用量に対する 0 の実効価格があります。 予約ごとに、予約割引を受けた Azure VMware Solution インスタンスを確認できます。

使用状況データに予約割引を表示する方法の詳細については、EA を使用している場合は、 [エンタープライズ加入契約に適用される Azure の予約の使用状況](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)に関する記事を参照してください。 個別サブスクリプションを使用している場合は、 [従量課金制サブスクリプションに適用される Azure の予約の使用状況](../cost-management-billing/reservations/understand-reserved-instance-usage.md)に関するページを参照してください。

## <a name="change-a-reservation-after-purchase"></a>購入後に予約を変更する

購入後に予約に次の種類の変更を加えることができます。

-   予約スコープの更新

-   インスタンス サイズの柔軟性 (該当する場合)

-   所有権

また、予約をより小さいチャンクに分割したり、予約を統合したりすることもできます。 いかなる変更であっても、新しい商用のトランザクションを発生させたり、予約の終了日が変更されたりすることはありません。

>[!NOTE]
>予約を購入した後は、次の種類の変更を直接行うことはできません。
>
> - 既存の予約のリージョン
> - SKU
> - Quantity
> - Duration
>
>ただし、変更を加える必要がある場合、予約を " *交換*" することができます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「 [Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。