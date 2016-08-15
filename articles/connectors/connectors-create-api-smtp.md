<properties
pageTitle="SMTP | Microsoft Azure"
description="Azure App Service を使用してロジック アプリを作成します。SMTP に接続して電子メールを送信します。"
services="logic-apps"	
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
ms.date="07/15/2016"
ms.author="deonhe"/>

# SMTP コネクタの概要

SMTP に接続して電子メールを送信します。

[任意のコネクタ](./apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。ロジック アプリの作成方法については、[こちら](../app-service-logic/app-service-logic-create-a-logic-app.md)をご覧ください。

## SMTP への接続

ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの "接続" を作成する必要があります。[接続](./connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。たとえば、SMTP に接続するには、まず SMTP "接続" が必要です。接続を作成するには、接続先のサービスへのアクセスに通常使用する資格情報を入力する必要があります。そのため、SMTP の例では、SMTP への接続を作成するために、接続名、SMTP サーバーのアドレス、ユーザーのログイン情報に対して資格情報が必要になります。接続の詳細については、[こちら]()をご覧ください。

### SMTP への接続を作成する

>[AZURE.INCLUDE [SMTP への接続を作成する手順](../../includes/connectors-create-api-smtp.md)]

## SMTP トリガーの使用

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。トリガーの詳細については、[こちら](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)をご覧ください。

SMTP には独自のトリガーがないため、この例では **[Salesforce - When an object is created (Salesforce - オブジェクトの作成時)]** トリガーを使用します。このトリガーは、Salesforce で新しいオブジェクトが作成されるとアクティブになります。この例では、Salesforce で新しい潜在顧客が作成されるたびに、新しい潜在顧客の作成を通知する "電子メールの送信" アクションが SMTP コネクタ経由で実行されるようにトリガーを設定します。

1. Logic Apps デザイナーの検索ボックスに「salesforce」と入力し、**[Salesforce - When an object is created (Salesforce - オブジェクトの作成時)]** トリガーを選択します。![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)

2. **[When an object is created (オブジェクトの作成時)]** コントロールが表示されます。![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)

3. **[オブジェクトの種類]** を選択し、オブジェクトの一覧から *[潜在顧客]* を選択します。この手順では、Salesforce で新しい潜在顧客が作成されるたびにロジック アプリに通知するトリガーを作成していることを示しています。![](../../includes/media/connectors-create-api-salesforce/trigger3.png)

4. トリガーが作成されました。![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)

## SMTP アクションの使用

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。アクションの詳細については、[こちら](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)をご覧ください。

トリガーが追加されたら、次の手順に従って、Salesforce で新しい潜在顧客が作成されたときに実行される SMTP アクションを追加します。

1. **[+ 新しいステップ]** をクリックして、新しい潜在顧客が作成されたときに実行するアクションを追加します。![](../../includes/media/connectors-create-api-salesforce/trigger4.png)

2. **[アクションの追加]** を選択します。実行するアクションを検索できる検索ボックスが開きます。![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)

3. 「smtp」と入力して、SMTP に関連するアクションを検索します。

4. 新しい潜在顧客が作成されたときに実行するアクションとして、**[SMTP - Send Email (SMTP - 電子メールの送信)]** を選択します。アクションの制御ブロックが表示されます。これまで SMTP 接続を確立したことがない場合は、このデザイナー ブロックで接続を確立する必要があります。![](../../includes/media/connectors-create-api-smtp/smtp-2.png)

5. **[SMTP - Send Email (SMTP - 電子メールの送信)]** ブロックで、必要な電子メール情報を入力します。![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)

6. ワークフローをアクティブにするために、作業内容を保存します。

## 技術的な詳細

ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## SMTP トリガー

SMTP にはトリガーはありません。

## SMTP アクション

SMTP には次のアクションがあります。


|アクション|説明|
|--- | ---|
|[電子メールを送信する](connectors-create-api-smtp.md#send-email)|この操作では、1 人以上の受信者に電子メールを送信します。|

### アクションの詳細

ここでは、このコネクタのアクションとその応答について詳しく説明します。


### 電子メールを送信する
この操作では、1 人以上の受信者に電子メールを送信します。


|プロパティ名| Displayname Settings|説明|
| ---|---|---|
|To|To|"recipient1@domain.com;recipient2@domain.com" のようにセミコロンで区切って電子メール アドレスを指定|
|CC|cc|"recipient1@domain.com;recipient2@domain.com" のようにセミコロンで区切って電子メール アドレスを指定|
|[件名]|[件名]|電子メールの件名|
|本文|本文|電子メールの本文|
|ファイル|ファイル|差出人の電子メールアドレス (例: sender@domain.com)|
|IsHtml|Is HTML|電子メールを HTML として送信 (true/false)|
|[Bcc]|bcc|"recipient1@domain.com;recipient2@domain.com" のようにセミコロンで区切って電子メール アドレスを指定|
|[重要度]|[重要度]|電子メールの重要度 (高、標準、または低)|
|ContentData|Attachments Content Data (添付ファイルのコンテンツ データ)|コンテンツ データ (ストリームの場合は base64 でエンコード、文字列の場合は現状のまま)|
|ContentType|Attachments Content Type (添付ファイルのコンテンツの種類)|コンテンツの種類|
|ContentTransferEncoding|Attachments Content Transfer Encoding (添付ファイルのコンテンツ転送エンコード)|コンテンツ転送エンコード (base64 またはなし)|
|FileName|Attachments File Name (添付ファイルのファイル名)|ファイル名|
|ContentId|Attachments Content ID (添付ファイルのコンテンツ ID)|コンテンツ ID|

* は、必須のプロパティを示します。


## HTTP 応答

上記のアクションとトリガーは、次の HTTP 状態コードを 1 つ以上返す場合があります。

|名前|説明|
|---|---|
|200|OK|
|202|承認済み|
|400|正しくない要求|
|401|権限がありません|
|403|許可されていません|
|404|見つかりません|
|500|内部サーバー エラー。不明なエラーが発生しました。|
|default|操作に失敗しました。|

## 次のステップ
[ロジック アプリを作成します](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->