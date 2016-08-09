<properties
	pageTitle="ロジック アプリに HTTP + Swagger アクションを追加する | Microsoft Azure"
	description="HTTP + Swagger アクションと操作の概要"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# HTTP + Swagger アクションの概要

HTTP + Swagger アクションでは、[Swagger ドキュメント](https://swagger.io)を使用して REST エンドポイントへのファースト クラス コネクタを作成できます。

- ファースト クラスのデザイナー エクスペリエンスで、ロジック アプリを拡張して REST エンドポイントを呼び出します。

ロジック アプリで HTTP + Swagger アクションの使用を開始するには、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

---

## トリガーまたはアクションとしての HTTP + Swagger の使用

HTTP + Swagger トリガーおよびアクションは [HTTP アクション](connectors-native-http.md)と同様に機能しますが、[Swagger メタデータ](https://swagger.io)を使用してデザイナーで API と出力の状態を表示することでデザイン エクスペリエンスが向上します。また、HTTP + Swagger をトリガーとして使用することもできます。ポーリング トリガーを実装する場合は、[こちらのドキュメントに記載](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers)されているポーリング パターンに従う必要があります。
	
Logic Apps のトリガーとアクションの詳細については、[こちら](connectors-overview.md)をご覧ください。

ワークフローのアクションとして HTTP + Swagger 操作を使用する例を次に示します。

1. **[新しいステップ]** をクリックします。
1. **[アクションの追加]** を選択します。
1. アクションの検索ボックスに「swagger」と入力して、HTTP + Swagger アクションを表示します。

	![HTTP + Swagger アクションの選択](./media/connectors-native-http-swagger/using-action-1.png)

1. Swagger ドキュメントの URL を入力します。
	- この URL は HTTPS エンドポイントである必要があり、デザイナーで使用するために CORS が有効になっている必要があります。Swagger ドキュメントがこれを満たしていない場合は、[CORS を有効にした Azure Storage](#hosting-swagger-from-storage)を使用してドキュメントを格納できます。
1. 読み取りの横をクリックし、Swagger ドキュメントからレンダリングします。
1. HTTP 呼び出しに必要なすべてのパラメーターを追加します。

	![Complete HTTP action](./media/connectors-native-http-swagger/using-action-2.png)

1. ツール バーの左上隅にある [保存] をクリックすると、ロジック アプリが保存および発行 (アクティブ化) されます。

### ストレージからの Swagger のホスト

ホストされていないか、デザイナーで使用するために必要なセキュリティとクロス オリジンの要件を満たしていない Swagger ドキュメントを参照することが必要な場合があります。この場合、Swagger ドキュメントを Azure Storage に格納し、CORS を有効にすることで、ドキュメントを参照できます。Azure Storage で Swagger を作成、構成し、格納する手順を次に示します。

1. [BLOB ストレージを含む Azure ストレージ アカウントを作成](../storage/storage-create-storage-account.md)し、アクセス許可を "パブリック アクセス" に設定する
1. BLOB で CORS を有効にする
	- [この PowerShell スクリプト](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)を使用すると、この設定を自動的に構成できます。
1. Swagger ファイルを Azure BLOB にアップロードする
	- これは、[Azure ポータル](https://portal.azure.com)、または [Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールから実行できます。
1. Azure BLOB 内のドキュメントへの HTTPS リンクを参照する (`https://*storageAccountName*.blob.core.windows.net/*container*/*filename*` の形式に従います)。

---

## 技術的な詳細

このコネクタでサポートされているトリガーとアクションの詳細を次に示します。

## HTTP + Swagger トリガー

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらを参照してください。](connectors-overview.md) HTTP + Swagger コネクタにはトリガーが 1 つあります。

|トリガー|説明|
|---|---|
|HTTP + Swagger|HTTP 呼び出しを実行し、応答コンテンツを返します|

## HTTP + Swagger アクション

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらを参照してください。](connectors-overview.md) HTTP + Swagger コネクタには、使用可能なアクションが 1 つあります。

|アクション|説明|
|---|---|
|HTTP + Swagger|HTTP 呼び出しを実行し、応答コンテンツを返します|

### アクションの詳細

HTTP + Swagger コネクタには、使用可能なアクションが 1 つ用意されています。各アクションの情報、必須および任意の入力フィールド、アクションの使用に関連した対応する出力の詳細を次に示します。

#### HTTP + Swagger

Swagger メタデータを使用して HTTP 送信要求を行います。* は必須フィールドを意味します。

|表示名|プロパティ名|説明|
|---|---|---|
|メソッド*|method|使用する HTTP 動詞|
|URI*|uri|HTTP 要求の URI|
|ヘッダー|headers|含める HTTP ヘッダーの JSON オブジェクト|
|本文|body|HTTP 要求の本文|
|認証|authentication|要求に使用する認証 - [詳細については HTTP を参照](./connectors-native-http.md#authentication)|
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

---

## 次のステップ

ロジック アプリに取り組む方法とコミュニティの詳細を次に示します。

## ロジック アプリを作成します

プラットフォームを試用し、[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)してください。[API リスト](apis-list.md)を参照すると、ロジック アプリで使用可能な他のコネクタについて確認できます。

<!---HONumber=AcomDC_0727_2016-->