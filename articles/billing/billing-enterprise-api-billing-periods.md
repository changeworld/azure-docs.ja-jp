---
title: "Azure Billing Enterprise API - 請求期間 |Microsoft Docs"
description: "Enterprise Azure の顧客がプログラムで消費データを取り出せるようにする Reporting API について説明します。"
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: ab8d7fefb64b1358bb1b9667d280cb53fc2f636c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---billing-periods-preview"></a>企業ユーザー向けの Reporting API - 請求期間 (プレビュー)

Billing Periods API は、指定した加入契約の消費量データが含まれる請求期間リストを逆時系列順に返します。 各期間には、BalanceSummary、UsageDetails、Marktplace Charges、および PriceSheet の 4 セットのデータの API ルートを示すプロパティが含まれています。 期間にデータがない場合、対応するプロパティは null です。 


##<a name="request"></a>要求 
追加の必要がある共通のヘッダー プロパティは、[ここ](billing-enterprise-api.md)で指定します。 

|メソッド | 要求 URI|
|-|-|
|GET| https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingperiods|


## <a name="response"></a>応答
 
    
    
        [
            {
                  "billingPeriodId": "201704",
                  "billingStart": "2017-04-01T00:00:00Z",
                  "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                  "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                  "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                  "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },            
            ....
        ]
    

**応答プロパティの定義**

|プロパティ名| 型| Description
|-|-|-|
|billingPeriodId| string| 特定の請求期間を表す一意の ID|
|billingStart| datetime| 期間開始日を表す ISO 8601 文字列|
|billingEnd| datetime| 期間終了日を表す ISO 8601 文字列|
|balanceSummary| string| この期間の残高サマリー データにルーティングする URL パス|
|usageDetails| string| この期間の使用状況詳細データにルーティングする URL パス|
|marketplaceCharges| string| この期間の Marketplace 料金データにルーティングする URL パス|
|priceSheet| string| この期間の価格シート データにルーティングする URL パス|

<br/>
## <a name="see-also"></a>関連項目
* [Balance and Summary API](billing-enterprise-api-balance-summary.md)

* [Usage Detail API](billing-enterprise-api-usage-detail.md) 

* [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md) 

* [Price Sheet API](billing-enterprise-api-pricesheet.md)
