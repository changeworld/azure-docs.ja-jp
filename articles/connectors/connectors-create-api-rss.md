<properties
pageTitle="RSS | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。RSS コネクタを使用して、フィード アイテムを発行および取得できます。また、新しいアイテムがフィードに公開されたときに操作をトリガーすることもできます。"
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# RSS コネクタの使用



RSS コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [フロー](http://flows.microsoft.com)  

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

RSS コネクタは、アクションとして使用できます。RSS コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 RSS コネクタでは、次のアクションやトリガーを使用できます。

### RSS のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|すべての RSS フィード アイテムを取得します。|
### RSS のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|feedUrl|string|○|query|なし|フィード URL|

#### Response

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

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|feedUrl|string|○|query|なし|フィード URL|

#### Response

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
|値|array|いいえ |



### FeedItem


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|あり |
|title|string|あり |
|content|string|あり |
|links|array|いいえ |
|updatedOn|string|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0525_2016-->