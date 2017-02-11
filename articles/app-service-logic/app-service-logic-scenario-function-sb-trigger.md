---
title: "ロジック アプリのシナリオ: Azure Functions Service Bus トリガーの作成 | Microsoft Docs"
description: "Azure Functions を使用してロジック アプリの Service Bus トリガーを作成する"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3f8031d31d1291972026430e67005863579ae911


---
# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>ロジック アプリのシナリオ: Azure Functions を使用した Azure Service Bus トリガーの作成
Azure Functions を使用して、実行時間の長いリスナーまたはタスクをデプロイする必要がある場合に、ロジック アプリのトリガーを作成できます。 たとえば、キューをリッスンする関数を作成し、プッシュ トリガーとしてすぐにロジック アプリを起動することができます。

## <a name="build-the-logic-app"></a>ロジック アプリを構築する
この例では、トリガーする必要のあるロジック アプリごとに関数を実行します。 最初に、HTTP 要求トリガーのあるロジック アプリを作成します。 関数は、キュー メッセージが受信されたときに、そのエンドポイントを呼び出します。  

1. 新しいロジック アプリを作成して、 **[手動 - HTTP 要求を受信したとき]** トリガーを選択します。  
   必要に応じて、 [jsonschema.net](http://jsonschema.net)などのツールを使用して、キュー メッセージで使用する JSON スキーマを指定することができます。 スキーマをトリガーに貼り付けます。 そうすることでデザイナーがデータの形状を把握し、ワークフローを介して簡単にプロパティを流し込むことができます。
2. キュー メッセージを受信した後に発生する追加の手順を追加します。 たとえば、Office 365 を使用して電子メールを送信します。  
3. このロジック アプリを保存して、このロジック アプリのトリガーに使用するコールバック URL を生成します。 この URL がトリガー カードに表示されます。

![コールバック URL がトリガー カードに表示されます。][1]

## <a name="build-the-function"></a>関数を構築する
次に作成するのは、トリガーとしてキューをリッスンする関数です。

1. [Azure Functions ポータル](https://functions.azure.com/signin)で、**[新しい関数]** を選択し、次に **[ServiceBusQueueTrigger - C#]** テンプレートを選択します。
   
    ![Azure Functions ポータル][2]
2. Service Bus キュー (Azure Service Bus SDK の `OnMessageReceive()` リスナーを使用) への接続を構成します。
3. キュー メッセージをトリガーとしてロジック アプリのエンドポイント (先ほど作成したもの) を呼び出す単純な関数を作成します。 関数の完全な例を次に示します。 例では、 `application/json` メッセージのコンテンツの種類を使用していますが、必要に応じて変更できます。
   
   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
   
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

テストするには、 [Service Bus エクスプ ローラー](https://github.com/paolosalvatori/ServiceBusExplorer)などのツールを使用してキュー メッセージを追加します。 関数がメッセージを受信した直後にロジック アプリが起動することを確認します。

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG



<!--HONumber=Nov16_HO3-->


