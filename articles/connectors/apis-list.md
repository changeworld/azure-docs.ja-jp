<properties
	pageTitle="Microsoft が管理するコネクタの一覧 | Microsoft Azure App Service"
	description="Azure App Service でロジック アプリを構築する場合に使用できる、Microsoft が管理するコネクタの完全な一覧を提供します"
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
	ms.topic="get-started-article"
	ms.date="04/29/2016"
	ms.author="deonhe"/>

# 管理されているコネクタの一覧

>[AZURE.NOTE] 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。2014-12-01-preview スキーマ バージョンについては、[コネクタの一覧](../app-service-logic/app-service-logic-connectors-list.md)をご覧ください。

料金情報および各サービス レベルに含まれる新機能の一覧については、「[Azure App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください。

> [AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps を体験することもできます。「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」にアクセスしてください。App Service で短時間有効な簡易版のロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

アイコンをクリックすると、これらのコネクタを活用して、対象のサービスを呼び出すアプリを簡単に構築する方法について確認できます。各コネクタは、ロジック アプリ、PowerApps、およびフローを構築するために使用できます。

|API||||
|-----------|-----------|-----------|-----------|
|[![API アイコン][blobicon]<br/>**Azure BLOB**][azureblobdoc]|[![API アイコン][bingsearchicon]<br/>**Bing Search**][bingsearchdoc]|[![API アイコン][boxicon]<br/>**Box**][boxDoc]|[![API アイコン][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![API アイコン][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![API アイコン][facebookicon]<br/>**Facebook**][facebookdoc]|[![API アイコン][ftpicon]<br/>**FTP**][ftpdoc]|[![API アイコン][githubicon]<br/>**GitHub**][githubdoc]|
[![API アイコン][googledriveicon]<br/>**Google Drive**][googledrivedoc]|[![API アイコン][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![API アイコン][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![API アイコン][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|
|[![API アイコン][office365icon]<br/>**Office 365**<br/>**Users**][office365usersdoc]|[![API アイコン][office365icon]<br/>**Office 365**<br/>**Video**][office365videodoc]|[![API アイコン][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API アイコン][onedriveicon]<br/>**OneDrive<br/>for Business**][onedriveforbusinessdoc]|
|[![API アイコン][outlookicon]<br/>**Outlook**][outlookdoc]|[![API アイコン][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![API アイコン][rssicon]<br/>**RSS**][rssdoc]|[![API アイコン][salesforceicon]<br/>**Salesforce**][salesforcedoc]|
|[![API アイコン][sendgridicon]<br/>**SendGrid**][sendgriddoc]|[![API アイコン][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![API アイコン][sftpicon]<br/>**SFTP**][sftpdoc]|[![API アイコン][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|
|[![API アイコン][slackicon]<br/>**Slack**<br/>][slackdoc]|[![API アイコン][smtpicon]<br/>**SMTP**][smtpdoc]|[![API アイコン][sqlicon]<br/>**SQL Azure**][sqldoc]|[![API アイコン][sqlicon]<br/>**SQL Mashup**][mashupsqldoc]|
|[![API アイコン][trelloicon]<br/>**Trello**][trellodoc]|[![API アイコン][twilioicon]<br/>**Twilio**][twiliodoc]|[![API アイコン][twittericon]<br/>**Twitter**][twitterdoc]|[![API アイコン][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|
[![API アイコン][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] 2014-12-01-preview のスキーマを使用してロジック アプリを作成している場合は、BizTalk 用などのエンタープライズ統合コネクタが上記の一覧にないことにお気付きでしょう。これらの重要性については認識しており、近日中に公開できるように取り組んでおります。正確な公開日をお知らせすることはできませんが、このような API を公開することは最優先事項の 1 つになっています。それまでの間は、[Logic Apps から v1 API および BizTalk API にアクセスする](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/)ことができます。ご理解いただきありがとうございます。しばらくお待ちください。


### トリガーとして使用できるコネクタ
一部のコネクタは、特定のイベントが発生したときにアプリに通知できるトリガーを備えています。たとえば、FTP コネクタには OnUpdatedFile トリガーがあります。このトリガーをリッスンし、トリガーが起動されるたびにアクションを実行するロジック アプリ、PowerApp、またはフローを構築できます。

2 種類のトリガーがあります。

* ポーリング トリガー: このトリガーは、指定された頻度でサービスをポーリングして、新しいデータをチェックします。新しいデータを利用できる場合、そのデータを入力値としてアプリの新しいインスタンスが実行されます。同じデータが複数回使用されることを防ぐために、アプリで読み取られて渡されるデータのクリーンアップをトリガーで実行される場合があります。
* プッシュ トリガー: このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンします。その後、アプリの新しいインスタンスをトリガーします。一例として Twitter コネクタが挙げられます。


### アクションとして使用できるコネクタ
コネクタは、アプリ内でアクションとして使用することもできます。アクションは、アプリの実行時に使用できるデータの検索に役立ちます。たとえば、注文を処理するときに、SQL データベースから顧客データを検索することが必要になる場合があります。または、変換先テーブルでデータの書き込み、更新、または削除が必要になる場合があります。これらを実行するために、コネクタによって提供されるアクションを使用できます。アクションは、Swagger メタデータで定義された操作にマップされます。


[新機能](../app-service-logic/app-service-logic-schema-2015-08-01.md) [今すぐロジック アプリを作成する](../app-service-logic/app-service-logic-create-a-logic-app.md) [今すぐ PowerApps を使い始める](../power-apps/powerapps-get-started-azure-portal.md) [既存のロジック アプリを最新のスキーマ バージョンに移行する](connectors-schema-migration.md)

<!--API Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Azure BLOB に接続して、BLOB コンテナーのファイルを管理します。"
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Web、イメージ、ニュース、ビデオを対象に Bing 検索します。"
[boxDoc]: ./connectors-create-api-box.md "Box に接続して、ファイルのアップロード、取得、削除、一覧の取得や、その他のファイル タスクを実行できます。"
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Dynamics CRM Online に接続し、CRM Online データでより多くのことを実行できます。"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Dropbox に接続して、ファイルの取得、削除、一覧の取得や、その他のファイル タスクを実行できます。"
[exceldoc]: ./connectors-create-api-excel.md "Excel に接続します。"
[facebookdoc]: ./connectors-create-api-facebook.md "Facebook に接続することで、タイムラインへの投稿、ページ フィードの取得などを行います。"
[ftpdoc]: ./connectors-create-api-ftp.md "FTP/FTPS サーバーに接続して、ファイルのアップロード、取得、削除などのさまざまな FTP タスクを実行できます。"
[googledrivedoc]: ./connectors-create-api-googledrive.md "GoogleDrive に接続し、自分のデータを操作します。"
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Office 365 コネクタを使用すると、電子メールの送受信、予定表の管理などの操作を実行できます。さらに、Office 365 アカウントを使用して連絡先を管理することができます。"
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "個人用の Microsoft OneDrive に接続して、ファイルのアップロード、削除、一覧の取得などの操作を実行できます。"
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "ビジネス用の Microsoft OneDrive に接続し、ファイルのアップロード、削除、一覧表示などの操作を実行します。"
[outlookdoc]: ./connectors-create-api-outlook.md "Outlook メールボックスに接続し、電子メールへのアクセスなどを行います。"
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Microsoft Project Online に接続します。"
[rssdoc]: ./connectors-create-api-rss.md "RSS コネクタを使用して、フィード アイテムを発行および取得できます。また、新しいアイテムがフィードに発行されたときに操作をトリガーすることもできます。"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Salesforce アカウントに接続し、アカウント、潜在顧客、営業案件などを管理します。"
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Microsoft Project Online に接続します。"
[servicebusdoc]: ./connectors-create-api-servicebus.md "Service Bus キューとトピックからメッセージを送信したり、Service Bus キューとサブスクリプションからメッセージを受信したりできます。"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "SharePoint Online に接続して、ドキュメントやリスト項目を管理します。"
[slackdoc]: ./connectors-create-api-slack.md "Slack に接続し、Slack チャネルにメッセージを投稿します。"
[sftpdoc]: ./connectors-create-api-sftp.md "SFTP に接続して、ファイルのアップロード、取得、削除などの操作を実行できます。"
[githubdoc]: ./connectors-create-api-github.md "GitHub に接続して、問題を追跡できます。"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "優れた電子メールを送信します。"
[smtpdoc]: ./connectors-create-api-smtp.md "SMTP サーバーに接続して、添付ファイルを含む電子メールを送信します。"
[sqldoc]: ./connectors-create-api-sqlazure.md "SQL Azure Database に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。"
[mashupsqldoc]: ./connectors-create-api-mashupsql.md "SQL に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。"
[trellodoc]: ./connectors-create-api-trello.md "Trello は、どのようなものでも柔軟かつ視覚的に整理でき、だれとでも共有できる無料ツールです。"
[twiliodoc]: ./connectors-create-api-twilio.md "Twilio に接続して、メッセージの送信と取得、利用可能な番号の取得、着信電話番号の管理などを実行できます。"
[twitterdoc]: ./connectors-create-api-twitter.md "Twitter に接続してタイムラインを取得し、ツイートの投稿などを実行します。"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "常時同期のデジタル ライフを実現。"
[yammerdoc]: ./connectors-create-api-yammer.md "Yammer に接続して、メッセージを投稿し、新しいメッセージを取得します。"

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[mashupsqlicon]: ./media/apis-list/mashupsqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0504_2016-->