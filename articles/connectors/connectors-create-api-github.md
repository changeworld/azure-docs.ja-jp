<properties
pageTitle="GitHub | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。GitHub は、 web ベースの Git リポジトリホスティングサービスです。独自の機能を追加するだけでなく、Git の分散リビジョン コントロール機能とソース コード管理 (SCM) 機能をすべて提供します。"
services="app-servicelogic"	
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
ms.date="05/17/2016"
ms.author="deonhe"/>

# GitHub コネクタの使用



GitHub コネクタは、次のツールから使用できます。

- [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md)
- [PowerApps](http://powerapps.microsoft.com)
- [フロー](http://flows.microsoft.com)

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事をご覧ください。

## トリガーとアクション

GitHub コネクタは、アクションとして使用できます。GitHub コネクタにはトリガーがあります。すべてのコネクタは、JSON および XML 形式のデータに対応します。

 GitHub コネクタでは、次のアクションやトリガーを使用できます。

### GitHub のアクション
実行できるアクションは以下のとおりです。

|アクション|説明|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|問題を作成する|
### GitHub のトリガー
次のイベントをリッスンできます。

|トリガー | 説明|
|--- | ---|
|問題が開かれた場合|問題が開かれます|
|問題が閉じられた場合|問題が閉じられます|
|問題が割当てられた場合|問題が割当てられます|


## GitHub への接続を作成する
GitHub を使用してロジック アプリを作成するには、まず**接続**を作成し、次のプロパティの詳細を指定する必要があります。

|プロパティ| 必須|説明|
| ---|---|---|
|トークン|あり|GitHub の資格情報を提供します|
接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

>[AZURE.INCLUDE [GitHub への接続を作成する手順](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] 他のロジック アプリでもこの接続を使用できます。

## GitHub のリファレンス
適用されるバージョン: 1.0

## CreateIssue
問題の作成: 問題を作成します

```POST: /repos/{repositoryOwner}/{repositoryName}/issues```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|repositoryOwner|string|○|path|なし|リポジトリの所有者です|
|repositoryName|string|○|path|なし|リポジトリ名です|
|issueBasicDetails| |○|body|なし|問題の詳細情報です|

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


## IssueOpened
問題が開かれた場合: 問題が開かれます

```GET: /trigger/issueOpened```

この呼び出しには、パラメーターはありません
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


## IssueClosed
問題が閉じられた場合: 問題が閉じられます

```GET: /trigger/issueClosed```

この呼び出しには、パラメーターはありません
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


## IssueAssigned
問題が割り当てられた場合: 問題が割り当てられます

```GET: /trigger/issueAssigned```

この呼び出しには、パラメーターはありません
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

### IssueBasicDetailsModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|title|string|あり |
|body|string|あり |
|assignee|string|はい |



### IssueDetailsModel


| プロパティ名 | データ型 | 必須 |
|---|---|---|
|title|string|あり |
|body|string|あり |
|assignee|string|あり |
|number|string|いいえ |
|state|string|いいえ |
|created\_at|string|いいえ |
|repository\_url|string|いいえ |


## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->