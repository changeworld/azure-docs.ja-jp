---
title: Azure Blockchain Workbench REST API を使用する
description: Azure Blockchain Workbench REST API を使用する方法を示すシナリオ
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 63e87c59a2e560b5a78708482c2ed89f5f8fb127
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257905"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Azure Blockchain Workbench REST API を使用する 

Azure Blockchain Workbench REST API は、開発者やインフォメーション ワーカーが ブロックチェーン アプリケーションに豊富な統合をビルドする方法を提供します。 このドキュメントでは、Workbench REST API のいくつかの主要なメソッドについて説明します。 サインインした ユーザーが、割り当てられたブロックチェーン アプリケーションを表示して対話ができるようなカスタム ブロックチェーン クライアントを、開発者が作成するシナリオを検討します。 ユーザーは、クライアントでコントラクトのインスタンスを表示し、スマート コントラクトでアクションを実行することができます。 クライアントはサインインしたユーザーのコンテキストで Workbench REST API を使用して、次の操作をします。

* List applications
* アプリケーションのワークフローを一覧表示する
* ワークフローのスマート コントラクトのインスタンスを一覧表示する
* コントラクトで使用可能なアクションを一覧表示する
* コントラクトのアクションを実行する

このシナリオで使用されているブロックチェーン アプリケーションのサンプルは [GitHub からダウンロード](https://github.com/Azure-Samples/blockchain)できます。 

## <a name="list-applications"></a>List applications

ブロックチェーン クライアントにユーザーがサインインしたら、最初のタスクは、そのユーザーのすべての Blockchain Workbench アプリケーションを取得することです。 このシナリオでは、ユーザーは、次の 2 つのアプリケーションへのアクセスがあります。

1.  [Asset Transfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2.  [Refrigerated Transportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

[Applications GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) を使用します。

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

応答には、ユーザーが Blockchain Workbench でアクセス権を持っているすべてのブロックチェーン アプリケーションが一覧表示されます。 Blockchain Workbench 管理者は、すべてのブロックチェーン アプリケーションを取得しますが、Workbench の非管理者は、少なくとも 1 つの関連付けられたアプリケーション ロールを持つ、または関連付けられたスマート コントラクトのインスタンス ロールを持つブロックチェーンをすべて取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>アプリケーションのワークフローを一覧表示する

ユーザーが利用可能なブロックチェーン アプリケーションを選択すると (この場合は **Asset Transfer**)、ブロックチェーン クライアントは指定されたブロックチェーン アプリケーションのすべてのワークフローを取得します。 その後ユーザーは、ワークフローのすべてのスマート コントラクトのインスタンスが表示される前に、該当するワークフローを選択できます。 各ブロックチェーン アプリケーションには 1 つ以上のワークフローがあり、各ワークフローにはゼロまたはスマート コントラクトのインスタンスがあります。 ブロックチェーン クライアント アプリケーションをビルドする際に、ブロックチェーン アプリケーションのワークフローが 1 つしかない場合は、ユーザーが適切なワークフローを選択することができるユーザー エクスペリエンス フローをスキップすることをお勧めします。 この場合では、**Asset Transfer** には **Asset Transfer** と呼ばれる 1 つのみのワークフローがあります。

[Applications Workflows GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) を使用します。

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

応答には、ユーザーが Blockchain Workbench でアクセス権を持っている指定されたブロックチェーン アプリケーションのすべてのワークフローが一覧表示されます。 Blockchain Workbench 管理者は、すべてのブロックチェーン ワークフローを取得しますが、Workbench の非管理者は、少なくとも 1 つの関連付けられたアプリケーション ロールを持つ、またはスマート コントラクトのインスタンス ロールに関連付けられているすべてのワークフローを取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>ワークフローのスマート コントラクトのインスタンスを一覧表示する

ユーザーが該当するワークフローを選択すると (この場合は **Asset Transfer**)、ブロックチェーン クライアントは指定されたワークフローのすべてのスマート コントラクトのインスタンスを取得します。 この情報を利用して、ワークフローのすべてのスマート コントラクトのインスタンスを表示して、表示されたスマート コントラクトのインスタンスにユーザーがディープ ダイブすることができます。 この例では、ユーザーがスマート コントラクトのインスタンスの 1 つにアクションを実行させるように対話することを検討します。

[Contracts GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget) を使用します。

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

応答には、指定したワークフローのすべてのスマート コントラクトのインスタンスが一覧表示されます。 Workbench 管理者は、すべてのスマート コントラクトのインスタンスを取得しますが、Workbench の非管理者は、少なくとも 1 つの関連付けられたアプリケーション ロールを持つ、またはスマート コントラクトのインスタンス ロールに関連付けられているすべてのスマート コントラクトのインスタンスを取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>コントラクトで使用可能なアクションを一覧表示する

ユーザーがコントラクトの 1 つにディープ ダイブすることを決定すると、ブロックチェーン クライアントはコントラクトの状態に基づいて、利用可能なすべてのアクションを表示します。 この例では、ユーザーが作成した新しいスマート コントラクトに利用可能なすべてのアクションが示されています。

* Modify: ユーザーはアセットの価格と説明を変更することができます。
* Terminate: ユーザーはアセットのコントラクトを終了することができます。

[Contract Action GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget) を使用します。

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

応答には、指定されたスマート コントラクトのインスタンスの現在の状態に基づいて、ユーザーが実行できるすべてのアクションが一覧表示されます。 ユーザーは、ユーザーが関連付けられたアプリケーション ロールを持つ、またはスマート コントラクトのインスタンス ロールに関連付けられている場合、指定されたスマート コントラクトのインスタンスの現在の状態に該当するすべてのアクションを取得します。

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>コントラクトのアクションを実行する

その後、ユーザーは指定されたスマート コントラクトのインスタンスにアクションを実行することができます。 ここでは、ユーザーがアセットの説明と価格を次の例のように変更するシナリオを示します。

* 説明: "My updated car"
* 価格: 54321

[Contract Action POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost) を使用します。

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

ユーザーは、指定されたスマート コントラクトのインスタンスの状態とユーザーが関連付けられたアプリケーション ロールまたはスマート コントラクトのインスタンス ロールに基づいてのみアクションを実行することができます。 投稿が成功すると、応答本文なしで HTTP 200 OK の応答が返されます。

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API リファレンス](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)