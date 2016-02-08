<properties
	pageTitle="使用可能なコネクタと API Apps の一覧 | Microsoft Azure App Service"
	description="Azure App Service のコネクタと API Apps に関するページを参照してください。"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="mandia"/>


# Logic Apps で使用するコネクタと API Apps の一覧
このトピックでは、マイクロソフトによって作成された、Logic Apps 内で使用できるすべてのコネクタと API Apps を示します。

料金情報および各サービス レベルに含まれる新機能の一覧については、「[Azure App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください。

> [AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## コアのコネクタ
次の表に、コアのコネクタとして使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API Apps を示します。

名前 | 説明
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | Azure で Hadoop クラスターを作成し、別の Hadoop ジョブを送信するには、このコネクタを使用します。
[Azure の Service Bus](app-service-logic-connector-azureservicebus.md) | Service Bus キューとトピックからメッセージを送信したり、Service Bus キューとサブスクリプションからメッセージを受信したりできます。
[Azure Storage Blob](app-service-logic-connector-azurestorageblob.md) | BLOB ストレージに接続して、BLOB の取得、削除、一覧の取得などを実行できます。 
Bing 検索 | Web、イメージ、ニュース、ビデオを対象に Bing 検索します。
Bing 翻訳 | Bing を使用して、テキストを別の言語に翻訳します。
[Box](app-service-logic-connector-box.md) | Box に接続して、ファイルのアップロード、取得、削除、一覧の取得や、その他のファイル タスクを実行できます。
[Chatter](app-service-logic-connector-chatter.md) | Chatter に接続して、メッセージの投稿と検索を実行できます。新しいメッセージを取得することもできます。
[Dropbox](app-service-logic-connector-dropbox.md) | Dropbox に接続して、ファイルの取得、削除、一覧の取得や、その他のファイル タスクを実行できます。
[Facebook](app-service-logic-connector-facebook.md) | Facebook に接続して、メッセージ、画像、その他の詳細を公開できます。また、メッセージ、コメントを取得できるほか、本や映画などのユーザーの好みに関する情報を取得することもできます。
[HTTP](app-service-logic-connector-http.md) | HTTP リスナーは、HTTP サーバーとして機能するエンドポイントを開き、着信 HTTP または HTTPS 要求をリッスンします。HTTP アクションに API アプリは必要ありません。HTTP アクションは Logic Apps 内でネイティブにサポートされます。
[Microsoft Office 365](app-service-logic-connector-office365.md) | Office 365 コネクタを使用すると、電子メールの送受信、予定表の管理などの操作を実行できます。さらに、Office 365 アカウントを使用して連絡先を管理することができます。
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | 個人用の Microsoft OneDrive に接続して、ファイルのアップロード、削除、一覧の取得などの操作を実行できます。
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | オンプレミスの Microsoft SharePoint Server または SharePoint Online に接続して、ドキュメントやリスト項目を管理できます。既定の資格情報、OAuth 2.0、Windows 認証、およびフォームベース認証などのさまざまな認証方法がサポートされています。
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Yammer に接続して、メッセージを投稿し、新しいメッセージを取得します。
[QuickBooks](app-service-logic-connector-quickbooks.md) | このコネクタを使用すると、Intuit QuickBooks のさまざまなエンティティ (顧客、項目、請求書など) の作成、更新、クエリなどの操作を実行できます。
[Slack](app-service-logic-connector-slack.md) | Slack に接続し、Slack チャネルにメッセージを投稿します。
[Salesforce](app-service-logic-connector-salesforce.md) | Salesforce アカウントに接続し、アカウント、潜在顧客、営業案件などを管理します。
[SugarCRM](app-service-logic-connector-sugarcrm.md) | SugarCRM Online に接続して、さまざまな種類のモジュール (アカウント、連絡先など) の作成、更新、読み取りなどの操作を実行できます。
[Twilio](app-service-logic-connector-twilio.md) | Twilio に接続して、メッセージの送信と取得、利用可能な番号の取得、着信電話番号の管理などを実行できます。
[Twitter](app-service-logic-connector-twitter.md) | Twitter に接続してタイムラインを取得し、ツイートの投稿などを実行します。
[待機](app-service-logic-connector-wait.md) | このコネクタを使用して、アプリの実行を遅らせることができます。アプリの実行を、指定した時間分遅らせることも、特定の時点まで遅らせることもできます。


## エンタープライズ統合コネクタ
次の表に、エンタープライズ統合コネクタとして使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API Apps を示します。

名前 | 説明
------------- | -------------
[AS2 コネクタ](app-service-logic-connector-as2.md) | AS2 トランスポート プロトコルを使用してメッセージを送受信できます。データは、デジタル証明書と暗号化を使用して安全かつ確実に転送されます。
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | 企業間通信で EDIFACT プロトコルを使用して、メッセージを送受信します。
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | フラット ファイル データ (Excel、csv など) と XML データ間の相互運用性を提供します。この API アプリによって、フラット ファイル インスタンスを XML に、またはその逆方向に変換できます。
[BizTalk JSON エンコーダー](app-service-logic-connector-jsonencoder.md) | アプリでの JSON データと XML データの相互運用に役立つエンコーダーおよびデコーダーです。JSON インスタンスを指定して XML に変換したり、その逆方向に変換したりできます。
[BizTalk ルール](app-service-logic-use-biztalk-rules.md) | BizTalk ルールは組織内のビジネス ロジックを定義し、制御します。ビジネス ポリシーは、関連付けられているアプリケーションの再コンパイルや再デプロイなしで更新できます。
[BizTalk 取引先管理](app-service-logic-connector-tpm.md) | パートナー、契約、契約で使用されるスキーマと証明書を使用して、企業間リレーションシップを定義したり保持したりします。これらのリレーションシップは、AS2、EDIFACT、および X12 API Apps を使用して適用されます。
[BizTalk Transform Service](app-service-logic-transform-xml-documents.md) | データをある形式から別の形式に変換します。また、既存のマップ (.trfm ファイル) をアップロードし、ソース スキーマとターゲット スキーマ間のリンクを表示できるほか、サンプルの入力 XML コンテンツを使用した "テスト" 機能も使用できます。文字列操作、条件付き割り当てなどの各種組み込み関数も用意されています。
[BizTalk X12](app-service-logic-connector-x12.md) | 企業間通信で X12 プロトコルを使用して、メッセージを送受信します。
[BizTalk XML Validator](app-service-logic-xml-validator.md) | 定義済みの XML スキーマを基準として XML データを検証します。既存のスキーマを使用することも、フラット ファイルのインスタンス、JSON のインスタンス、または既存のコネクタに基づいてスキーマを生成することもできます。
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | 選択した XPath に基づいて XML コンテンツを検索し、データを抽出します。
[DB2 コネクタ](app-service-logic-connector-db2.md) | Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM DB2 データベースに接続します。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。<br/><br/>トリガーはありません。アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。
[ファイル](app-service-logic-connector-file.md) | このコネクタを使用すると、オンプレミスのファイル システムやネットワークに接続して、ファイルのアップロード、削除、一覧の取得などのさまざまなファイル タスクを実行できます。
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | FTP/FTPS サーバーに接続して、ファイルのアップロード、取得、削除などのさまざまな FTP タスクを実行できます。
[Informix](app-service-logic-connector-informix.md) | Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM Informix データベースに接続します。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。<br/><br/>トリガーはありません。アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。
[Microsoft SQL Server](app-service-logic-connector-sql.md) | オンプレミスの SQL Server または Azure SQL Database に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。
MQ | Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM WebSphere MQ Server Version 8 に接続します。オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。コネクタには、Microsoft Client for MQ も含まれています。<br/><br/>トリガーはありません。アクションはありません。<br/><br/>**注** 現在のところ、Logic Apps で使用することはできません。
[Oracle Database](app-service-logic-connector-oracle.md) | オンプレミスの Oracle Database に接続して、データベース テーブルのエントリを作成、更新、取得、削除できます。
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| POP3 サーバーに接続して、添付ファイルを含むメールを取得します。
[SAP](app-service-logic-connector-sap.md) | オンプレミスの SAP サーバーに接続し、RFC、BAPI、tRFC を呼び出して、IDOC を送信します。
[SFTP](app-service-logic-connector-sftp.md) (SSH ファイル転送プロトコル)| SFTP に接続して、ファイルのアップロード、取得、削除などの操作を実行できます。
[SMTP](app-service-logic-connector-smtp.md) (簡易メール転送プロトコル) | SMTP サーバーに接続して、添付ファイルを含む電子メールを送信します。

## トリガーとしてのコネクタ
いくつかのコネクタは、Logic Apps のトリガーとして提供されています。これらのトリガーには、次の 2 種類があります。

1. ポーリング トリガー: このトリガーは、指定された頻度でサービスをポーリングして、新しいデータをチェックします。新しいデータを利用できる場合、そのデータを入力値としてロジック アプリの新しいインスタンスが実行されます。同じデータが複数回使用されることを防ぐために、ロジック アプリで読み取られて渡されるデータのクリーンアップをトリガーで実行される場合があります。このようなコネクタには、ファイル、SQL、Azure Storage があります。
2. プッシュ トリガー: このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンします。その後、ロジック アプリの新しいインスタンスをトリガーします。このようなコネクタには、HTTP リスナーや Twitter があります。

## アクションとしてのコネクタ
コネクタは、ロジック アプリ内でアクションとして使用することもできます。アクションは、実行時に使用するロジック アプリ内のデータの検索に役立ちます。たとえば、注文を処理するときに、顧客に関する情報を求めて SQL データベースのデータを検索することが必要になる場合があります。または、変換先のデータの書き込み、更新、または削除が必要になる場合があります。これらを実行するために、コネクタによって提供されるアクションを使用できます。アクションは、Swagger メタデータで定義されているとおりに、API Apps 内の操作と対応付けられています。

## 独自のコネクタと API Apps の作成
[コネクタと API Apps のリファレンス](http://aka.ms/appservicesconnectorreference) [Azure App Service の API アプリのトリガー](../app-service-api/app-service-api-dotnet-triggers.md) [Logic Apps のリファレンス](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## コネクタと API Apps の詳細
[コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md) [Azure App Service での Hybrid Connection Manager の使用](app-service-logic-hybrid-connection-manager.md) [組み込み API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)

<!---HONumber=AcomDC_0128_2016-->