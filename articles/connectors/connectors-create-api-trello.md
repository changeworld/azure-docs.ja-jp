<properties
pageTitle="Trello | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。Trello を使用すると、職場でも家庭でも、すべてのプロジェクトを把握できます。プロジェクトを管理したり何かを整理するための、簡単で、自由度が高く、柔軟性があり、視覚化された方法です。Trello に接続してボード、一覧、およびカードを管理する"
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# Trello コネクタの使用



Trello コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [フロー](http://flows.microsoft.com)

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

Trello コネクタは、アクションとして使用できます。Trello コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Trello コネクタでは、次のアクションやトリガーを使用できます。

### Trello のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|ボードのカードを一覧表示します|
|[GetCard](connectors-create-api-trello.md#getcard)|ID を使用してカードを取得します|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|カードを更新します|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|カードを削除します|
|[CreateCard](connectors-create-api-trello.md#createcard)|Trello アカウントに新しいカードを作成します|
|[ListBoards](connectors-create-api-trello.md#listboards)|ボードを一覧表示します|
|[GetBoard](connectors-create-api-trello.md#getboard)|ID を使用してボードを取得します|
|[ListLists](connectors-create-api-trello.md#listlists)|ボードのカードの一覧を一覧表示します|
|[GetList](connectors-create-api-trello.md#getlist)|ID を使用して一覧を取得します|
### Trello のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|
|ボードに新しいカードが追加されたタイミング|ボードに新しいカードが追加されたときに、フローをトリガーします|
|一覧に新しいカードが追加されたタイミング|一覧に新しいカードが追加されたときに、フローをトリガーします|


## Trello への接続を作成する
Trello を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|はい|Trello 資格情報を提供します|
接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

>[AZURE.INCLUDE [Trello への接続を作成する手順](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Trello のリファレンス
適用されるバージョン: 1.0

## OnNewCardInBoard
ボードに新しいカードが追加されたタイミング: ボードに新しいカードが追加されたときに、フローをトリガーします。

```GET: /trigger/boards/{board_id}/cards```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|path|なし|カードを取得するボードの一意の ID|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## OnNewCardInList
一覧に新しいカードが追加されたタイミング: 一覧に新しいカードが追加されたときに、フローをトリガーします。

```GET: /trigger/lists/{list_id}/cards```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|query|なし|カードを取得するボードの一意の ID|
|list\_id|string|○|path|なし|カードを取得する一覧の一意の ID|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## ListCards
ボードのカードの一覧表示: ボードのカードを一覧表示します。

```GET: /boards/{board_id}/cards```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|path|なし|すべてのカードを取得するボードの ID|
|actions|string|×|query|なし|返すアクションの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|attachments|boolean|×|query|なし|添付ファイルを表示するか|
|attachment\_fields|string|×|query|なし|返す添付ファイル フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|stickers|boolean|×|query|なし|ステッカーを表示するか|
|members|boolean|×|query|なし|メンバーを表示するか|
|memeber\_fields|string|×|query|なし|返すメンバー フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|CheckItemStates|boolean|×|query|なし|カードの状態を返すか|
|Checklists|string|×|query|なし|表示チェックリスト|
|limit|integer|×|query|なし|返す結果の最大数。1 ～ 1000|
|since|string|×|query|なし|この日付より後のすべてのカードを取得します|
|準備|string|×|query|なし|この日付より前のすべてのカードを取得します|
|filter|string|×|query|なし|応答をフィルター処理します|
|fields|string|×|query|なし|返すカード フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## GetCard
ID を使用するカードの取得: ID を使用してカードを取得します。

```GET: /cards/{card_id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|query|なし|カードを取得するボードの ID|
|card\_id|string|○|path|なし|取得するカードの ID|
|actions|string|×|query|なし|返すアクションの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|actions\_entities|boolean|×|query|なし|アクション エンティティを返すか|
|actions\_display|boolean|×|query|なし|アクション表示を返すか|
|actions\_limit|integer|×|query|なし|返すアクションの最大数|
|action\_fields|string|×|query|なし|各アクションについて返すアクション フィールドの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|action\_memberCreator\_fields|string|×|query|なし|返すアクション メンバー作成者のフィールドの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|attachments|boolean|×|query|なし|添付ファイルを返すか|
|attachement\_fields|string|×|query|なし|各添付ファイルについて返す添付ファイルのフィールドの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|members|boolean|×|query|なし|メンバーを返すか|
|member\_fields|string|×|query|なし|各メンバーについて対して返すメンバー フィールドの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|membersVoted|boolean|×|query|なし|投票されたメンバーを返すか|
|memberVoted\_fields|string|×|query|なし|投票された各メンバーについて返す投票されたメンバー フィールドの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|checkItemStates|boolean|×|query|なし|カードの状態を返すか|
|checkItemState\_fields|string|×|query|なし|各カード項目の状態について返す状態フィールドの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|checklists|string|×|query|なし|返すチェックリスト|
|checklist\_fields|string|×|query|なし|各チェックリストについて返すチェックリスト フィールドの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|board|boolean|×|query|なし|カードが属するボードを返すか|
|board\_fields|string|×|query|なし|返すボード フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|list|boolean|×|query|なし|カードが属する一覧を返すか|
|list\_fields|string|×|query|なし|返す一覧フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|stickers|boolean|×|query|なし|ステッカーを返すか|
|sticker\_fields|string|×|query|なし|各ステッカーについて返すステッカー フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|fields|string|×|query|なし|返すカード フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## UpdateCard
カードの更新: カードを更新します。

```PUT: /cards/{card_id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|query|なし|カードを取得するボードの ID|
|card\_id|string|○|path|なし|更新するカードの ID|
|updateCard| |○|body|なし|更新されたカード値|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## DeleteCard
カードの削除: カードを削除します。

```DELETE: /cards/{card_id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|query|なし|カードを取得するボードの ID|
|card\_id|string|○|path|なし|削除するカードの ID|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## CreateCard
カードの作成: Trello アカウントに新しいカードを作成します。

```POST: /cards```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|query|なし|カードを作成するボードの一意の ID|
|newCard| |○|body|なし|Trello ボードに追加する新しいカード|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## ListBoards
ボードの一覧表示: ボードを一覧表示します。

```GET: /member/me/boards```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|filter|string|×|query|なし|ボードの結果に適用するフィルターの一覧。'all' または有効な値のコンマ区切りの一覧を指定します|
|fields|string|×|query|なし|返すボード フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|actions|string|×|query|なし|返すアクション フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|actions\_entities|boolean|×|query|なし|アクション エンティティを返すか|
|actions\_limit|integer|×|query|なし|返すアクションの最大数|
|actions\_format|string|×|query|なし|返すアクションの形式を指定します|
|actions\_since|string|×|query|なし|指定した日付より後のアクションを返します|
|action\_fields|string|×|query|なし|返すアクションのフィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|memberships|string|×|query|なし|返すメンバーシップ情報を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|organization|boolean|×|query|なし|組織の情報を返すかどうかを指定します|
|organization\_fields|string|×|query|なし|返す組織フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|lists|string|×|query|なし|ボードに属している一覧を返すかどうかを指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## GetBoard
ID を使用するボードの取得: ID を使用してボードを取得します。

```GET: /boards/{board_id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|path|なし|取得するボードの一意の ID|
|actions|string|×|query|なし|返すアクションの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|action\_entities|boolean|×|query|なし|アクション エンティティを返すかどうかを指定します|
|actions\_display|boolean|×|query|なし|アクション表示を返すかどうかを指定します|
|actions\_format|string|×|query|なし|返すアクションの形式を指定します|
|actions\_since|string|×|query|なし|この日付より後のアクションのみを返します|
|actions\_limit|integer|×|query|なし|返すアクションの最大数|
|action\_fields|string|×|query|なし|各フィールドについて返すフィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|action\_memeber|boolean|×|query|なし|アクション メンバーを返すかどうかを指定します|
|action\_member\_fields|string|×|query|なし|各アクション メンバーについて返すメンバー フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|action\_memberCreator|boolean|×|query|なし|アクション メンバー作成者を返すかどうかを指定します|
|action\_memberCreator\_fields|string|×|query|なし|返すアクション メンバー作成者フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|cards|string|×|query|なし|返すカードを指定します|
|card\_fields|string|×|query|なし|各カードについて返すフィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|card\_attachments|boolean|○|query|なし|カードの添付ファイルを返すかどうかを指定します|
|card\_attachment\_fields|string|×|query|なし|各添付ファイルについて返す添付ファイル フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|card\_checklists|string|×|query|なし|各カードについて返すチェックリストを指定します|
|card\_stickers|boolean|×|query|なし|カード ステッカーを返すかどうかを指定します|
|boardStarts|string|×|query|なし|返すボードの星を指定します|
|ラベル|string|×|query|なし|返すラベルを指定します|
|label\_fields|string|×|query|なし|各カードについて返すラベル フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|labels\_limits|integer|×|query|なし|返すラベルの最大数|
|lists|string|×|query|なし|返す一覧を指定します|
|list\_fields|string|×|query|なし|各一覧について返す一覧フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|memberships|string|×|query|なし|返すメンバーシップの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|memberships\_member|boolean|×|query|なし|メンバーシップ メンバーを返すかどうかを指定します。|
|memberships\_member\_fields|string|×|query|なし|各メンバーシップ メンバーについて返すメンバー フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|members|string|×|query|なし|返すメンバーシップの一覧を指定します|
|member\_fields|string|×|query|なし|各メンバーについて返すメンバー フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|membersInvited|string|×|query|なし|返す招待されたメンバーを指定します|
|membersInvited\_fields|string|×|query|なし|それぞれについて返すフィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|checklists|string|×|query|なし|返すチェックリストを指定します|
|checklist\_fields|string|×|query|なし|各チェックリストについて返すチェックリスト フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|organization|boolean|×|query|なし|組織の情報を返すかどうかを指定します|
|organization\_fields|string|×|query|なし|各組織について返す組織フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|organization\_memberships|string|×|query|なし|返す組織メンバーシップの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|myPerfs|boolean|×|query|なし|マイ パフォーマンスを返すかどうかを指定します|
|fields|string|×|query|なし|返すフィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## ListLists
ボードのカード一覧の一覧表示: ボードのカード一覧を一覧表示します。

```GET: /boards/{board_id}/lists```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|path|なし|一覧を取得するボードの一意の ID|
|cards|string|×|query|なし|返すカードを指定します|
|card\_fields|string|×|query|なし|返すカード フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|filter|string|×|query|なし|一覧のフィルター プロパティを指定します|
|fields|string|×|query|なし|返すフィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## GetList
ID を使用する一覧の取得: ID を使用して一覧を取得します。

```GET: /lists/{list_id}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|board\_id|string|○|query|なし|一覧を取得するボードの一意の ID|
|list\_id|string|○|path|なし|取得する一覧の一意の ID|
|cards|string|×|query|なし|返すカードを指定します|
|card\_fields|string|×|query|なし|各カードについて返すカード フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|board|boolean|×|query|なし|ボード情報を返すかどうかを指定します|
|board\_fields|string|×|query|なし|返すボード フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|
|fields|string|×|query|なし|返す一覧フィールドの一覧を指定します。'all' または有効な値のコンマ区切りの一覧を指定します|

#### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました|


## オブジェクト定義 

### Card


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|いいえ |
|checkItemStates|array|いいえ |
|closed|boolean|いいえ |
|dateLastActivity|string|いいえ |
|desc|string|いいえ |
|idBoard|string|いいえ |
|idList|string|いいえ |
|idMembersVoted|array|いいえ |
|idShort|integer|いいえ |
|idAttachmentCover|string|いいえ |
|manualCoverAttachment|boolean|いいえ |
|idLabels|array|いいえ |
|name|string|いいえ |
|pos|integer|いいえ |
|shortLink|string|いいえ |
|badges|未定義|いいえ |
|due|string|いいえ |
|email|string|いいえ |
|shortUrl|string|いいえ |
|subscribed|boolean|いいえ |
|url|string|いいえ |



### Badges


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|Votes|integer|いいえ |
|ViewingMemberVoted|boolean|いいえ |
|Subscribed|boolean|いいえ |
|Fogbugz|string|いいえ |
|CheckItems|integer|いいえ |
|CheckItemsChecked|integer|いいえ |
|説明|integer|いいえ |
|[添付ファイル]|integer|いいえ |
|説明|boolean|いいえ |
|Due|string|いいえ |



### オブジェクト


| プロパティ名 | データ型 | 必須 |
|---|---|---|



### CreateCard


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|idList|string|あり |
|name|string|はい |
|desc|string|いいえ |
|pos|string|いいえ |
|idMembers|string|いいえ |
|idLabels|string|いいえ |
|urlSource|string|いいえ |
|fileSource|string|いいえ |
|idCardSource|string|いいえ |
|keepFromSource|string|いいえ |



### UpdateCard


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|name|string|いいえ |
|desc|string|いいえ |
|closed|boolean|いいえ |
|idMembers|string|いいえ |
|idAttachmentCover|string|いいえ |
|idList|string|いいえ |
|idBoard|string|いいえ |
|pos|string|いいえ |
|due|string|いいえ |
|subscribed|boolean|いいえ |



### Board


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|いいえ |
|closed|boolean|いいえ |
|dateLastActivity|string|いいえ |
|dateLastView|string|いいえ |
|desc|string|いいえ |
|idOrganization|string|いいえ |
|invitations|array|いいえ |
|invited|boolean|いいえ |
|labelNames|未定義|いいえ |
|memberships|array|いいえ |
|name|string|いいえ |
|pinned|boolean|いいえ |
|powerUps|array|いいえ |
|perfs|未定義|いいえ |
|shortLink|string|いいえ |
|shortUrl|string|いいえ |
|starred|string|いいえ |
|subscribed|string|いいえ |
|url|string|いいえ |



### ラベル


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|green|string|いいえ |
|yellow|string|いいえ |
|orange|string|いいえ |
|red|string|いいえ |
|purple|string|いいえ |
|blue|string|いいえ |
|sky|string|いいえ |
|lime|string|いいえ |
|pink|string|いいえ |
|black|string|いいえ |



### Membership


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|いいえ |
|idMember|string|いいえ |
|memberType|string|いいえ |
|unconfirmed|boolean|いいえ |



### Perfs


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|permissionLevel|string|いいえ |
|voting|string|いいえ |
|コメント|string|いいえ |
|invitations|string|いいえ |
|selfJoin|boolean|いいえ |
|cardCovers|boolean|いいえ |
|calendarFeedEnabled|boolean|いいえ |
|background|string|いいえ |
|backgroundColor|string|いいえ |
|backgroundImage|string|いいえ |
|backgroundImageScaled|string|いいえ |
|backgroundTile|boolean|いいえ |
|backgroundBrightness|string|いいえ |
|canBePublic|boolean|いいえ |
|canBeOrg|boolean|いいえ |
|canBePrivate|boolean|いいえ |
|canInvite|boolean|いいえ |



### 一覧表示


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|id|string|いいえ |
|name|string|いいえ |
|closed|boolean|いいえ |
|idBoard|string|いいえ |
|pos|number|いいえ |
|subscribed|boolean|いいえ |
|cards|array|いいえ |
|board|未定義|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->