---
title: Azure の請求およびコスト管理の自動化シナリオ | Microsoft Docs
description: 一般的な請求およびコスト管理のシナリオがどのようにさまざまな API にマッピングされているかについて学習します。
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: f84071577e9636e40d621093e2c57e3e9adf4913
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247668"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>請求およびコスト管理の自動化シナリオ

請求およびコスト管理領域の一般的なシナリオが以下に識別され、これらのシナリオで使用できるさまざまな API にマップされています。 シナリオと API のマッピング以降には、使用可能なすべての API の概要と提供される機能が示されています。 

## <a name="common-scenarios"></a>一般的なシナリオ 

請求およびコスト管理 API をさまざまなシナリオで使用して、コストと使用状況に関する質問に答えることができます。  一般的なシナリオの概要を以下に示します。

- **請求書の調整** - Microsoft からの請求金額が正しいか。  請求書の内容はどうなっているか。自分で料金を計算できるか。

- **クロス請求** - 請求金額がわかったところで、組織の誰がその料金を支払う必要があるか。

- **コストの最適化** - 請求金額がわかったところで、Azure に関する支出からどのように利益を得ることができるか。

- **コスト管理** - Azure に関する支出と使用状況を時間の経過に沿って確認したい。 どのような傾向があるか。 どのようにすればよりうまくできるか。

- **Azure の月間の支出** - 当月の今日までの支出はいくらか。 Azure に関する支出と使用状況に何か調整を加える必要があるか。 Azure を最も消費しているのは月内のいつか。

- **警告の設定** - リソースベースの消費または通貨ベースの警告を設定したい。

## <a name="scenario-to-api-mappings"></a>シナリオと API のマッピング

|         API/シナリオ        | 請求書の調整    | クロス請求    | コストの最適化    | コスト管理    | 当月の今日までの支出    | アラート    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgets                     |                           |                  |           ○          |                  |                    |     ○     |
| Marketplaces                |             ○             |         ○        |           ○          |         ○        |          ○         |     ○     |
| Price Sheet                 |             ○             |         ○        |           ○          |         ○        |          ○         |           |
| Reservation Recommendations |                           |                  |           ○          |                  |                    |           |
| Reservation Details         |                           |                  |           ○          |         ○        |                    |           |
| Reservation Summaries       |                           |                  |           ○          |         ○        |                    |           |
| Usage Details               |             ○             |         ○        |           ○          |         ○        |          ○         |     ○     |
| Billing Periods             |             ○             |         ○        |           ○          |         ○        |                    |           |
| Invoices                    |             ○             |         ○        |           ○          |         ○        |                    |           |
| RateCard                    |             ○             |                  |           ○          |         ○        |          ○         |           |
| Unrated Usage               |             ○             |                  |           ○          |                  |          ○         |           |

> [!NOTE]
> 以下のシナリオと API のマッピングには、Enterprise Consumption API シリーズは含まれていません。 可能であれば、一般的な Consumption API シリーズを利用して、今後の新しい開発シナリオに対応してください。

## <a name="api-summaries"></a>API の概要

### <a name="consumption"></a>消費
("*下記を除く、すべての API の Web Direct + Enterprise のお客様*")

-   **Budgets** ("*Enterprise のお客様のみ*"): [Budgets API](https://docs.microsoft.com/rest/api/consumption/budgets) を使用して、リソース、リソース グループ、または課金測定のコストまたは使用予算を作成します。  予算を作成した後は、ユーザーが定義した予算のしきい値を超えたときに通知するよう警告を構成できます。 予算金額に達したときに実行されるアクションを構成することもできます。
-   **Marketplaces**: [Marketplace Charge API](https://docs.microsoft.com/rest/api/consumption/marketplaces) を使用して、すべての Marketplace リソース (Azure サード パーティ製ソリューション) に関する料金および使用状況データを取得します。 このデータを使用すると、すべての Marketplace リソースのコストを合算したり、特定のリソースのコスト/使用状況を調査したりすることができます。
-   **Price Sheet** ("*Enterprise のお客様のみ*"): Enterprise のお客様は、[Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) を使用して、すべての測定のカスタム価格を取得できます。 企業は、このデータを使用状況の詳細とマーケットプレースの使用状況情報と共に使用して、使用状況およびマーケットプレース データを使用してコストの計算を実行することができます。 
-   **Reservation Recommendations**: [Reservation Recommendations API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) を使用して、VM 予約済みインスタンスの購入に関する推奨事項を取得します。 推奨事項は、お客様が予想されるコスト削減と購入額を分析できるように設計されています。
-   **Reservation Details**: [Reservation Details API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) を使用して、予約された消費と実際に使用された量の比較など、以前に購入した VM 予約に関する情報を表示します。 データは、VM 単位の詳細度で表示することができます。
-   **Reservation Summaries**: [Reservation Summaries API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) を使用して、予約された消費量と実際に使用された合計の量の比較など、以前に購入した VM 予約に関する集計情報を表示します。 
-   **Usage Details**: [Usage Details API](https://docs.microsoft.com/rest/api/consumption/usagedetails) を使用して、すべての Azure ファースト パーティ リソースの料金および使用状況を取得します。 情報は、現在 1 日あたり測定あたり 1 回発行される使用状況詳細レコード形式で提供されます。 この情報を使用すると、すべてのリソースのコストを合算したり、特定のリソースのコスト/使用状況を調査したりすることができます。
-   **RateCard**: Web Direct のお客様は、[RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) を使用して測定レートを取得できます。 次に、返された情報をリソース使用状況情報と共に使用して、予想される請求書を手動で計算することができます。 
-   **Unrated Usage**: [Unrated Usage API](https://msdn.microsoft.com/library/azure/mt219003.aspx) を使用して、Azure による測定/課金の前に生の使用状況情報を取得できます。

### <a name="billing"></a>課金
-   **Billing Periods**: [Billing Periods API](https://docs.microsoft.com/rest/api/billing/billingperiods) を使用して、分析する請求期間を、その期間の請求書 ID と共に決定します。 請求書 ID は、以下の請求書 API で使用できます。 
-   **Invoices**: [Invoices API](https://docs.microsoft.com/rest/api/billing/invoices) を使用して、指定された請求期間の PDF 形式の請求書のダウンロード URL を取得します。

### <a name="enterprise-consumption"></a>Enterprise Consumption
"*(すべての API。Enterprise のみ)*"

-   **Balance Summary**: [Balance Summary API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) を使用して、残高、新規購入、Azure Marketplace サービス料金、調整、および超過料金に関する情報の月別サマリーを取得します。 この情報は、現在の請求期間または過去の任意の期間について取得できます。 企業はこのデータを使用して、手動で計算された集計料金との比較を実行することができます。 この API では、リソースに固有の情報またはコストの集計ビューは提供されません。
-   **Usage Details**: [Usage Details API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) を使用して、当月のファースト パーティ Azure 使用状況の詳細情報、特定の請求期間、またはカスタム日付期間を取得します。 企業は、このデータを使用して、料金と消費に基づいて請求書を手動で計算できます。また、存在する部門/組織情報を使用して、組織全体にわたってコストを計算することもできます。 データは、使用状況/コストのリソースに固有のビューを提供します。
-   **Marketplace Store Charge**: [Marketplace Store Charge API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) を使用して、当月のサード パーティ Azure 使用状況詳細情報、特定の請求期間、またはカスタム日付期間を取得します。 企業は、このデータを使用して、料金と消費に基づいて請求書を手動で計算できます。また、存在する部門/組織情報を使用して、組織全体にわたってコストを計算することもできます。 Marketplace Store Charge API は、使用状況/コストのリソースに固有のビューを提供します。
-   **Price Sheet**: [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) を使用すると、指定された加入契約と請求期間に対する各測定に適用可能な課金率が提供されます。 この率情報を使用状況の詳細およびマーケットプレースの使用状況情報と組み合わせて使用して、予測される請求書を手動で計算することができます。
-   **Billing Periods**: [Billing Periods API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) を使用して、請求期間の一覧と、その請求期間に関連する 4 セットの Enterprise API データの API ルートを示すプロパティを取得します (BalanceSummary、UsageDetails、Marketplace Charges、および PriceSheet)。
-   **Reserved Instance Recommendations**: [Reserved Instance Recommendations API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) は、お客様の 7 日、30 日、または 60 日間の仮想マシンの使用状況を調べ、単一および共有購入推奨事項を提供します。 予約インスタンス API を使用すると、お客様は、予想されるコスト削減と推奨される購入額を分析することができます。

## <a name="next-steps"></a>次の手順

- Azure Billing API シリーズを使用してプログラムで Azure の使用状況に関する分析情報を取得する方法については、[Azure Billing API シリーズの概要](billing-usage-rate-card-overview.md)に関するページを参照してください。
