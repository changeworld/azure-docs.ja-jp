---
title: "ワークフローでのループとスコープの作成またはデータの分割処理 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps で、ループを作成してデータを反復処理したり、アクションをスコープにグループ化したり、データを分割処理して複数のワークフローを開始したりします。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: estfan; LADocs
ms.openlocfilehash: 24a19ef7d93b4b0d006dfb0aed38f88d3d821d99
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logic Apps のループ、スコープ、分割処理
  
Logic Apps には、ワークフロー内で配列、コレクション、バッチ、ループを操作するためのいくつかの方法が用意されています。
  
## <a name="foreach-loop-and-arrays"></a>ForEach ループと配列
  
Logic Apps では、一連のデータをループ処理し、各項目に対してアクションを実行できます。  コレクションのループ処理を行うには、`foreach` アクションを使用します。  デザイナーで for each ループを追加してください。  反復処理を行う配列を選択した後、アクションの追加を開始できます。  for each ループごとに複数のアクションを追加できます。  ループ内では、配列のそれぞれの値で行われる処理を指定できるようになります。

  この例は、"microsoft.com" を含む各メール アドレスにメールを送信するものです。 コード ビューを使用した場合は、次の例のように for each ループを指定できます。

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  `foreach` アクションでは、数千の要素を含んだ配列に対して反復処理を実行できます。  反復処理は、既定で並列実行されます。  配列と同時実行の制限について詳しくは、[制限と構成](logic-apps-limits-and-config.md)に関するページを参照してください。

### <a name="sequential-foreach-loops"></a>シーケンシャルな ForEach ループ

foreach ループを順番に実行できるようにするには、`Sequential` 操作オプションを追加する必要があります。

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Until ループ
  
  条件が満たされるまで 1 つのアクションまたは一連のアクションを実行できます。  until ループの最も一般的なシナリオとして、求めている応答が得られるまでエンドポイントを呼び出す処理があります。  デザイナーでは、until ループの追加を指定できます。  ループ内にアクションを追加した後、終了条件とループの上限を設定できます。
  
  これは、応答本文に値 "Completed" が含まれるまで HTTP エンドポイントを呼び出す例です。  この処理は、次のいずれかのタイミングで終了します。 
  
  * HTTP 応答の状態が "Completed" になったとき
  * 1 時間にわたって試行されたとき
  * 100 回ループしたとき
  
  コード ビューを使用した場合は、次の例のように until ループを指定できます。
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn と分割処理

トリガーによって受け取った項目の配列を分割して、項目ごとにワークフローを開始する必要がある場合があります。  この分割処理は、`spliton` コマンドを使用して実現できます。  既定では、トリガーの Swagger によって、配列であるペイロードが指定されると、`spliton` が追加されます。 配列内の項目ごとに、`spliton` コマンドによって実行が開始されます。  SplitOn は、手動で構成またはオーバーライドできるトリガーにのみ追加できます。 `spliton` を使用したうえで、同期応答パターンを実装することはできません。  `spliton` のほかに `response` アクションを含む任意のワークフローは、呼び出されると非同期に実行され、即時に `202 Accepted` 応答を送信します。  

  この例では、項目の配列を受け取り、行ごとに分割します。 SplitOn は、次の例に示すように、コード ビューで指定できます。

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>スコープ

スコープを使用すると、一連のアクションをグループ化することができます。  スコープは、例外処理を実装する場合に便利です。  デザイナーでは、新しいスコープを追加し、その内部に任意のアクションを追加できます。  スコープは、コード ビューで次の例のように定義できます。


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
