---
title: 操作の取得 API | Azure Marketplace
description: オファーのすべての操作を取得するか、指定した operationId の特定の操作を取得します。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 1fbcc1d50dbc4488c4123be64e85de612233ccc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935774"
---
<a name="retrieve-operations"></a>操作の取得
===================

オファーのすべての操作を取得するか、指定した operationId の特定の操作を取得します。 クライアントは、クエリ パラメーターを使用して実行中の操作にフィルターを適用できます。

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI パラメーター
--------------

|  **Name**          |      **説明**                                                                                           | **データの種類** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  パブリッシャー ID。たとえば、`Contoso`                                                                   |  string       |
|  offerId           |  オファー ID                                                                                              |  string       |
|  operationId       |  オファーの操作を一意に識別する GUID。 operationId はこの API を使用して取得できます。また、[オファーの発行](./cloud-partner-portal-api-publish-offer.md) API など、実行時間の長い任意の操作に対する応答の HTTP ヘッダーでも返されます。  |   Guid   |
|  filteredStatus    | この API によって返されるコレクションを状態 (たとえば `running`) でフィルター処理するために使用されるオプションのクエリ パラメーター。  |   string |
|  api-version       | API の最新バージョン                                                                                           |    Date      |
|  |  |  |


<a name="header"></a>ヘッダー
------

|  **Name**          |  **値**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Authorization     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>本文の例
------------

### <a name="response"></a>Response

#### <a name="get-operations"></a>GET 操作

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET 操作

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>応答本文のプロパティ

|  **Name**                    |  **説明**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | 操作を一意に識別する GUID                                                       |
|  submissionType              | 報告されているオファーの操作の種類を特定します。たとえば、`Publish/GGoLive`      |
|  createdDateTime             | 操作が作成された UTC 日時                                                       |
|  lastActionDateTime          | 操作に対する最終更新の UTC 日時                                       |
|  status                      | 操作の状態。`not started` \| `running` \| `failed` \| `completed` のいずれか。 `running` 状態にできるのは、一度に 1 つの操作だけです。 |
|  error                       | 失敗した操作のエラー メッセージ                                                               |
|  |  |


### <a name="response-status-codes"></a>応答状態コード

| **コード**  |   **説明**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - 要求が正常に処理され、要求された操作が返されました。        |
|  400      | `Bad/Malformed request` - エラーの応答本文にさらに情報が含まれている場合があります。                    |
|  403      | `Forbidden` - クライアントは、指定の名前空間にアクセスできません。                          |
|  404      | `Not found` - 指定のエンティティが存在しません。                                                 |
|  |  |
