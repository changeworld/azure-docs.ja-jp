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
ms.date="02/25/2016"
ms.author="deonhe"/>

# Office 365 Video API の概要
Office 365 ビデオに関する情報の取得、ビデオの一覧の取得などを行うには、Office 365 ビデオに接続します。Office 365 Video API は、以下のツールから使用できます。

- ロジック アプリ 

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。この API は、以前のスキーマ バージョンではサポートされていません。

Office 365 ビデオは、次のことを行えます。

- Office 365 ビデオから取得したデータに基づいてビジネス フローを構築できます。 
- ビデオ ポータルの状態の確認、チャネル内にあるすべてのビデオの一覧の取得などのアクションを使用できます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、Bing Search API を使用して Office 365 ビデオを検索でき、Office 365 Video API を使用してそのビデオに関する情報を取得できます。ビデオが要件を満たしている場合は、Facebook にこのビデオを投稿できます。 

ロジック アプリに操作を追加する方法については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション

Office 365 Users API では、次のアクションを使用できます。トリガーはありません。

| トリガー | アクション|
| --- | --- |
| なし | <ul><li>ビデオ ポータルの状態を確認します</li><li>表示可能なすべてのチャネルを取得します</li><li>ビデオの Azure Media Services マニフェストの再生の URL を取得します</li><li>ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます</li><li>特定の Office 365 ビデオを取得します</li><li>チャネル内にあるすべての Office 365 ビデオを一覧表示します</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## Office 365 Video API への接続を作成する
この API をロジック アプリに追加する場合は、Office 365 ビデオ アカウントにサインインして、ロジック アプリでアカウントに接続できるようにする必要があります。

1. Office 365 ビデオ アカウントにサインインします。
2. ロジック アプリが Office 365 アカウントに接続して使用することを許可します。 

接続を作成したら、テナント名またはチャネル ID など、Office 365 ビデオ プロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**を参照してください。

>[AZURE.TIP] 他のロジック アプリでも、前述の Office 365 ビデオ接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### ビデオ ポータルの状態を確認します 
ビデオ ポータルの状態で、ビデオ サービスが有効かどうかを確認します。```GET: /{tenant}/IsEnabled```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|



### 表示可能なすべてのチャネルを取得します 
ユーザーが表示アクセス権を持っているすべてのチャネルを取得します。```GET: /{tenant}/Channels```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|




### チャネル内にあるすべての Office 365 ビデオを一覧表示します 
チャネル内にあるすべての Office 365 ビデオを一覧表示します。```GET: /{tenant}/Channels/{channelId}/Videos```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|ビデオを取得するチャネルの ID|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|




### 特定の Office 365 ビデオの情報を取得します 
特定の Office 365 ビデオの情報を取得します。```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|チャネル ID|
|videoId|string|○|path|なし|ビデオ ID|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|




### ビデオの Azure Media Services マニフェストの再生 URL を取得します 
ビデオの Azure Media Services マニフェストの再生 URL を取得します。```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|チャネル ID|
|videoId|string|○|path|なし|ビデオ ID|
|streamingFormatType|string|○|query|なし|ストリーミング形式の種類。1 - Smooth Streaming または MPEG-DASH。0 - HLS Streaming|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|




### ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます 
ベアラー トークンを取得して、ビデオを復号化するアクセス権を得ます。```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|テナント|string|○|path|なし|ユーザーが属するディレクトリのテナント名|
|channelId|string|○|path|なし|チャネル ID|
|videoId|string|○|path|なし|ビデオ ID|


#### Response

|名前|説明|
|---|---|
|200|操作に成功しました|
|400|BadRequest|
|401|権限がありません|
|404|見つかりません|
|500|内部サーバー エラー|
|default|操作に失敗しました。|


## オブジェクト定義

#### チャネル: チャネル クラス

| 名前 | データ型 | 必須|
|---|---|---|
|ID|string|×|
|タイトル|string|×|
|説明|string|×|


#### ビデオ 

| 名前 | データ型 |必須|
|---|---|---|
|ID|string|×|
|タイトル|string|×|
|説明|string|×|
|CreationDate|string|×|
|所有者|string|×|
|ThumbnailUrl|string|×|
|VideoUrl|string|×|
|VideoDuration|整数|×|
|VideoProcessingStatus|整数|×|
|ViewCount|整数|×|


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)。

<!---HONumber=AcomDC_0323_2016-->