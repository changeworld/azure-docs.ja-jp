---
title: REST API で Azure エンタープライズ加入契約請求データを確認する | Microsoft Docs
description: Azure REST API を使用して、エンタープライズ加入契約の課金情報を確認する方法を説明します。
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064617"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>REST API を使用してエンタープライズ加入契約の請求書を確認する

Azure レポート API は Azure コストの確認や管理に役立ちます。

ここでは、Enterprise アカウント加入契約に関連付けられている現在の請求書を取得する方法を説明します。

現在の請求書を取得するには:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>要求を作成する  

`{enrollmentID}` パラメーターは必須です。これには、Enterprise アカウント (EA) の加入契約 ID を指定する必要があります。

次のヘッダーは必須です｡ 

|要求ヘッダー|説明|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 `application/json` を設定します。|  
|*Authorization:*|必須。 有効な `Bearer` [API キー](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)を設定します。 |  

この例は、現在の請求期間の詳細を返す同期呼び出しを示しています。 パフォーマンスの理由から、同期呼び出しでは先月の情報が返されます。  また、[API を非同期に](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)呼び出すと、36 か月分のデータを返すこともできます。


## <a name="response"></a>Response  

応答に成功すると､状態コード 200 (OK) が返されます｡内容は､アカウントの詳しいコストの一覧です｡

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

**data** の各項目は請求内容を表します。

|Response プロパティ|説明|
|----------------|----------|
|**cost** | データセンターの場所に適した通貨での請求額。 |
|**subscriptionGuid** | サブスクリプションのグローバルに一意の ID。 | 
|**departmentId** | 部署の ID (ある場合)。 |
|**date** | 料金が課金された日付。 |
|**タグ** | サブスクリプションに関連付けられたタグを含む JSON 文字列。 |
|**resourceGroup**|コストを発生させたオブジェクトを含むリソース グループの名前。 |
|**nextLink**| 設定時には、詳細の次のページの URL が指定されます。 ページが最終ページの場合は空白です。 |  
||
  
部署 ID、リソース グループ、タグ、および関連するフィールドは、EA 管理者によって定義されます。  

この例は省略されたものです。response の各フィールドの詳しい説明については、[使用状況の詳細の取得](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)に関する記事をご覧ください。 

その他の状態コードは､エラー状態を示します｡ そのような場合､response オブジェクトによって､要求が失敗した理由が説明されます。

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>次の手順 
- 「[Enterprise Reporting の概要](https://docs.microsoft.com/azure/billing/billing-enterprise-api)」を参照してください。
- [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) について調べます。   
- [Azure Rest API の開始](https://docs.microsoft.com/rest/api/azure/)   
