<properties
   pageTitle="JavaScript API"
   description="JavaScript API"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/15/2015"
   ms.author="stepsic"/>

#JavaScript API アプリ
JavaScript API アプリを使用すると、*ロジック アプリの実行中に*単純な JavaScript の式を簡単に実行できます。

##この API アプリを使用するタイミング
この API アプリの重要なシナリオは、記述するコードのライフサイクルをロジック アプリと同じにする場合と、このコードを他のシナリオで呼び出さ*ない*ようにする場合です。

一方、ロジック アプリに依存しないライフサイクルを持つ再利用可能なコードのスニペットが必要な場合は、WebJobs API アプリを使用して簡単なコード式を作成し、これらをロジック アプリから呼び出す必要があります。

最後に、他のパッケージを追加したい場合は、JavaScript API アプリを使用してすべてのライブラリを追加できないため、WebJobs API アプリを使用する必要があります。

C# で式を記述する場合は、[C# API アプリ](app-service-logic-cs-api.md)を使用しください。

##JavaScript API アプリを作成する
JavaScript API アプリを使用するには、まず JavaScript API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成時にインラインで作成するか、Azure Marketplace から JavaScript API アプリを選択することで作成できます。

##Logic Apps デザイナー画面で JavaScript API アプリを使用する
###トリガー
ロジック アプリ サービスが (定義した間隔で) ポーリングするトリガーを作成できます。任意のコンテンツが返された場合はロジック アプリが実行され、それ以外の場合は、次のポーリング間隔まで待機してもう一度確認します。

トリガーへの入力は、**JavaScript の式** (評価される式) です。これは関数内で呼び出され、ロジック アプリを実行しない場合は `false` を返す必要がありますが、ロジック アプリを実行する場合はこれ以外の何でも返すことができます。応答の内容をロジック アプリのアクションで使用できるようになります。**コンテキスト オブジェクト**は、トリガーに渡すことができる省略可能なオブジェクトです。必要な数だけプロパティを定義できますが、最上位のエンティティをオブジェクトにする必要があります (`{ "bar" : 0}` など)。

たとえば、1 時間のうち 15 ～ 30 分の間のみロジック アプリを実行する簡単なトリガーを作成できます。

```
var d = new Date(); return (d.getMinutes() > 15) && (d.getMinutes() < 30);
```

###アクション

同様に、実行するアクションを指定できます。

アクションへの入力は、**JavaScript の式** (評価される式) です。任意のコンテンツを取得するために、`return` ステートメントを含める必要があります。**コンテキスト オブジェクト**は、トリガーに渡すことができる省略可能なオブジェクトです。必要な数だけプロパティを定義できますが、最上位のエンティティをオブジェクトにする必要があります (`{ "bar" : 0}` など)。

たとえば、Office 365 使用している場合は、**新しい電子メール**をトリガーします。返されるオブジェクトは次のとおりです。```
{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

ただし、Yammer の投稿にこれらの添付ファイルをアップロードする場合、Yammer 添付ファイルのスキーマは少し異なります。これをロジック アプリ内で解析できるようになりました。コンテキスト オブジェクトの場合は、`@triggerBody()` を渡すだけです。式の場合は、次のように渡します。

```
return Attachments.map(function(obj){var a = obj.Content; a.FileName = obj.Name; return a;})
```

このアクションは、関数から返された JSON を返します。このため、Yammer API アプリで **Attachments** プロパティの `@body('javascriptapi')` を参照できます。

## コネクタでできること
これでコネクタが作成されたため、このコネクタを Logic App を使用してビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=Oct15_HO4-->