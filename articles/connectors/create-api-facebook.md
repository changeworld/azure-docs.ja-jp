<properties
	pageTitle="ロジック アプリに Facebook API を追加する | Microsoft Azure"
	description="Facebook API と REST API パラメーターの概要"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/23/2016"
   ms.author="mandia"/>

# Facebook API の概要
Facebook に接続し、タイムラインへの投稿、ページ フィードの取得などを行います。

Facebook API は、ロジック アプリから使用できます。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [Facebook コネクタ](../app-service-logic/app-service-logic-connector-facebook.md)をクリックしてください。


Facebook では、次の操作を実行できます。

- Facebook から取得したデータに基づいてビジネス フローを構築できます。 
- 新しい投稿を取得したときにトリガーを使用できます。
- タイムラインへの投稿、ページ フィードの取得などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、タイムラインに新しい投稿がある場合、その投稿を取得して、Twitter フィードにプッシュすることができます。 

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
Facebook API には、次のトリガーとアクションがあります。

| トリガー | アクション|
| --- | --- |
| <ul><li>タイムラインに新しい投稿がある場合</li></ul> |<ul><li>タイムラインからフィードを取得する</li><li>タイムラインに投稿する</li><li>タイムラインに新しい投稿がある場合</li><li>ページ フィードを取得する</li><li>ユーザーのタイムラインを取得する</li><li>ページに投稿する</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## Facebook への接続を作成する
ロジック アプリにこの API を追加するとき、Facebook に接続するロジック アプリを承認する必要があります。

1. Facebook アカウントにサインインします。
2. **[Authorize]** を選択して、ロジック アプリが Facebook に接続して使用することを許可します。 

接続を作成したら、Facebook のプロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**を参照してください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ Facebook 接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### タイムラインからフィードを取得する
ログインしたユーザーのタイムラインからフィードを取得します。```GET: /me/feed```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|fields|string|×|query|なし |取得フィールドを指定します。例 (id、name、picture)。|
|limit|integer|×|query| なし|取得する投稿の最大数|
|を以下に置き換えることができます。|string|×|query| なし|投稿の一覧を、接続している場所の投稿に制限します。|
|filter|string|×|query| なし|特定のストリーム フィルターに一致する投稿のみを取得します。|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### タイムラインに投稿する
ログインしたユーザーのタイムラインにステータス メッセージを投稿します。```POST: /me/feed```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|post|string |○|body|なし |投稿する新しいメッセージ|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### タイムラインに新しい投稿がある場合
ログインしているユーザーのタイムラインに新しい投稿があると、新しいフローをトリガーします。```GET: /trigger/me/feed```

パラメーターはありません。

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### ページ フィードを取得する
指定したページのフィードから投稿を取得します。```GET: /{pageId}/feed```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|pageId|string|○|path| なし|取得する投稿のページ ID。|
|limit|integer|×|query| なし|取得する投稿の最大数|
|include\_hidden|ブール値|×|query|なし |ページで非表示にされていた投稿を含めるかどうか|
|fields|string|×|query|なし |取得フィールドを指定します。例 (id、name、picture)。|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### ユーザーのタイムラインを取得する
ユーザーのタイムラインから投稿を取得します。```GET: /{userId}/feed```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userId|string|○|path|なし |タイムラインを取得するユーザーの ID。|
|limit|integer|×|query|なし |取得する投稿の最大数|
|を以下に置き換えることができます。|string|×|query|なし |投稿の一覧を、接続している場所の投稿に制限します。|
|filter|string|×|query| なし|特定のストリーム フィルターに一致する投稿のみを取得します。|
|fields|string|×|query| なし|取得フィールドを指定します。例 (id、name、picture)。|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


### ページに投稿する
ログインしたユーザーとして Facebook ページにメッセージを投稿します。```POST: /{pageId}/feed```

| 名前|データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|pageId|string|○|path|なし |投稿するページの ID。|
|post|many |○|body|なし |投稿する新しいメッセージ。|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## オブジェクト定義

#### GetFeedResponse

|プロパティ名 | データ型 | 必須|
|---|---|---|
|data|array|×|

#### TriggerFeedResponse

|プロパティ名 | データ型 |必須|
|---|---|---|
|data|array|×|

#### PostItem: プロファイルのフィードの単一エントリ
プロファイルには、user、page、app、または group を指定できます。

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|×|
|admin\_creator|array|×|
|caption|string|×|
|created\_time|string|×|
|description|string|×|
|feed\_targeting|未定義|×|
|from|未定義|×|
|icon|string|×|
|is\_hidden|ブール値|×|
|is\_published|ブール値|×|
|link|string|×|
|message|string|×|
|name|string|×|
|object\_id|string|×|
|picture|string|×|
|place|未定義|×|
|privacy|未定義|×|
|properties|array|×|
|source|string|×|
|status\_type|string|×|
|story|string|×|
|targeting|未定義|×|
|to|array|×|
|type|string|×|
|updated\_time|string|×|
|with\_tags|未定義|×|

#### TriggerItem: プロファイルのフィードの単一エントリ
プロファイルには、user、page、app、または group を指定できます。

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|×|
|created\_time|string|×|
|from|未定義|×|
|message|string|×|
|type|string|×|

#### AdminItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|×|
|link|string|×|

#### PropertyItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|name|string|×|
|text|string|×|
|href|string|×|

#### UserPostFeedRequest

|プロパティ名 | データ型 |必須|
|---|---|---|
|message|string|○|
|link|string|×|
|picture|string|×|
|name|string|×|
|caption|string|×|
|description|string|×|
|place|string|×|
|tags|string|×|
|privacy|未定義|×|
|object\_attachment|string|×|

#### PagePostFeedRequest

|プロパティ名 | データ型 |必須|
|---|---|---|
|message|string|○|
|link|string|×|
|picture|string|×|
|name|string|×|
|caption|string|×|
|description|string|×|
|actions|array|×|
|place|string|×|
|tags|string|×|
|object\_attachment|string|×|
|targeting|未定義|×|
|feed\_targeting|未定義|×|
|published|ブール値|×|
|scheduled\_publish\_time|string|×|
|backdated\_time|string|×|
|backdated\_time\_granularity|string|×|
|child\_attachments|array|×|
|multi\_share\_end\_card|ブール値|×|

#### PostFeedResponse

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|×|

#### ProfileCollection

|プロパティ名 | データ型 |必須|
|---|---|---|
|data|array|×|

#### UserItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|×|
|first\_name|string|×|
|last\_name|string|×|
|name|string|×|
|gender|string|×|
|about|string|×|

#### ActionItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|name|string|×|
|link|string|×|

#### TargetItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|countries|array|×|
|locales|array|×|
|regions|array|×|
|cities|array|×|

#### FeedTargetItem: この投稿を対象とするニュース フィードを制御するオブジェクト
これらのグループのメンバーはこの投稿を表示する可能性が高く、他のユーザーは可能性が低い場合です。ページにのみ適用されます。

|プロパティ名 | データ型 |必須|
|---|---|---|
|countries|array|×|
|regions|array|×|
|cities|array|×|
|age\_min|integer|×|
|age\_max|integer|×|
|genders|array|×|
|relationship\_statuses|array|×|
|interested\_in|array|×|
|college\_years|array|×|
|interests|array|×|
|relevant\_until|integer|×|
|education\_statuses|array|×|
|locales|array|×|

#### PlaceItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|×|
|name|string|×|
|overall\_rating|number|×|
|location|未定義|×|

#### LocationItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|city|string|×|
|country|string|×|
|latitude|number|×|
|located\_in|string|×|
|longitude|number|×|
|name|string|×|
|region|string|×|
|state|string|×|
|street|string|×|
|zip|string|×|

#### PrivacyItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|description|string|×|
|値|string|○|
|allow|string|×|
|deny|string|×|
|friends|string|×|

#### ChildAttachmentsItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|link|string|×|
|picture|string|×|
|image\_hash|string|×|
|name|string|×|
|description|string|×|

#### PostPhotoRequest

|プロパティ名 | データ型 |必須|
|---|---|---|
|url|string|○|
|caption|string|×|

#### PostPhotoResponse

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|○|
|post\_id|string|○|

#### PostVideoRequest

|プロパティ名 | データ型 |必須|
|---|---|---|
|videoData|string|○|
|description|string|○|
|title|string|○|
|uploadedVideoName|string|×|

#### GetPhotoResponse

|プロパティ名 | データ型 |必須|
|---|---|---|
|data|未定義|○|

#### GetPhotoResponseItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|url|string|○|
|is\_silhouette|ブール値|○|
|height|string|×|
|width|string|×|

#### GetEventResponse

|プロパティ名 | データ型 |必須|
|---|---|---|
|data|array|○|

#### GetEventResponseItem

|プロパティ名 | データ型 |必須|
|---|---|---|
|id|string|○|
|name|string|○|
|start\_time|string|×|
|end\_time|string|×|
|timezone|string|×|
|location|string|×|
|description|string|×|
|ticket\_uri|string|×|
|rsvp\_status|string|○|


## 次のステップ

[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0224_2016-->