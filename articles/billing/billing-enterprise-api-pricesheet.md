---
title: "Azure Billing Enterprise API - 価格シート |Microsoft Docs"
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
ms.openlocfilehash: e5deb660ae5cfed5ac64000d070f4dd2e42ec9c0
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---pricesheet-preview"></a>企業ユーザー向けの Reporting API - 価格シート (プレビュー)

Price Sheet API を使用すると、特定の加入契約と請求期間に対する各測定に適用可能な課金率が提供されます。

##<a name="request"></a>要求
追加の必要がある共通のヘッダー プロパティは、[ここ](billing-enterprise-api.md)で指定します。 請求期間が指定されていない場合は、現在の請求期間に対するデータが返されます。

|メソッド | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|


## <a name="response"></a>応答

    
          [
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                  "billingPeriodId": "201704",
                  "meterName": "A1 VM",
                  "unitOfMeasure": "100 Hours",
                  "includedQuantity": 0,
                  "partNumber": "N7H-00015",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                  "billingPeriodId": "201404",
                  "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                  "unitOfMeasure": "100 GB",
                  "includedQuantity": 0,
                  "partNumber": "N9H-00402",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            ...
        ]
    

**応答プロパティの定義**

|プロパティ名| 型| Description
|-|-|-|
|id| string| 特定の PriceSheet 項目 (請求期間ごとの測定) を表す一意の ID|
|billingPeriodId| string| 特定の請求期間を表す一意の ID|
|meterName| string| 測定名|
|unitOfMeasure| string| サービスを測定するための測定単位|
|includedQuantity| 小数点| 含まれる数量 |
|partNumber| string| 測定に関連付けられた部品番号|
|unitPrice| 小数点| 測定の単価|
|currencyCode| string| 単価の通貨コード|
<br/>
## <a name="see-also"></a>関連項目

* [Billing Periods API](billing-enterprise-api-billing-periods.md)

* [Usage Detail API](billing-enterprise-api-usage-detail.md)

* [Balance and Summary API](billing-enterprise-api-balance-summary.md)

* [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md)

