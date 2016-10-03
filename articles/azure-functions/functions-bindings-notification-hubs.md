<properties
	pageTitle="Azure Functions における通知ハブのバインド | Microsoft Azure"
	description="Azure Functions で Azure Notification Hub のバインドを使用する方法について説明します。"
	services="functions"
	documentationCenter="na"
	authors="wesmc7777"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Azure Functions における通知ハブの出力バインド

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Notification Hub のバインドを構成したりコーディングしたりする方法について説明します。

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

関数を使用して、構成された Azure Notification Hub でほんの数行のコードを使ってプッシュ通知を送信できます。ただし、通知ハブは、使用するプラットフォーム通知サービス (PNS) 用に構成する必要があります。Azure Notification Hub の構成方法や、通知を受け取るために登録するクライアント アプリケーションの開発方法の詳細については、[Notification Hubs の使用](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)に関するページを参照して、上部の目的のクライアント プラットフォームをクリックしてください。

## Azure Notification Hub 出力バインドの function.json

function.json ファイルは、次のプロパティを提供します。

- `name`: 通知ハブ メッセージの関数コードで使用される変数名。
- `type`: *"notificationHub"* に設定する必要があります。
- `tagExpression`: タグ式。これにより、タグ式に一致する通知を受信するように登録した一連のデバイスに通知を配信するように指定できます。詳細については、「[ルーティングとタグ式](../notification-hubs/notification-hubs-tags-segment-push-message.md)」を参照してください。
- `hubName`: Azure ポータル内の通知ハブ リソースの名前。
- `connection`: この接続文字列は、使用している通知ハブの *DefaultFullSharedAccessSignature* 値に設定された**アプリケーション設定**の接続文字列である必要があります。
- `direction` : *"out"* に設定する必要があります。
 
function.json の例:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

## Azure Notification Hub の接続文字列の設定

Notification Hub 出力バインドを使用するには、ハブの接続文字列を設定する必要があります。このためには、*[統合]* タブで、単に通知ハブを選択するか、新しいハブを作成します。

*DefaultFullSharedAccessSignature* の接続文字列を通知ハブに追加して、既存のハブの接続文字列を手動で追加することもできます。この接続文字列により、通知メッセージを送信するための関数アクセス権限が付与されます。*DefaultFullSharedAccessSignature* 接続文字列の値には、Azure ポータルの通知ハブ リソースのメイン ブレードにある **[キー]** ボタンからアクセスできます。ハブの接続文字列を手動で追加するには、次の手順を実行します。

1. Azure ポータルの **[Function App]** ブレードで、**[Function App Settings (関数アプリの設定)]、[Go to App Service settings (App Service の設定に移動)]** の順にクリックします。

2. **[設定]** ブレードで、**[アプリケーション設定]** をクリックします。

3. **[接続文字列]** セクションまでスクロールして、通知ハブの *DefaultFullSharedAccessSignature* 値の名前付きエントリを追加します。種類を **[カスタム]** に変更します。
4. 出力バインドの接続文字列名を参照します。上の例で使用した **MyHubConnectionString** と同様です。

## Azure Notification Hub のコード例 (Node.js タイマー トリガー) 

次の例では、`location` と `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

## Azure Notification Hub のコード例 (F# タイマー トリガー)

次の例では、`location` と `message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

	let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
	    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]

## Azure Notification Hub のコード例 (C# キュー トリガー)

次の例では、`message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

次の例では、有効な JSON 文字列を使用して、`message` を含む[テンプレート登録](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)の通知を送信します。

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

## 種類 Notification を使用した Azure Notification Hub キュー トリガーの C# コード例

次の例では、[Microsoft Azure Notification Hubs ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)で定義された種類 `Notification` の使用方法を示します。この種類、およびライブラリを使用するには、Function App の *project.json* ファイルをアップロードする必要があります。project.json ファイルは、次のような JSON テキスト ファイルです。

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

project.json ファイルのアップロードの詳細については、[project.json ファイルのアップロードに関するページ](functions-reference.md#fileupdate)を参照してください。

コード例:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Info($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## 次のステップ

[AZURE.INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->