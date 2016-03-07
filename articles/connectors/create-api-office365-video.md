<properties
pageTitle="ロジック アプリで Office 365 Video API を使用する | Microsoft Azure"
description="Microsoft Azure App Service のロジック アプリで Office 365 Video API (コネクタ) を使用する方法の概要について説明します"
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
ms.date="02/18/2016"
ms.author="deonhe"/>

# Office 365 Video API の概要

Office 365 Video API には、Office 365 のチャネルとビデオを操作する API があります。

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、こちらの [Office 365 Video コネクタ](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md) をクリックしてください。

Office 365 Video では、次の操作を実行できます。

* ロジック アプリを構築できます

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション

Office 365 Video API はアクションとして使用できます。トリガーはありません。すべての API は、JSON および XML 形式のデータに対応します。

 Office 365 Video API では、次のアクションやトリガーを使用できます。

### Office365 Video API のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|IsVideoPortalEnabled|ビデオ ポータルの状態で、ビデオ サービスが有効かどうかを確認します|
|ListViewableChannels|ユーザーが表示アクセス権を持っているすべてのチャネルを取得します|
|ListVideos|チャネル内にあるすべての Office 365 ビデオを一覧表示します|
|GetVideo|特定の Office 365 ビデオの情報を取得します|
|GetPlaybackUrl|ビデオの Azure Media Services マニフェストの再生 URL を取得します|
|GetStreamingKeyAccessToken|ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます|
ActionsTableReplaceMeLater## Office 365 Video API への接続を作成します。Office 365 Video API を使用するには、まず**接続**を作成してから、次のプロパティの詳細を指定します。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|あり|SharePoint Online の資格情報を指定する|


>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## Office365 Video REST API リファレンス
#### このドキュメントの対象バージョン: 1.0


### ビデオ ポータルの状態で、ビデオ サービスが有効かどうかを確認します
**```GET: /{tenant}/IsEnabled```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|


### 次のような応答があります。

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### ユーザーが表示アクセス権を持っているすべてのチャネルを取得します
**```GET: /{tenant}/Channels```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|


### 次のような応答があります。

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### チャネル内にあるすべての Office 365 ビデオを一覧表示します
**```GET: /{tenant}/Channels/{channelId}/Videos```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|ビデオを取得するチャネルの ID|


### 次のような応答があります。

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### 特定の Office 365 ビデオの情報を取得します
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|チャネル ID|
|videoId|string|○|path|なし|ビデオ ID|


### 次のような応答があります。

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### ビデオの Azure Media Services マニフェストの再生 URL を取得します
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|チャネル ID|
|videoId|string|○|path|なし|ビデオ ID|
|streamingFormatType|string|○|query|なし|ストリーミング形式の種類。1 - Smooth Streaming または MPEG-DASH。0 - HLS Streaming|


### 次のような応答があります。

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



### ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**



| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|チャネル ID|
|videoId|string|○|path|なし|ビデオ ID|


### 次のような応答があります。

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|
------



## オブジェクト定義: 

 **Channel**: Channel クラス

Channel の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|ID|string|
|タイトル|string|
|説明|string|



 **Video**: Video クラス

Video の必須プロパティ:


必須のプロパティはありません。


**すべてのプロパティ**:


| 名前 | データ型 |
|---|---|
|ID|string|
|タイトル|string|
|説明|string|
|CreationDate|string|
|Owner|string|
|ThumbnailUrl|string|
|VideoUrl|string|
|VideoDuration|integer|
|VideoProcessingStatus|integer|
|ViewCount|integer|


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0224_2016-->