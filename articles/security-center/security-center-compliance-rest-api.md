---
title: REST API を使用した Azure Security Center のコンプライアンス監視 | Microsoft Docs
description: Azure Security Center REST API を使用して自動コンプライアンス スキャンの結果を確認します。
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819220"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Azure REST API を使用して Security Center のコンプライアンス結果を確認する

この記事では、サブスクリプションの一覧について、Azure REST API を使用してセキュリティ コンプライアンス情報を取得する方法を説明します。

## <a name="review-compliance-for-each-subscription"></a>サブスクリプションごとのコンプライアンスを確認する

次の例は、[Compliances - Get](/rest/api/securitycenter/compliances/get) 操作を使用して、指定のコンプライアンスとサブスクリプションのセキュリティ評価情報を取得します。

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

`{complianceName}` パラメーターは必須です。`{subscription-id}` について評価するコンプライアンスの名前を指定する必要があります。 出力には次のような評価結果が含まれます。

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>複数のサブスクリプションのコンプライアンスを確認する

複数のサブスクリプションについてデータを取得するには、まず、次のように [Subscriptions - List](/rest/api/resources/subscriptions/list) 操作を使用してサブスクリプションの一覧を取得する呼び出しを行います。 次に、返されたサブスクリプション ID ごとに上記の [Compliances - Get](/rest/api/securitycenter/compliances/get) を呼び出します。

API 呼び出しは次のとおりです。

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

これは、次の値を含む配列を返します。 上の呼び出しの subscriptionId 値を使用して、すべてのサブスクリプションのコンプライアンス情報を確認します。

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






