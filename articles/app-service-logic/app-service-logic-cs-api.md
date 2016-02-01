<properties
   pageTitle="ロジック アプリでの C# API アプリの C# 式の実行 | Microsoft Azure"
   description="C# API アプリまたはコネクタ"
   services="app-service\logic"
   documentationCenter=".net"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="01/19/2016"
   ms.author="jehollan"/>

#C# API アプリ
C# API アプリを使用すると、ロジック アプリの実行中に単純な C# の式を簡単に実行できます。

##この API アプリを使用するタイミング
この API アプリの重要なシナリオは、記述するコードのライフサイクルをロジック アプリと同じにする場合と、このコードを他のシナリオで呼び出さないようにする場合です。

一方、ロジック アプリに依存しないライフサイクルを持つ再利用可能なコードのスニペットが必要な場合は、WebJobs API アプリを使用して簡単なコード式を作成し、これらをロジック アプリから呼び出す必要があります。

最後に、他のパッケージを追加したい場合は、Base64 エンコード バイナリ文字列 (Blob Storage からの出力など) としてコネクタにアセンブリ (.dll) で渡す必要があります。パッケージとアセンブリの柔軟性を高めたい場合は、WebJob が適しているでしょう。

JS で式を記述する場合は、[JavaScript API アプリ](app-service-logic-javascript-api.md)を使用してください。

##C# API アプリを作成する
C# API アプリを使用するには、まず C# API アプリのインスタンスを作成する必要があります。これは、ロジック アプリの作成時にインラインで作成するか、Azure Marketplace から C# API アプリを選択することで作成できます。

##Logic Apps デザイナー画面で C# API アプリを使用する
###トリガー
ロジック アプリ サービスが (定義した間隔で) ポーリングするトリガーを作成できます。`false` 以外が返された場合はロジック アプリが実行され、それ以外の場合は、次のポーリング間隔まで待機してもう一度確認します。

トリガーへの入力は、**C# の式** (評価される式) です。これは関数内で呼び出されます。ロジック アプリを実行しない場合は `false` を返す必要がありますが、ロジック アプリを実行する場合はこれ以外の値を返すことができます。応答の内容は、ロジック アプリのアクションで使用できます。

たとえば、1 時間のうち 15 ～ 30 分間のみロジック アプリを実行する簡単なトリガーを作成できます。

```
var d = new DateTime.Now; return (d.Minute > 15) && (d.Minute < 30);
```

###アクション

同様に、実行するアクションを指定できます。

アクションへの入力は、**C# の式** (評価される式) です。任意のコンテンツを取得するために、`return` ステートメントを含める必要があります。**コンテキスト オブジェクト**は、トリガーに渡すことができる省略可能なコンテキスト オブジェクトです。必要な数だけプロパティを定義できますが、JObject `{ ... }` をベースにする必要があり、キー名を使用してスクリプト内でオブジェクトを参照できます (値は名前に対応する JToken として渡されます)。**ライブラリ**は、スクリプトのコンパイルに含まれる .dll ファイルの省略可能な配列です。この配列は次の構造を使用し、出力として .dll を使用して Blob Storage コネクタの次に最適に機能します。

```javascript
[{"filename": "name.dll", "assembly": {Base64StringFromConnector}, "usingstatment": "using Library.Reference;"}]
```

たとえば、Office 365 使用している場合は、**新しい電子メール**をトリガーします。返されるオブジェクトは次のとおりです。

```javascript
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

```javascript
JArray YammerAttachments = new JObject();
foreach(var obj in (JArray)Attachments)
{
	JObject att = new JObject();
	att["Content"] = obj["content"];
	att["FileName"] = obj["Name"];
	YammerAttachments.Add(att);	
}
return YammerAttachments;
```

このアクションは、結果オブジェクトの関数から返されたオブジェクトを返します。このため、Yammer API アプリで **Attachments** プロパティの `@body('csapi').results` を参照できます。

## コネクタでできること
これでコネクタが作成されたため、ロジック アプリを使用してこのコネクタをビジネス フローに追加することができます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。「[API アプリとコネクタの管理と監視](../app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0121_2016-->