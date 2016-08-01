<properties
	pageTitle="ロジック アプリに HTTP アクションを追加する | Microsoft Azure"
	description="HTTP アクションとプロパティの概要"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="app-service-logic"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# HTTP アクションの概要

HTTP アクションを使用すると、組織のワークフローを拡張し、HTTP 経由で任意のエンドポイントと通信することができます。

- 管理対象の Web サイトがダウンしたときにアクティブ化する (トリガーする) ロジック アプリ ワークフローを作成します。
- HTTP 経由で任意のエンドポイントと通信して、ワークフローを他のサービスに拡張します。

ロジック アプリでの HTTP アクションの使用を開始するには、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事を参照してください。

---

## HTTP トリガーの使用

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。

ロジック アプリ デザイナーで HTTP トリガーをセットアップする方法の例を次に示します。

1. ロジック アプリで HTTP トリガーを追加します。
1. ポーリングする HTTP エンドポイントのパラメーターを入力します。
1. ポーリング頻度の繰り返し間隔を変更します。
1. これで、各チェック中に返される内容を含むロジック アプリが起動します。

![HTTP トリガー](./media/connectors-native-http/using-trigger.png)

### HTTP トリガーのしくみ

HTTP トリガーは、定期的な実行間隔で HTTP エンドポイントを呼び出します。既定では、HTTP 応答コードが 300 より小さい場合にロジック アプリが実行されます。HTTP 呼び出しの後に、ロジック アプリを起動するかどうかを判断する判断するために評価する条件をコードビューに追加することができます。返された状態コードが `400` 以上の場合に常に起動する HTTP トリガーの例を次に示します。

```javascript
"Http": 
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

HTTP トリガーのパラメーターの詳細については、[MSDN を参照](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger)してください。

## HTTP アクションの使用
	
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらを参照してください](connectors-overview.md)。

1. **[New Step (新しいステップ)]** ボタンを選択します。
1. **[Add an action (アクションの追加)]** を選択します。
1. アクションの検索ボックスに「HTTP」と入力して、HTTP アクションの一覧を表示します。

	![Select HTTP action](./media/connectors-native-http/using-action-1.png)

1. HTTP 呼び出しに必要なすべてのパラメーターを追加します。

	![Complete HTTP action](./media/connectors-native-http/using-action-2.png)

1. ツール バーの左上隅にある [保存] をクリックすると、ロジック アプリが保存および発行 (アクティブ化) されます。

---

## 技術的な詳細

このコネクタでサポートされているトリガーとアクションの詳細を次に示します。

## HTTP トリガー

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。 HTTP コネクタには 1 つのトリガーがあります。

|トリガー|説明|
|---|---|
|HTTP|HTTP 呼び出しを実行し、応答コンテンツを返します|

## HTTP アクション

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらを参照してください](connectors-overview.md)。 HTTP コネクタには、使用可能なアクションが 1 つあります。

|アクション|説明|
|---|---|
|HTTP|HTTP 呼び出しを実行し、応答コンテンツを返します|

### アクションの詳細

HTTP コネクタには、使用可能なアクションが 1 つ用意されています。各アクションの情報、必須および任意の入力フィールド、アクションの使用に関連した対応する出力の詳細を次に示します。

#### HTTP 要求
HTTP 送信要求を実行します。* は必須フィールドを意味します。

|表示名|プロパティ名|説明|
|---|---|---|
|メソッド*|method|使用する HTTP 動詞|
|URI*|uri|HTTP 要求の URI|
|ヘッダー|headers|含める HTTP ヘッダーの JSON オブジェクト|
|本文|body|HTTP 要求の本文|
<br>

**出力の詳細**

HTTP 応答

|プロパティ名|データ型|説明|
|---|---|---|
|ヘッダー|オブジェクト|応答ヘッダー|
|本文|オブジェクト|応答オブジェクト|
|状態コード|int|HTTP 状態コード|

### HTTP 応答

さまざまなアクションを呼び出すと、特定の応答を受け取る場合があります。次の表に、対応する応答と説明を示します。

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|

---

## 次のステップ

ロジック アプリに取り組む方法とコミュニティの詳細を次に示します。

## ロジック アプリを作成します

ここで、プラットフォームと、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)を試してみましょう。[API の一覧](apis-list.md)を参照すると、ロジック アプリで使用可能な他のコネクタが見つかります。

<!---HONumber=AcomDC_0720_2016-->