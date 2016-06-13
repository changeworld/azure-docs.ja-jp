<properties
   pageTitle="ロジック アプリのシナリオ: Azure Functions Service Bus トリガー | Microsoft Azure"
   description="Logic Apps の Service Bus トリガーとして Azure Functions を使用する方法について説明します。"
   services="app-service\logic,functions"
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
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# ロジック アプリのシナリオ: Azure Functions Service Bus トリガー

実行に時間のかかるリスナーやタスクをデプロイする必要があるときは、Azure Functions を Logic Apps のトリガーとして活用してください。たとえば、キューをリッスンする Azure 関数を作成し、プッシュ トリガーとして直接ロジック アプリを起動することができます。

## ロジック アプリの作成

この例では、トリガーする必要のあるロジック アプリごとに関数を実行状態にします。そこでまず、HTTP 要求トリガーを持つロジック アプリを作成する必要があります。キュー メッセージを受信するたび、そのエンドポイントが Azure 関数によって呼び出されます。

1. 新しいロジック アプリを開いて **[手動 - HTTP 要求を受信したとき]** トリガーを選択します。  
    * キュー メッセージに適用する JSON スキーマは、必要に応じて [jsonschema.net](http://jsonschema.net) などのツールを使って指定し、トリガーに貼り付けることができます。そうすることでデザイナーがデータの形状を把握し、ワークフローを介して簡単にプロパティを流し込むことができます。
1. キュー メッセージの受信後に実行する必要のあるステップがあれば追加します。たとえば Office 365 で電子メール メッセージを送信することもできます。  
1. このロジック アプリを保存して、このロジック アプリのトリガーに使用するコールバック URL を生成します。この URL がトリガー カードに表示されます。

![][1]

## Azure 関数の作成

次に作成するのは、トリガーとしてキューをリッスンする Azure 関数です。

1. [Azure Functions ポータル](https://functions.azure.com/signin)を開き、**[ServiceBusQueueTrigger - C#]** テンプレートで **[新しい関数]** を選択します。

    ![][2]

2. Service Bus キュー (Service Bus SDK の `OnMessageReceive()` リスナーを使用) への接続を構成します。
3. キュー メッセージでロジック アプリのエンドポイント (先ほど作成したもの) を呼び出す単純な関数を作成します。以下に示したのは、関数のコード例全体です。メッセージのコンテンツ タイプは `application/json` となっていますが、これは適宜変更してかまいません。

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

テストは、[Service Bus エクスプローラー](https://github.com/paolosalvatori/ServiceBusExplorer)などのツールを使い、キュー メッセージを追加することによって行います。そのメッセージが関数に渡った後、すぐにロジック アプリが呼び出されることを確認してください。

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->