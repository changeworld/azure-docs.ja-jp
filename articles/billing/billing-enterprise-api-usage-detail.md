---
title: "Azure Billing Enterprise API - 使用状況の詳細 |Microsoft Docs"
description: "Azure Billing Usage API と RateCard API について説明します。これらの API を使用すると、Azure のリソース消費と傾向を把握できます。"
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
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>企業ユーザー向けの Reporting API - 使用状況の詳細 (プレビュー)

Usage Detail API を使用すると、毎日の消費量の内訳と加入契約別の推定料金が提示されます。 この結果には、インスタンス、測定、および部署に関する情報も含まれています。 この API では、請求期間または指定された開始日と終了日によってクエリを実行できます。 
## <a name="consumption-apis"></a>Consumption API


##<a name="request"></a>要求 
追加の必要がある共通のヘッダー プロパティは、[ここ](billing-enterprise-api.md)で指定します。 請求期間が指定されていない場合は、現在の請求期間に対するデータが返されます。 カスタム期間の範囲は、開始日および終了日パラメーターを yyyy-MM-dd 形式で指定します。 サポートされている最大の時間範囲は、36 か月間です。  

|メソッド | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>応答

> データが大量になる可能性があるため、結果セットはページングされます。 nextLink プロパティ (ある場合) は、データの次のページへのリンクを指定します。 リンクが空の場合は、最後のページであることを示します。 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

**応答プロパティの定義**

|プロパティ名| 型| Description
|-|-|-|
|id| string| API 呼び出しの一意の ID。 |
|data| JSON 配列| 各インスタンス\測定に対する毎日の使用状況詳細の配列。|
|nextLink| string| データに複数のページがある場合、nextLink はデータの次のページを返す URL を示します。 |

## <a name="see-also"></a>関連項目
* [Billing Periods API](billing-enterprise-api-billing-periods.md)

* [Balance and Summary API](billing-enterprise-api-balance-summary.md)

* [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md) 

* [Price Sheet API](billing-enterprise-api-pricesheet.md)
