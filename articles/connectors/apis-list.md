<properties
	pageTitle="マネージ API の一覧 | Microsoft Azure App Service"
	description="Azure App Service でロジック アプリを構築する場合に使用できる、Microsoft が管理する API の完全な一覧を取得する"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="deonhe"/>

# ここでは、Microsoft が管理する、アプリ構築用の API の一覧を示します。

アイコンをクリックすると、これらの API を活用して、対象のサービスを呼び出すアプリを簡単に構築する方法を確認できます。これらの API を使用すると、ロジック アプリまたはパワー アプリのいずれか、または両方を構築できます。

料金情報および各サービス レベルに含まれる新機能の一覧については、「[Azure App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください。

> [AZURE.NOTE] Azure アカウントにはサインアップせずに Azure Logic Apps を体験することもできます。「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」にアクセスしてください。App Service で短時間有効な簡易版のロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

|API||||
|-----------|-----------|-----------|-----------|
|[![API アイコン][blobicon]<br/>**Azure BLOB**][azureblobdoc]|[![API アイコン][bingsearchicon]<br/>**Bing Search**][bingsearchdoc]|[![API アイコン][boxicon]<br/>**Box**][boxDoc]|[![API アイコン][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![API アイコン][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![API アイコン][facebookicon]<br/>**Facebook**][facebookdoc]|[![API アイコン][ftpicon]<br/>**FTP**][ftpdoc]|[![API アイコン][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![API アイコン][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![API アイコン][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![API アイコン][office365icon]<br/>**Office 365**<br/>**ユーザー**][office365usersdoc]|[![API アイコン][office365icon]<br/>**Office 365**<br/>**ビデオ**][office365videodoc]|
|[![API アイコン][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API アイコン][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![API アイコン][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![API アイコン][sftpicon]<br/>**SFTP**][sftpdoc]|
|[![API アイコン][sharepointicon]<br/>**SharePoint**<br/>**Server**][sharepointdoc]|[![API アイコン][slackicon]<br/>**Slack**<br/>][slackdoc]|[![API アイコン][smtpicon]<br/>**SMTP**][smtpdoc]|[![API アイコン][sqlicon]<br/>**SQL Azure**][sqldoc]|
|[![API アイコン][twilioicon]<br/>**Twilio**][twiliodoc]|[![API アイコン][twittericon]<br/>**Twitter**][twitterdoc]|[![API アイコン][yammericon]<br/>**Yammer**][yammerdoc] | |


> [AZURE.NOTE] これらの API は、サービスの 2015-12-01-preview バージョンで動作します。

### トリガーとして使用できる API もあります。
いくつかの API は、特定のイベントが発生したときにアプリに通知するトリガーになります。たとえば、FTP API には、OnUpdatedFile トリガーがあります。このトリガーをリッスンし、トリガーが起動されるたびにアクションを実行するロジック アプリまたはパワー アプリを構築できます。

2 種類のトリガーがあります。

* ポーリング トリガー: このトリガーは、指定された頻度でサービスをポーリングして、新しいデータをチェックします。新しいデータを利用できる場合、そのデータを入力値としてアプリの新しいインスタンスが実行されます。同じデータが複数回使用されることを防ぐために、アプリで読み取られて渡されるデータのクリーンアップをトリガーで実行される場合があります。
* プッシュ トリガー: このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンします。その後、アプリの新しいインスタンスをトリガーします。一例として Twitter API が挙げられます。


### アクションとして使用できる API もあります。
API は、アプリ内でアクションとして使用することもできます。アクションは、アプリの実行時に使用できるデータの検索に役立ちます。たとえば、注文を処理するときに、SQL データベースから顧客データを検索することが必要になる場合があります。または、変換先テーブルでデータの書き込み、更新、または削除が必要になる場合があります。これらを実行するために、API によって提供されるアクションを使用できます。アクションは、Swagger メタデータで定義された操作にマップされます。


[新機能](../app-service-logic/app-service-logic-schema-2015-08-01.md) [今すぐロジック アプリを構築する](../app-service-logic/app-service-logic-create-a-logic-app.md) [今すぐパワー アプリの使用を開始する](../power-apps/powerapps-get-started-azure-portal.md)

<!--API Documentation-->
[azureblobdoc]: ./create-api-azureblobstorage.md "Azure BLOB に接続して、BLOB コンテナーのファイルを管理します。"
[bingsearchDoc]: ./create-api-bingsearch.md "Web、イメージ、ニュース、ビデオを対象に Bing 検索します。"
[boxDoc]: ./create-api-box.md "Box に接続して、ファイルのアップロード、取得、削除、一覧の取得や、その他のファイル タスクを実行できます。"
[crmonlinedoc]: ./create-api-crmonline.md "Dynamics CRM Online に接続し、CRM Online データでより多くのことを実行できます。"
[dropboxdoc]: ./create-api-dropbox.md "Dropbox に接続して、ファイルの取得、削除、一覧の取得や、その他のファイル タスクを実行できます。"
[exceldoc]: ./create-api-excel.md "Excel に接続します。"
[facebookdoc]: ./create-api-facebook.md "Facebook に接続することで、タイムラインへの投稿、ページ フィードの取得などを行います。"
[ftpdoc]: ./create-api-ftp.md "FTP/FTPS サーバーに接続して、ファイルのアップロード、取得、削除などのさまざまな FTP タスクを実行できます。"
[googledrivedoc]: ./create-api-googledrive.md "GoogleDrive に接続し、自分のデータを操作します。"
[microsofttranslatordoc]: ./create-api-microsofttranslator.md
[office365outlookdoc]: ./create-api-office365-outlook.md "Office 365 コネクタを使用すると、電子メールの送受信、予定表の管理などの操作を実行できます。さらに、Office 365 アカウントを使用して連絡先を管理することができます。"
[officeunifieddoc]: ./create-api-bingsearch.md
[office365usersdoc]: ./create-api-office365-users.md
[office365videodoc]: ./create-api-office365-video.md
[onedrivedoc]: ./create-api-onedrive.md "個人用の Microsoft OneDrive に接続して、ファイルのアップロード、削除、一覧の取得などの操作を実行できます。"
[salesforcedoc]: ./create-api-salesforce.md "Salesforce アカウントに接続し、アカウント、潜在顧客、営業案件などを管理します。"
[servicebusdoc]: ./create-api-servicebus.md "Service Bus キューとトピックからメッセージを送信したり、Service Bus キューとサブスクリプションからメッセージを受信したりできます。"
[sharepointdoc]: ./create-api-sharepointserver.md "オンプレミスの Microsoft SharePoint Server または SharePoint Online に接続して、ドキュメントやリスト項目を管理できます。既定の資格情報、OAuth 2.0、Windows 認証、およびフォームベース認証などのさまざまな認証方法がサポートされています。"
[slackdoc]: ./create-api-slack.md "Slack に接続し、Slack チャネルにメッセージを投稿します。"
[sftpdoc]: ./create-api-sftp.md "SFTP に接続して、ファイルのアップロード、取得、削除などの操作を実行できます。"
[smtpdoc]: ./create-api-smtp.md "SMTP サーバーに接続して、添付ファイルを含む電子メールを送信します。"
[sqldoc]: ./create-api-sqlazure.md "SQL Azure Database に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。"
[twiliodoc]: ./create-api-twilio.md "Twilio に接続して、メッセージの送信と取得、利用可能な番号の取得、着信電話番号の管理などを実行できます。"
[twitterdoc]: ./create-api-twitter.md "Twitter に接続してタイムラインを取得し、ツイートの投稿などを実行します。"
[yammerdoc]: ./create-api-yammer.md "Yammer に接続して、メッセージを投稿し、新しいメッセージを取得します。"

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0224_2016-->