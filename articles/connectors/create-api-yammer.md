<properties
pageTitle="ロジック アプリで Yammer API を追加する | Microsoft Azure"
description="Yammer API と REST API パラメーターの概要"
services=""	
documentationCenter="" 	
authors="msftman"	
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
ms.author="deonhe"/>

# Yammer API の使用

Yammer に接続し、エンタープライズ ネットワークの会話にアクセスします。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [Yammer](../app-service-logic/app-service-logic-connector-yammer.md) をクリックしてください。

Yammer で次のことができます。

- Yammer から取得したデータに基づいてビジネス フローを構築します。 
- グループまたはフォローしているフィードで新しいメッセージが発生したときにトリガーを使用します。
- アクションを使用し、メッセージを投稿したり、すべてのメッセージを取得したりします。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、新しいメッセージが表示されたら、Office 365 を使用して電子メールを送信できます。

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
Yammer には、次のトリガーとアクションが含まれています。

トリガー | アクション
--- | ---
<ul><li>グループに新しいメッセージがあるとき</li><li>フォローしているフィードに新しいメッセージがあるとき</li></ul>| <ul><li>すべてのメッセージを取得する</li><li>グループのメッセージを取得する</li><li>自分がフォローしているフィードからメッセージを取得する</li><li>メッセージを投稿する</li><li>グループに新しいメッセージがあるとき</li><li>自分がフォローしているフィードに新しいメッセージがあるとき</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## Yammer への接続を作成する
Yammer API を使用するには、最初に**接続**を作成し、以下のプロパティの詳細を指定します。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|あり|Yammer 資格情報を入力します|

次の手順に従って、Yammer にサインインし、ロジック アプリの Yammer **接続**の構成を完了します。

1. **[繰り返し]** を選択します。
2. **[頻度]** を選択し、**[間隔]** を入力します。
3. **[アクションの追加]** を選択します。![Yammer の構成][1]
4. 検索ボックスに「yammer」と入力し、名前に yammer が含まれたすべてのエントリが返されるまで待ちます。
5. **[Yammer - すべてのメッセージを取得]** を選択します。
6. **[Yammer にサインイン]** をクリックします。![Yammer の構成][2]
7. アプリケーションを承認するために、Yammer 資格情報を入力してサインインします。![Yammer の構成][3]  
8. 組織のログイン ページにリダイレクトされます。Yammer がロジック アプリと対話することを**許可**します。![Yammer の構成][4] 
9. サインインしたら、ロジック アプリに戻ります。**[Yammer - すべてのメッセージを取得]** セクションを構成し、必要な他のトリガーやアクションを追加して、ロジック アプリを完成させます。![Yammer の構成][5]  
10. 上部のメニュー バーの **[保存]** をクリックして、作業内容を保存します。


>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Yammer REST API リファレンス
このドキュメントの対象バージョン: 1.0


### ログイン ユーザーの Yammer ネットワークのすべてのパブリック メッセージを取得します。
Yammer Web インターフェイスの "すべて" の会話に対応します。```GET: /messages.json```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|older\_then|integer|×|query|なし|数値文字列として指定されたメッセージ ID よりも古いメッセージを返します。これはメッセージのページネーションに便利です。たとえば、20 件のメッセージを表示しているとき、最も古いメッセージが 2912 番の場合、「?older\_than=2912」を要求に追加すると、現在表示されているメッセージより前の 20 件が取得されます。|
|newer\_then|integer|×|query|なし|数値文字列として指定されたメッセージ ID よりも新しいメッセージを返します。新しいメッセージのポーリング時にこれを使用します。メッセージを表示しているとき、返された最も新しいメッセージが 3516 番の場合、要求に「?newer\_than=3516」というパラメーターを指定すると、ページに既に表示されているメッセージが重複して取得されることがありません。|
|limit|integer|×|query|なし|指定した数のメッセージのみを返します。|
|page|integer|×|query|なし|指定されたページを取得します。返されたデータが上限を超える場合、このフィールドを使用し、後続のページにアクセスできます。|


### Response

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|408|要求タイムアウト|
|429|要求が多すぎます|
|500|内部サーバー エラー。不明なエラーが発生しました|
|503|Yammer サービス利用不可|
|504|ゲートウェイ タイムアウト|
|default|操作に失敗しました。|


### グループまたはすべての会社フィードにメッセージを投稿する
グループ ID が指定されている場合、メッセージは指定されたグループに投稿されます。指定されていない場合、すべての会社フィードに投稿されます。```POST: /messages.json```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|input| |○|body|なし|メッセージの投稿要求|


### Response

|名前|説明|
|---|---|
|201|作成日時|



## オブジェクト定義

#### メッセージ: Yammer メッセージ

| 名前 | データ型 | 必須 |
|---|---| --- | 
|id|integer|×|
|content\_excerpt|string|×|
|sender\_id|integer|×|
|replied\_to\_id|integer|×|
|created\_at|string|×|
|network\_id|integer|×|
|message\_type|string|×|
|sender\_type|string|×|
|url|string|×|
|web\_url|string|×|
|group\_id|integer|×|
|body|未定義|×|
|thread\_id|integer|×|
|direct\_message|ブール値|×|
|client\_type|string|×|
|client\_url|string|×|
|言語|string|×|
|notified\_user\_ids|array|×|
|privacy|string|×|
|liked\_by|未定義|×|
|system\_message|ブール値|×|

#### PostOperationRequest: Yammer に投稿する Yammer コネクタの投稿要求を表す

| 名前 | データ型 | 必須 |
|---|---| --- | 
|body|string|○|
|group\_id|integer|×|
|replied\_to\_id|integer|×|
|direct\_to\_id|integer|×|
|broadcast|ブール値|×|
|topic1|string|×|
|topic2|string|×|
|topic3|string|×|
|topic4|string|×|
|topic5|string|×|
|topic6|string|×|
|topic7|string|×|
|topic8|string|×|
|topic9|string|×|
|topic10|string|×|
|topic11|string|×|
|topic12|string|×|
|topic13|string|×|
|topic14|string|×|
|topic15|string|×|
|topic16|string|×|
|topic17|string|×|
|topic18|string|×|
|topic19|string|×|
|topic20|string|×|

#### MessageList: メッセージの一覧

| 名前 | データ型 | 必須 |
|---|---| --- | 
|messages|array|×|


#### MessageBody: メッセージの本文

| 名前 | データ型 | 必須 |
|---|---| --- | 
|parsed|string|×|
|plain|string|×|
|rich|string|×|

#### LikedBy: いいねを付けたユーザー

| 名前 | データ型 | 必須 |
|---|---| --- | 
|count|integer|×|
|names|array|×|

#### YammmerEntity: いいねを付けたユーザー

| 名前 | データ型 | 必須 |
|---|---| --- | 
|type|string|×|
|id|integer|×|
|full\_name|string|×|


## 次のステップ
[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

[1]: ./media/create-api-yammer/connectionconfig1.png
[2]: ./media/create-api-yammer/connectionconfig2.png
[3]: ./media/create-api-yammer/connectionconfig3.png
[4]: ./media/create-api-yammer/connectionconfig4.png
[5]: ./media/create-api-yammer/connectionconfig5.png

<!---HONumber=AcomDC_0302_2016-->