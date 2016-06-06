<properties
pageTitle="ロジック アプリで Slack コネクタを使用する | Microsoft Azure"
description="Microsoft Azure App Service Logic Apps で Slack コネクタを使用する方法の概要について説明します"
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# Slack コネクタの使用

Slack はチーム コミュニケーション ツールです。チームのすべてのコミュニケーションが 1 個所にまとめられ、どこにいてもすぐに検索、使用できるようになります。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

Slack コネクタでは、次の操作を実行できます。

* ロジック アプリを構築できます

ロジック アプリに操作を追加する方法については、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関するページをご覧ください。

## トリガーとアクション

Slack コネクタは、アクションとして使用できます。トリガーはありません。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 Slack コネクタでは、次のアクションやトリガーを使用できます。

### Slack のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|PostMessage|指定したチャネルにメッセージを投稿します。|
## Slack への接続を作成する
Slack コネクタを使用するには、最初に**接続**を作成し、以下のプロパティの詳細を指定します。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|あり|Slack の資格情報を指定します|

次の手順に従って、Slack にサインインし、ロジック アプリの Slack **接続**の構成を完了します。

1. **[繰り返し]** を選択します。
2. **[頻度]** を選択し、**[間隔]** を入力します。
3. **[アクションの追加]** を選択します。![Slack の構成][1]  
4. 検索ボックスに「Slack」と入力し、名前に Slack が含まれたすべてのエントリが返されるまで待ちます。
5. **[Slack - メッセージの投稿]** を選択します。
6. **[Slack にサインイン]** をクリックします。![Slack の構成][2]
7. アプリケーションを承認するために、Slack 資格情報を入力してサインインします。![Slack の構成][3]  
8. 組織のログイン ページにリダイレクトされます。Slack がロジック アプリと対話することを**承認**します。![Slack の構成][5] 
9. 承認が完了すると、ロジック アプリにリダイレクトされます。**[Slack - すべてのメッセージを取得]** セクションを構成して、ロジック アプリを完成させます。必要な他のトリガーやアクションを追加します。![Slack の構成][6]
10. メニュー バー上の **[保存]** を選択して、作業内容を保存します。


>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Slack REST API リファレンス
#### このドキュメントの対象バージョン: 1.0


### 指定したチャネルにメッセージを投稿します。
**```POST: /chat.postMessage```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|channel|string|○|query|なし|メッセージを送信するチャネル、プライベート グループ、IM チャネル。名前 (例: #general) またはエンコードされた ID を指定できます。|
|text|string|○|query|なし|送信するメッセージのテキスト。書式設定オプションについては、https://api.slack.com/docs/formatting.| をご覧ください。
|username|string|×|query|なし|ボットの名前|
|as\_user|ブール値|×|query|なし|ボットとしてではなく、認証されたユーザーとして、メッセージを投稿するには、true を渡します|
|parse|string|×|query|なし|メッセージの処理方法を変更します。詳細については、https://api.slack.com/docs/formatting.| をご覧ください。
|link\_names|integer|×|query|なし|チャネル名とユーザー名を検索してリンクします。|
|unfurl\_links|ブール値|×|query|なし|主にテキストベースのコンテンツの展開を有効にするには、true を渡します。|
|unfurl\_media|ブール値|×|query|なし|メディア コンテンツの展開を無効にするには、false を渡します。|
|icon\_url|string|×|query|なし|このメッセージのアイコンとして使用する画像の URL|
|icon\_emoji|string|×|query|なし|このメッセージのアイコンとして使用する絵文字|


### 次のような応答があります。

|名前|説明|
|---|---|
|200|OK|
|400|正しくない要求|
|408|要求タイムアウト|
|429|要求が多すぎます|
|500|内部サーバー エラー。不明なエラーが発生しました|
|503|Slack サービス利用不可|
|504|ゲートウェイ タイムアウト|
|default|操作に失敗しました。|
------



## オブジェクト定義: 

 **Message**: Slack メッセージ

Message の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|id|integer|
|content\_excerpt|string|
|sender\_id|integer|
|replied\_to\_id|integer|
|created\_at|string|
|network\_id|integer|
|message\_type|string|
|sender\_type|string|
|url|string|
|web\_url|string|
|group\_id|integer|
|body|未定義|
|thread\_id|integer|
|direct\_message|ブール値|
|client\_type|string|
|client\_url|string|
|言語|string|
|notified\_user\_ids|array|
|privacy|string|
|liked\_by|未定義|
|system\_message|ブール値|



 **PostOperationRequest**: Slack に投稿する Slack コネクタの post 要求を表します。

PostOperationRequest の必須プロパティ:

body

**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|body|string|
|group\_id|integer|
|replied\_to\_id|integer|
|direct\_to\_id|integer|
|broadcast|ブール値|
|topic1|string|
|topic2|string|
|topic3|string|
|topic4|string|
|topic5|string|
|topic6|string|
|topic7|string|
|topic8|string|
|topic9|string|
|topic10|string|
|topic11|string|
|topic12|string|
|topic13|string|
|topic14|string|
|topic15|string|
|topic16|string|
|topic17|string|
|topic18|string|
|topic19|string|
|topic20|string|



 **MessageList**: メッセージの一覧

MessageList の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|messages|array|



 **MessageBody**: メッセージ本文

MessageBody の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|parsed|string|
|plain|string|
|rich|string|



 **LikedBy**: いいねを付けたユーザー

LikedBy の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|count|integer|
|names|array|



 **YammmerEntity**: いいねを付けたユーザー

YammmerEntity の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|type|string|
|id|integer|
|full\_name|string|


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)
## オブジェクト定義: 

 **WebResultModel**: Bing の Web 検索結果

WebResultModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|タイトル|string|
|説明|string|
|DisplayUrl|string|
|ID|string|
|FullUrl|string|



 **VideoResultModel**: Bing の動画検索結果

VideoResultModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|タイトル|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|ランタイム|integer|
|サムネイル|未定義|



 **ThumbnailModel**: マルチメディア要素のサムネイルのプロパティ

ThumbnailModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|MediaUrl|string|
|ContentType|string|
|幅|integer|
|高さ|integer|
|FileSize|integer|



 **ImageResultModel**: Bing の画像検索結果

ImageResultModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|タイトル|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|SourceUrl|string|
|サムネイル|未定義|



 **NewsResultModel**: Bing のニュース検索結果

NewsResultModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|タイトル|string|
|説明|string|
|DisplayUrl|string|
|ID|string|
|から|string|
|Date|string|



 **SpellResultModel**: Bing の検索候補の結果

SpellResultModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|ID|string|
|値|string|



 **RelatedSearchResultModel**: Bing の関連項目の検索結果

RelatedSearchResultModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|タイトル|string|
|ID|string|
|BingUrl|string|



 **CompositeSearchResultModel**: Bing の複合検索結果

CompositeSearchResultModel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|WebResultsTotal|integer|
|ImageResultsTotal|integer|
|VideoResultsTotal|integer|
|NewsResultsTotal|integer|
|SpellSuggestionsTotal|integer|
|WebResults|array|
|ImageResults|array|
|VideoResults|array|
|NewsResults|array|
|SpellSuggestionResults|array|
|RelatedSearchResults|array|


## オブジェクト定義: 

 **PostOperationResponse**: Slack への投稿に対する Slack コネクタの post 操作の応答を表します。

PostOperationResponse の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|OK|ブール値|
|channel|string|
|ts|string|
|message|未定義|
|error|string|



 **MessageItem**: チャネル メッセージ。

MessageItem の必須のプロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|text|string|
|id|string|
|ユーザー|string|
|created|integer|
|is\_user-deleted|ブール値|


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

<!---HONumber=AcomDC_0525_2016-->