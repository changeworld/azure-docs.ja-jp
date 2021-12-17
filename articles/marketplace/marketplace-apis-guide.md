---
title: パートナーのビジネスと Microsoft の eコマース プラットフォームおよび Azure Marketplace を連携させる
description: パートナーのビジネスと Microsoft の eコマース プラットフォーム (Azure Marketplace) を連携させます。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: MarlEva
ms.author: maevan
ms.date: 05/13/2021
ms.openlocfilehash: ee5613a316d1a45e296ca25af759590192342c3e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057406"
---
# <a name="align-your-business-with-our-e-commerce-platform"></a>パートナーのビジネスと Microsoft の eコマース プラットフォームを連携させる

この記事では、コマーシャル マーケットプレースのユーザー インターフェイス (UI) とプログラムのアプリケーション プログラミング インターフェイス (API) を組み合わせて、パートナーのビジネス プロセスをサポートする方法について説明します。 API の下にあるリンクは、開発者が自分の CRM システムとコマーシャル マーケットプレースを統合するために使用できる特定のインターフェイスを指しています。

## <a name="overview-of-activities"></a>アクティビティの概要

以下のアクティビティは順番になってはいません。 使用するアクティビティは、ビジネス ニーズと販売プロセスによって異なります。 このガイドでは、さまざまな API を統合して各アクティビティを自動化する方法について説明します。

| <center>アクティビティ | ISV 販売アクティビティ | 対応する Marketplace API | 対応する Marketplace UI |
| --- | --- | --- | --- |
| <center>**1. 製品マーケティング**<br><img src="media/api-guide/icon-product-marketing.png" alt="The icon for product marketing"> | 製品のメッセージ、位置付け、プロモーション、価格を作成する | 製品のメッセージ、位置付け、プロモーション、価格を作成する<br>[Partner Ingestion API](https://apidocs.microsoft.com/services/partneringestion/)<br>[Azure Apps Onboarding API](azure-app-apis.md)</ul> | 製品のメッセージ、位置付け、プロモーション、価格を作成する<br>パートナー センター (PC) → オファーの作成 |
| <center>**2. 需要生成**<br><img src="media/api-guide/icon-demand-generation.png" alt="The icon for demand generation"> | 製品のプロモーション<br>リードの育成<br>評価版、試用版、PoC | 製品のプロモーション<br>リードの育成<br>評価版、試用版、PoC<br>[D365、SFDC、Marketo 用のリード CRM コネクタ](partner-center-portal/commercial-marketplace-get-customer-leads.md)<br>[SalesForce CRM 用の共同販売 コネクタ](/partner-center/connector-salesforce)<br>[Dynamics 365 CRM 用の共同販売 コネクタ](/partner-center/connector-dynamics) | 製品のプロモーション<br>リードの育成<br>評価版、試用版、PoC<br>Azure Marketplace と AppSource<br>PC Marketplace 分析情報<br>PC 共同販売案件 |
| <center>**3. ネゴシエーションと見積もりの作成**<br><img src="media/api-guide/icon-negotiation-quote-creation.png" alt="The icon for negotiation and quote creation"> | T&C<br>価格<br>割引の承認<br>最終見積もり | T&C<br>価格<br>割引の承認<br>最終見積もり<br>[VM の CPP API](cloud-partner-portal-api-overview.md)<br>[AAD API の Microsoft Graph](../active-directory/reports-monitoring/concept-reporting-api.md)<br>[パートナー センター '7' API ファミリ](https://apidocs.microsoft.com/services/partnercenter) | T&C<br>価格<br>割引の承認<br>最終見積もり<br>PC → プラン (パブリックまたはプライベート) |
| <center>**4. 販売**<br><img src="media/api-guide/icon-sale.png" alt="The icon for sale"> | コントラクトの署名<br>収益の認識<br>請求処理<br>課金 | コントラクトの署名<br>収益の認識<br>請求処理<br>課金<br>[SaaS Fulfillment API v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[レポート API](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf) | コントラクトの署名<br>収益の認識<br>請求処理<br>課金<br>Azure portal/管理センター<br>PC Marketplace 報酬<br>PC 支払いレポート<br>PC Marketplace 分析<br>PC 共同販売終了 |
| <center>**5. メンテナンス**<br><img src="media/api-guide/icon-maintenance.png" alt="The icon for maintenance"> | 定期的な課金<br>超過分<br>製品のサポート | 定期的な課金<br>超過分<br>製品のサポート<br>[SaaS/AMAs: Billing API](https://partneranalytics-api.azureedge.net/partneranalytics-api/Programmatic%20Access%20to%20Commercial%20Marketplace%20Analytics%20Data_v1.pdf)<br>[SaaS Fulfillment API v.2](partner-center-portal/pc-saas-fulfillment-apis.md)<br>[Partner Payouts API](https://apidocs.microsoft.com/services/partnerpayouts) <br>[github](https://github.com/microsoft/Partner-Center-Payout-APIs)<br>[Metered billing API](marketplace-metering-service-apis.md)<br>[(EA Customer) Azure Consumption API](/rest/api/consumption/)<br>[(EA Customer) Azure Charges List API](/rest/api/consumption/charges/list) | 定期的な課金<br>超過分<br>製品のサポート<br>PC 支払いレポート<br>PC Marketplace 分析 |
| <center>**6. 契約終了**<br><img src="media/api-guide/icon-contract-end.png" alt="The icon for contract end"> | 更新または<br>Terminate |更新または<br>Terminate <br>[SaaS Fulfillment API v.2](./partner-center-portal/pc-saas-fulfillment-apis.md)<br>AMA/VM: auto-renew | 更新または<br>Terminate<br>PC Marketplace 分析 |
|

## <a name="next-steps"></a>次のステップ

- 必要に応じて、各 API の上記のリンクを参照してください。