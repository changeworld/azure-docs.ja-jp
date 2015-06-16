<properties 
   pageTitle="コネクタの使用" 
   description="コネクタの使用" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>

#コネクタ#
コネクタは、クラウドやオンプレミスで実行されるデータとサービスに接続するための API アプリです。コネクタを使うと、Logic Apps などで簡単にアクセスできるさまざまな組み込みのトリガーとアクションを使用して、データに簡単にアクセスできます。

Azure App Services では、すぐに使用できるさまざまなコネクタが提供されています。

##Standard コネクタ##
* [Azure Service Bus コネクタ]
* [Azure Storage BLOB コネクタ]
* Azure Web ジョブ コネクタ
* [Box コネクタ]
* [Chatter コネクタ]
* [Dropbox コネクタ]
* [Facebook コネクタ]
* [File コネクタ]
* [FTP コネクタ]
* [HDInsight コネクタ]
* [HTTP コネクタ]
* [Office 365 コネクタ]
* [OneDrive コネクタ]
* [Oracle コネクタ]
* [POP3 コネクタ]
* [QuickBooks コネクタ]
* [Salesforce コネクタ]
* [SFTP コネクタ]
* [SharePoint コネクタ]
* [Slack コネクタ]
* [SMTP コネクタ]
* [SQL コネクタ]
* [SugarCRM コネクタ]
* [Twilio コネクタ]
* [Twitter コネクタ]
* 待機コネクタ
* [Yammer コネクタ]

##Premium コネクタと API Apps##
* AS2 コネクタ
* BizTalk EDIFACT
* BizTalk Flat File Encoder
* BizTalk Transform Service
* BizTalk ルール
* BizTalk X12
* BizTalk XPath Extractor
* BizTalk XML Validator
* DB2 コネクタ
* Informix コネクタ
* Oracle データベース コネクタ
* MQ コネクタ
* [SAP コネクタ]
 
その他の詳細については、コネクタ API リファレンス [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference) を参照してください。

##コネクタとロジック アプリ##
ロジック アプリはトリガーとアクションで構成されます。一部のコネクタは、イベントや一部のデータの可用性に基づいてワークフローをインスタンス化するトリガーとして使用できます。コネクタは、データの読み取りと書き込みや、そのコネクタでサポートされているその他の操作を実行するためのアクションとしても使用されます。

###トリガーとしてのコネクタ###
いくつかのコネクタは、ロジック アプリのトリガーとして提供されています。これらのトリガーには、次の 2 種類があります。

1. ポーリング トリガー: このトリガーは、指定された頻度で目的のサービスをポーリングして、新しいデータをチェックします。新しいデータを利用できる場合、そのデータを入力値としてロジック アプリの新しいインスタンスが実行されます。ロジック アプリで読み取られて渡されるデータのクリーンアップなどの他のタスクをトリガーで実行して、同じデータが複数回使用されることを防いでいる場合があります。このようなコネクタには、ファイル、SQL、Azure のストレージがあります。
2. プッシュ トリガー: このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンして、ロジック アプリの新しいインスタンスをトリガーします。このようなコネクタには、HTTP リスナーや Twitter があります。

###アクションとしてのコネクタ###
コネクタは、ロジック アプリの一部を構成するアクションとして使用することもできます。アクションは、実行時に使用するロジック アプリ内のデータの検索に役立ちます。たとえば、注文を処理するときに、顧客に関する情報を求めて SQL DB のデータを検索することが必要になる場合があります。または、変換先のデータを書き込み、更新、または削除する必要があります。このために、コネクタによって提供されるアクションを使用できます。アクションは、Swagger メタデータで定義されているとおりに、API アプリ内の操作と対応付けられています。


<!-- Links -->

[Box コネクタ]: app-service-logic-connector-box.md
[Facebook コネクタ]: app-service-logic-connector-facebook.md
[Salesforce コネクタ]: app-service-logic-connector-salesforce.md
[Twitter コネクタ]: app-service-logic-connector-twitter.md
[SAP コネクタ]: app-service-logic-connector-sap.md
[FTP コネクタ]: app-service-logic-connector-ftp.md
[HTTP コネクタ]: app-service-logic-connector-http.md
[Azure Storage BLOB コネクタ]: app-service-logic-connector-azurestorageblob.md
[Office 365 コネクタ]: app-service-logic-connector-office365.md
[SharePoint コネクタ]: app-service-logic-connector-sharepoint.md
[SugarCRM コネクタ]: app-service-logic-connector-sugarcrm.md
[QuickBooks コネクタ]: app-service-logic-connector-quickbooks.md
[Yammer コネクタ]: app-service-logic-connector-yammer.md
[Twilio コネクタ]: app-service-logic-connector-twilio.md
[SMTP コネクタ]: app-service-logic-connector-smtp.md
[SFTP コネクタ]: app-service-logic-connector-sftp.md
[POP3 コネクタ]: app-service-logic-connector-pop3.md
[Dropbox コネクタ]: app-service-logic-connector-dropbox.md
[Chatter コネクタ]: app-service-logic-connector-chatter.md
[HDInsight コネクタ]: app-service-logic-connector-hdinsight.md
[Azure Service Bus コネクタ]: app-service-logic-connector-azureservicebus.md
[Oracle コネクタ]: app-service-logic-connector-oracle.md
[SQL コネクタ]: app-service-logic-connector-sql.md
[OneDrive コネクタ]: app-service-logic-connector-onedrive.md
[File コネクタ]: app-service-logic-connector-file.md
[Slack コネクタ]: app-service-logic-connector-slack.md


<!--HONumber=54--> 