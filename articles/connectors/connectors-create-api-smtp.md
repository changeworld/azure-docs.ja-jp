<properties
	pageTitle="Logic Apps で SMTP API を追加する | Microsoft Azure"
	description="SMTP API と REST API パラメーターの概要"
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

# SMTP API を使ってみる
SMTP サーバーに接続して、電子メールを送信します。SMTP API は次のツールから使用できます。

- Logic Apps

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。

SMTP では次の操作を実行できます。

- SMTP を使用した電子メールの送信を含む、ビジネス フローを構築します。 
- アクションを使用して電子メールを送信します。このアクションで応答を取得すると、出力を他のアクションでも使用できます。たとえば、FTP サーバー上に新しいファイルがある場合は、そのファイルを取得し、SMTP を使って添付ファイルとして電子メール送信できます。 

ロジック アプリでの操作の追加については、「[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」を参照してください。

## トリガーとアクション
SMTP API で利用できるアクションは次のとおりです。トリガーはありません。

|トリガー | アクション|
|--- | ---|
|なし | 電子メールの送信|

すべての API は、JSON および XML 形式のデータに対応します。

## SMTP への接続を作成する
この API をロジック アプリに追加するときに、次の値を入力します。

|プロパティ| 必須|説明|
| ---|---|---|
| SMTP Server Name | はい | 完全修飾ドメイン (FQDN) または SMTP サーバーの IP アドレスを入力します。|
| ユーザー名 |はい |ユーザー名を入力して、SMTP サーバーに接続します。 |
| パスワード | あり|ユーザー名のパスワードを入力します。 |

接続を作成したら、To、CC の値など、SMTP プロパティを入力します。これらのプロパティについては、このトピックの **REST API リファレンス**を参照してください。

>[AZURE.TIP] 他のロジック アプリでも、この同じ SMTP 接続を使用できます。

## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### 電子メールを送信する
電子メールを 1 人以上の受信者に送信します。```POST: /SendEmail```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|emailMessage| 多数|○|body|なし |電子メール メッセージ|

## オブジェクト定義

#### 電子メール: SMTP 電子メール

| 名前 | データ型 | 必須|
|---|---|---|
|To|string|×|
|CC|string|×|
|[件名]|string|×|
|本文|string|×|
|ファイル|string|×|
|IsHtml|ブール値|×|
|[Bcc]|string|×|
|[重要度]|string|×|
|[添付ファイル]|array|×|


#### 添付ファイル: 電子メールの添付ファイル

| 名前 | データ型 |必須|
|---|---|---|
|FileName|string|×|
|ContentId|string|×|
|ContentData|string|○|
|ContentType|string|○|
|ContentTransferEncoding|string|○|


## 次のステップ
[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。

<!---HONumber=AcomDC_0413_2016-->