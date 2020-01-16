---
title: 前払いまたは月払いで Azure の予約を購入する
description: 前払いまたは月払いで Azure の予約を購入する方法について説明します。
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: banders
ms.openlocfilehash: cf9bb7f3b9661d5957ad569fce7112fe16659761
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75986875"
---
# <a name="purchase-reservations-with-monthly-payments"></a>月払いで予約を購入する

従来、Azure の予約は前払いで購入する必要がありました。 今後は月払いで予約を購入することができます。 全額を支払う前払いでの購入とは異なり、月払いを選択すると、対象期間の月数で予約の総コストが均等に分割されます。 前払いも月払いも予約の総コストは同じであり、月払いを選んだとしても追加料金を支払う必要はありません。

毎月の支払金額は、現地通貨に対する当月の市場相場によって異なる場合があります。

月払いの対象となるのは次のとおりです。

- 仮想マシン
- Azure Storage
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- App Service 登録料

予約の購入は [Azure portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade) で行います。

![予約の購入を示す例](./media/monthly-payments-reservations/purchase-reservation.png)

予約の購入過程で、支払いスケジュールを確認できます。 **[全額支払スケジュールの表示]** をクリックします。

![予約の支払いスケジュールを表示する例](./media/monthly-payments-reservations/prepurchase-schedule.png)

購入後に支払いスケジュールを確認するには、予約を選択し、 **[予約注文 ID]** をクリックし、 **[支払]** タブをクリックします。

## <a name="view-payments-made"></a>過去の支払いを確認する

過去の支払いは、API や利用状況データを使用して確認できるほか、コスト分析で確認できます。 月払いで支払われた予約の場合、その頻度の値が **[定期的]** として、利用状況データと Reservation Charges API に表示されます。 前払いで支払われた予約の場合、この値が **[onetime]\(1 回\)** として表示されます。

コスト分析では、月払いの購入が既定のビューに表示されます。 すべての購入を表示するには、 **[Charge type]\(料金タイプ\)** に **[購入]** フィルターを、 **[頻度]** に **[定期的]** を適用します。 予約のみを表示するには、 **[予約]** のフィルターを適用します。

![コスト分析で予約の購入コストを表示する例](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>更新時に月払いに切り替える

予約を更新するときに、請求頻度を毎月に変更することができます。

## <a name="exchange-and-refunds"></a>交換と払戻

月払いで購入した予約は、他の予約と同様、払戻を受けたり交換したりすることができます。 現在、サポート リクエストを送信することで、月払いで購入した予約の交換または払戻を開始できます。

月払いで購入した予約を交換する場合、新たな購入対象の総生涯コストが、予約の返上で取り消される支払残金よりも大きくなければなりません。 交換に関しては、それ以外の制限や料金はありません。 前払いで購入した予約と引き換えに、新しい予約を月払いで購入することができます。 ただし、新しい予約の生涯価値は、返上する予約の実際の量に応じた価値よりも大きくなければなりません。

月払いの予約が取り消された場合、将来支払われることになっていた金額の取り消しに対して、Microsoft はキャンセル料を適用する可能性があります。 確定済み支払い残金の上限は、払戻の上限である 50,000 米国ドルとなります。

交換と払戻の詳細については、「[Azure の予約のセルフサービスによる交換と払戻](exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 予約の詳細については、「[Azure の予約とは](save-compute-costs-reservations.md)」を参照してください。
- 予約を購入する前に済ませておくべき作業については、「[購入する前に適切な VM サイズを決定する](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)」を参照してください。
