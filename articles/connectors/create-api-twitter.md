<properties
	pageTitle="PowerApps Enterprise とロジック アプリで Twitter API を追加する | Microsoft Azure"
	description="Twitter API と REST API パラメーターの概要"
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
   ms.date="02/25/2016"
   ms.author="mandia"/>


# Twitter API の使用
Twitter に接続し、ツイートを投稿したり、ユーザーのタイムラインを取得したりします。Twitter API は次のツールから使用できます。

- PowerApps 
- Logic Apps 

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、「[Twitter コネクタの使用開始とロジック アプリへの追加](../app-service-logic/app-service-logic-connector-twitter.md)」をご覧ください。

Twitter で次のことができます。

- Twitter から取得したデータに基づいてビジネス フローを構築します。 
- 新しいツイートがあるときのトリガーを利用します。
- アクションを使用し、ツイートを投稿したり、検索したりします。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、新しいツイートが表示されたら、Facebook にそのツイートを投稿できます。
- PowerApps Enterprise に Twitter API を追加します。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」をご覧ください。

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」をご覧ください。


## トリガーとアクション
Twitter には、次のトリガーとアクションが含まれています。

トリガー | アクション
--- | ---
<ul><li>新しいツイートが表示されたとき</li></ul>| <ul><li>新しいツイートを投稿する</li><li>新しいツイートが表示されたとき</li><li>ホーム タイムラインを取得する</li><li>ユーザーを取得する</li><li>ユーザーのタイムラインを取得する</li><li>ツイートを検索する</li><li>フォロワーを取得する</li><li>自分のフォロワーを取得する</li><li>フォローを取得する</li><li>自分のフォローを取得する</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。


## Twitter への接続を作成する

### PowerApps に構成を追加する
Twitter を PowerApps Enterprise に追加するときに、Twitter アプリケーションの**コンシューマー キー**と**コンシューマー シークレット**の値を入力します。Twitter アプリケーションでは、**リダイレクト URL** 値も使用されます。Twitter アプリケーションがない場合、次の手順でアプリケーションを作成できます。

1. [Twitter](https://apps.twitter.com) にサインインします。

2. **[Create New App]** を選択します。 ![Twitter アプリ ページ][6]

3. **[Create an application]** では次のようにします。
   
	1. **[Name]**、**[Description]**、**[Website]** に必要な値を入力します。
	2. **[Callback url]** に、Azure ポータルで Twitter API を追加したときに表示される**リダイレクト URL** 値を入力します。
	5. 規約に同意し、**Twitter アプリケーションを作成**します。  

	![Twitter アプリの作成][7]

**コンシューマー キー**値と**コンシューマー シークレット**値をコピーして、Azure ポータルの Twitter 構成に貼り付けます。


### ロジック アプリに構成を追加する
ロジック アプリにこの API を追加するとき、Twitter アカウントに接続するロジック アプリを承認する必要があります。

1. Twitter アカウントにサインインします。
2. **[Authorize]** を選択し、ロジック アプリが Twitter アカウントに接続して使用することを許可します。 

接続を作成したら、ツイートのテキストなど、Twitter プロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**をご覧ください。

>[AZURE.TIP] 他のロジック アプリでこの同じ Twitter 接続を使用できます。


## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### 新しいツイートを投稿する 
ツイート。```POST: /posttweet```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|tweetText|string|×|query|なし|投稿するテキスト|
|body| 文字列 (バイナリ) |×|body|なし|投稿するメディア|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### 新しいツイートが表示されたとき 
検索クエリに一致する新しいツイートが投稿されたときにワークフローをトリガーします。```GET: /onnewtweet```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|searchQuery|string|○|query|なし|クエリ テキスト (Twitter でサポートされているクエリ演算子を使用できます。http://www.twitter.com/search)|

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


### ホーム タイムラインを取得する 
自分と自分のフォロワーが投稿した最近のツイートとリツイートを取得します。```GET: /hometimeline```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|maxResults|integer|×|query|20|取得するツイートの最大数|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### ユーザーを取得する 
指定されたユーザーに関する詳細情報を取得します (例: ユーザー名、説明、フォロワー数など)。```GET: /user```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userName|string|○|query|なし|ユーザーの Twitter ハンドル|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### ユーザーのタイムラインを取得する 
指定したユーザーが投稿した最近のツイートをまとめて取得します。```GET: /usertimeline```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userName|string|○|query|なし|Twitter ハンドル|
|maxResults|integer|×|query|20|取得するツイートの最大数|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### ツイートを検索する 
指定されたクエリに一致する関連するツイートをまとめて取得します。```GET: /searchtweets```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|searchQuery|string|○|query|なし|クエリ テキスト (Twitter でサポートされているクエリ演算子を使用できます。http://www.twitter.com/search)|
|maxResults|integer|×|query|20|取得するツイートの最大数|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### フォロワーを取得する 
指定されたユーザーをフォローしているユーザーを取得します。```GET: /followers```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userName|string|○|query|なし|ユーザーの Twitter ハンドル|
|maxResults|integer|×|query|20|取得するユーザーの最大数|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### 自分のフォロワーを取得する 
自分をフォローしているユーザーを取得します。```GET: /myfollowers```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|maxResults|integer|×|query|20|取得するユーザーの最大数|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### フォローを取得する 
指定されたユーザーがフォローしているユーザーを取得します。```GET: /friends```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|userName|string|○|query|なし|ユーザーの Twitter ハンドル|
|maxResults|integer|×|query|20|取得するユーザーの最大数|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


### 自分のフォローを取得する 
自分がフォローしているユーザーを取得します。```GET: /myfriends```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|maxResults|integer|×|query|20|取得するユーザーの最大数|

#### Response
|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました|
|default|操作に失敗しました。|


## オブジェクト定義

### TweetModel: ツイート オブジェクトを表す

| プロパティ名 | データ型 | 必須 |
|---|---| --- | 
|TweetText|string|○|
|TweetId|string|×|
|CreatedAt|string|×|
|RetweetCount|integer|○|
|TweetedBy|string|○|
|MediaUrls|array|×|

### UserDetailsModel: Twitter ユーザーの詳細を表す

|プロパティ名 | データ型 | 必須 |
|---|---|---|
|FullName|string|○|
|Location (場所)|string|○|
|ID|integer|×|
|UserName|string|○|
|FollowersCount|integer|×|
|説明|string|○|
|StatusesCount|integer|×|
|FriendsCount|integer|×|

### TweetResponseModel: 投稿されたツイートを表すモデル

| 名前 | データ型 | 必須 |
|---|---|---|
|TweetId|string|○|

### TriggerBatchResponse[TweetModel]

|プロパティ名 | データ型 |必須 |
|---|---|---|
|値|array|×|


## 次のステップ
Twitter API を PowerApps Enterprise に追加したら、この API をアプリで使用するための[アクセス許可をユーザーに付与](../power-apps/powerapps-manage-api-connection-user-access.md)します。

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

<!--References-->

[6]: ./media/create-api-twitter/twitter-apps-page.png
[7]: ./media/create-api-twitter/twitter-app-create.png

<!---HONumber=AcomDC_0302_2016-->