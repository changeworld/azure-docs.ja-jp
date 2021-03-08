---
title: Azure Reservation コストのチャージ バック
description: Azure Reservation コストのチャージバックを表示する方法について学習します。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/10/2021
ms.author: banders
ms.openlocfilehash: 4fb15a7e677d566454d5d487c1cf69767d7f3a30
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368746"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure Reservation コストのチャージ バック

マイクロソフト エンタープライズ契約および Microsoft 顧客契約の課金閲覧者は、予約の分散コスト データを表示できます。 コスト データを使用して、サブスクリプション、リソース グループ、リソース、またはタグの金額をパートナーにチャージ バックできます。 償却されたデータにおいて実行価格は、比例配分された時間単位の予約コストです。 コストは、その日のリソースごとの予約の使用状況における合計コストです。

個々のサブスクリプションを持つユーザーは、使用状況ファイルから分散コスト データを取得できます。 リソースの予約割引を受けると、使用状況ファイルの *AdditionalInfo* セクションに予約の詳細が含まれます。 詳細については、[Azure portal からの利用状況のダウンロード](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv)に関するページを参照してください。

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>ショー バックとチャージ バックに使用する予約の利用状況データを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[コストの管理と請求]** に移動します。 
3. 左側のナビゲーションから **[コスト分析]** を選択します。 
4. **[実際のコスト]** で、 **[分散コスト]** メトリックを選択します。
5. 予約によって使用されたリソースを確認するには、 **[予約]** にフィルターを適用し、予約を選択します。
6. **[細分性]** を **[Monthly]\(毎月\)** または **[Daily]\(毎日\)** に設定します。
7. グラフの種類を **[テーブル]** に設定します。
8. **[リソース]** に **[グループ別]** オプションを設定します。

[![チャージバックに使用できる予約リソース コストを示す例](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Azure portal で予約使用率のコストを表示する方法を示すビデオを以下に示します。

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>ショー バックとチャージ バックに使用するデータを取得する
1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[コストの管理と請求]** に移動します。 
3. 左側のナビゲーションの **[エクスポート]** を選択します。 
4. **[追加]** ボタンをクリックします。
5. [Amortized cost as the metric]\(メトリックとしての償却コスト\) ボタンを選択し、エクスポートを設定します。

予約割引を受ける使用量の EffectivePrice は、予約の日割り計算です (ゼロではありません)。 これにより、サブスクリプション、リソース グループ、またはリソースによる予約使用量の金額を把握しやすくなり、内部での予約の使用率に応じたチャージバックに役立ちます。 このデータセットには未使用の予約時間も含まれます。 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API を使用して Azure の従量課金と予約の使用状況データを取得する

データは API を使用して取得することも、Azure portal からダウンロードすることもできます。

新しいデータを取得するには、[使用状況の詳細 API](/rest/api/consumption/usagedetails/list) を呼び出します。 用語に関する詳細については、[利用料金の用語](../understand/understand-usage.md)のページを参照してください。

Usage Details API への呼び出しの例を次に示します。

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

{billingPeriodId} と {enrollmentId} の詳細については、「[Usage Details – List](/rest/api/consumption/usagedetails/list)」 (使用方法の詳細 – 一覧) の API 記事を参照してください。

メトリックとフィルターに関する次の表の情報は、予約の一般的な問題の解決に役立ちます。

| **API データの種類** | API 呼び出しアクション |
| --- | --- |
| **すべての料金 (使用状況と購入)** | {metric} を ActualCost で置き換える |
| **予約割引を取得する使用状況** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/reservationId%20ne%20 で置き換える |
| **予約割引を取得しなかった使用状況** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/reservationId%20eq%20 で置き換える |
| **請求済み料金 (使用状況と購入)** | {metric} を AmortizedCost で置き換える |
| **未使用の予約レポート** | {metric} を AmortizedCost で置き換える<br><br>{filter} を properties/ChargeType%20eq%20'UnusedReservation' で置き換える |
| **予約購入** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/ChargeType%20eq%20'Purchase' で置き換える  |
| **返金** | {metric} を ActualCost で置き換える<br><br>{filter} を properties/ChargeType%20eq%20'Refund' で置き換える |

## <a name="download-the-usage-csv-file-with-new-data"></a>新しいデータを含む使用状況の CSV ファイルをダウンロードする

EA 管理者の場合、Azure portal から新しい使用状況データを含む CSV ファイルをダウンロードできます。 このデータは、EA ポータル (ea.azure.com) では確認できません。新しいデータを確認するには、Azure portal (portal.azure.com) から使用状況ファイルをダウンロードする必要があります。

Azure portal で [[コストの管理と請求]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts) に移動します。

1. 請求先アカウントを選択します。
2. **[使用量 + 請求金額]** をクリックします。
3. **[Download]** をクリックします。  
![Azure portal で使用状況データの CSV ファイルをダウンロードする場所を示す例](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **[使用量の詳細]** の **[Amortized usage data]\(償却された利用状況データ\)** を選択します。

ダウンロードする CSV ファイルには、実際のコストと分散コストが含まれます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ
- Azure の予約の利用状況データについて詳しくは、次の記事を参照してください。
  - [マイクロソフト エンタープライズ契約と Microsoft 顧客契約の予約コストと使用状況を把握する](understand-reserved-instance-usage-ea.md)
 
