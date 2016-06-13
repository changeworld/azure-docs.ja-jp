<properties
   pageTitle="Logic Apps のループ、スコープ、分割処理 | Microsoft Azure"
   description="Logic Apps のループ、スコープ、分割処理の概念"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Logic Apps のループ、スコープ、分割処理
  
>[AZURE.NOTE] 本記事は、Logic Apps の 2016-04-01-preview 以降のスキーマを対象としています。概念は以前のスキーマに似ていますが、スコープはこのスキーマ以降でのみ使用できます。
  
## ForEach ループと配列
  
Logic Apps では、一連のデータをループ処理し、各項目に対してアクションを実行できます。これは、`foreach` アクションを使用して実行できます。デザイナーでは、for each ループの追加を指定できます。反復処理を行う配列を選択した後、アクションの追加を開始できます。現在は、foreach ループにつきアクションは 1 つに制限されていますが、この制限は今後数週間のうち解除される予定です。ループ内では、配列のそれぞれの値で行われる処理を指定できるようになります。

コード ビューを使用した場合は、次のように for each ループを指定できます。これは、"microsoft.com" を含む各メール アドレスにメールを送信する for each ループの例です。

```
{
    "forEach_email": {
        "type": "foreach",
        "foreach": "@triggerBody()['emails']",
        "expression": "@contains(item(), 'microsoft.com')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        }
    }
}
```
  
  `foreach` アクションでは、最大 5,000 行の配列に対して反復処理を実行できます。各反復処理は並列実行できます。そのため、フロー制御が必要な場合は、メッセージをキューに追加することが必要になることがあります。
  
## Until ループ
  
  条件が満たされるまで 1 つのアクションまたは一連のアクションを実行できます。この最も一般的なシナリオとして、求めている応答が得られるまでエンドポイントを呼び出す処理があります。デザイナーでは、until ループの追加を指定できます。ループ内にアクションを追加した後、終了条件とループの上限を設定できます。ループのサイクル間には 1 分の遅延があります。
  
  コード ビューを使用した場合は、次のように until ループを指定できます。これは、応答本文に値 "Completed" が含まれるまで HTTP エンドポイントを呼び出す例です。この処理は、次のいずれかのタイミングで終了します。
  
  * HTTP 応答の状態が "Completed" になったとき
  * 1 時間にわたって試行されたとき
  * 100 回ループしたとき
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
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
  
## SplitOn と分割処理

トリガーは、項目の配列を受け取ったときに、これを分割して項目ごとにワークフローを開始したい場合があります。この処理は、`spliton` コマンドを使用して実現できます。既定では、トリガーの Swagger によって、配列であるペイロードが指定されると、`spliton` が追加され、項目ごとに実行が開始されます。SplitOn は、トリガーにのみ追加できます。これは、定義のコード ビューで手動で構成またはオーバーライドできます。現時点では、SplitOn を使用して、配列を最大 5,000 項目に分割できます。`spliton` を使用したうえで、同期応答パターンを実装することはできません。`spliton` のほかに `response` アクションを含む任意のワークフローは、呼び出されると非同期に実行され、即時に `202 Accepted` 応答を送信します。

SplitOn は、次の例に示すように、コード ビューで指定できます。この例では、項目の配列を受け取り、行ごとに分割します。

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

## スコープ

スコープを使用すると、一連のアクションをグループ化することができます。これは、例外処理を実装する場合に特に便利です。デザイナーでは、新しいスコープを追加し、その内部に任意のアクションを追加できます。スコープは、コード ビューで次のように定義できます。


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

<!---HONumber=AcomDC_0601_2016-->