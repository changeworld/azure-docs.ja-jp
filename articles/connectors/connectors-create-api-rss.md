<properties
pageTitle="RSS | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。RSS コネクタを使用して、フィード アイテムを発行および取得できます。また、新しいアイテムがフィードに公開されたときに操作をトリガーすることもできます。"
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# RSS コネクタの使用
RSS は、ブログ記事やニュースのヘッドラインのように、頻繁に更新されるコンテンツを発行するために使用する一般的な Web 配信形式です。多くのコンテンツ発行元は、ユーザーがサブスクライブすることができる RSS フィードを提供します。新しい項目を RSS フィードに発行するときに、RSS コネクタを使用してフィード情報を取得しフローをトリガーします。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

RSS コネクタは、アクションとして使用できます。RSS コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 RSS コネクタでは、次のアクションやトリガーを使用できます。

### RSS のアクション
実行できるアクションは以下のとおりです。

|アクション|Description|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|すべての RSS フィード アイテムを取得します。|
### RSS のトリガー
次のイベントをリッスンできます。

|トリガー | Description|
|--- | ---|
|新しいフィード アイテムが公開されたとき|新しいフィードが公開されたときに、ワークフローをトリガーします。|


## RSS への接続の作成

>[AZURE.INCLUDE [RSS フィードへの接続を作成する手順](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## RSS のリファレンス
適用されるバージョン: 1.0

## OnNewFeed
新しいフィード アイテムが公開されたとき: 新しいフィードが公開されたときにワークフローをトリガーします。

```GET: /OnNewFeed```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|feedUrl|string|○|query|なし|フィード URL|

#### 応答

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


## ListFeedItems
すべての RSS フィード アイテムの表示: すべての RSS フィード アイテムを取得します。

```GET: /ListFeedItems```

| 名前| データ型|必須|場所|既定値|Description|
| ---|---|---|---|---|---|
|feedUrl|string|○|query|なし|フィード URL|

#### 応答

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


## オブジェクト定義 

### TriggerBatchResponse[FeedItem]


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|値|array|なし |



### FeedItem


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|はい |
|title|string|はい |
|content|string|はい |
|links|array|なし |
|updatedOn|string|なし |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->