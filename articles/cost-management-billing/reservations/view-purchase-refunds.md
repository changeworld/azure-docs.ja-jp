---
title: Azure Reservation の購入と払戻のトランザクションを表示する
description: Azure Reservation の購入と払戻のトランザクションを表示する方法について学習します。
author: bandersmsft
ms.reviwer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/07/2021
ms.author: banders
ms.openlocfilehash: a821ef123e8690bffc9645a23175618928e55d19
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425692"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>予約購入と払戻のトランザクションの表示

予約購入と払戻のトランザクションを表示するには、いくつかの方法があります。 Azure portal、Power BI、REST API を使用できます。 交換された予約を、トランザクションでの払戻と購入として表示できます。

## <a name="view-reservation-purchases-in-the-azure-portal"></a>Azure portal で予約の購入を表示する

Enterprise Agreement と Microsoft 顧客契約の請求閲覧者は、予約の購入の累積額を [コスト分析] で表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[コストの管理と請求]** に移動します。
1. 左側のメニューで [コスト分析] を選択します。
1.  **[価格モデル]**   のフィルターを適用し、 **[予約]** を選択します。
1. 予約の購入を表示するには、 **[料金の種類]**   のフィルターを適用し、 **[購入]** を選択します。
1.  **[細分性]**   を  **[月単位]** に設定します。
1. グラフの種類を  **[列 (積み上げ)]** に設定します。

:::image type="content" source="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" alt-text="[コスト分析] における予約の購入を示すスクリーンショット。" lightbox="./media/view-purchase-refunds/reservation-purchase-cost-analysis.png" :::

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Azure portal で予約トランザクションを表示する

エンタープライズ登録または Microsoft 顧客契約の課金管理者は、Cost Management と課金で予約トランザクションを表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。
1. **[予約トランザクション]** を選択します。
1. 結果をフィルター処理するには、 **[期間]** 、 **[種類]** または **[説明]** を選択します。
1. **[適用]** を選択します。

[![Azure portal の予約トランザクションを示すスクリーンショット。](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Power BI で予約トランザクションを表示する

マイクロソフト エンタープライズ加入契約管理者は、Cost Management Power BI アプリで予約トランザクションを表示できます。

1. [Cost Management Power BI アプリ](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)を取得します。
1. RI の購入レポートに移動します。

[![予約トランザクションを示す例。](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

詳細については、[Enterprise Agreement 用の Cost Management Power BI アプリ](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md)に関するページを参照してください。

## <a name="use-apis-to-get-reservation-transactions"></a>API を使用して予約トランザクションを取得する

マイクロソフト エンタープライズ契約 (EA) と Microsoft 顧客契約のユーザーは、[予約トランザクション - List API](/rest/api/consumption/reservationtransactions/list) を使用して、予約トランザクション データを取得できます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

- 予約を管理する方法については、「[Azure の予約の管理](manage-reserved-vm-instance.md)」をご覧ください。
- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](save-compute-costs-reservations.md)
  - [Azure での予約の管理](manage-reserved-vm-instance.md)
