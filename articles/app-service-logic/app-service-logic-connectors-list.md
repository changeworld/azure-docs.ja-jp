<properties 
	pageTitle="コネクタと API Apps の一覧 | Azure" 
	description="Azure App Service マイクロサービス アーキテクチャのコネクタと API Apps について説明します。" 
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
	ms.date="06/08/2015" 
	ms.author="mandia"/>


# Microsoft Azure App Service のコネクタと API Apps の一覧
このトピックでは、マイクロソフトによって作成された使用可能なすべてのコネクタと API Apps を示します。

料金情報および各サービス レベルに含まれる新機能の一覧については、「[Azure App Service 料金](http://azure.microsoft.com/pricing/details/app-service/)」を参照してください。


## Standard コネクタ
次の表に、Standard コネクタと使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API Apps を示します。

名前 | 説明
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | このコネクタは、Azure 上で Hadoop クラスターを作成し、Hive、Pig、MapReduce、ストリーミング MapReduce など、さまざまな Hadoop ジョブを送信するために使用します。また、このコネクタを使用して、クラスターのスピン、ジョブの送信を実行できるほか、ジョブの完了を待機できます。
[Azure の Service Bus](app-service-logic-connector-azureservicebus.md) | このコネクタを使用すると、キュー、トピックなどの Service Bus エンティティからメッセージを送信したり、キュー、サブスクリプションなどの Service Bus エンティティからメッセージを受信したりできます。
[Azure Storage BLOB](app-service-logic-connector-azurestorageblob.md) | BLOB ストレージに接続して、BLOB のアップロード、BLOB の取得、BLOB の削除、コンテナー内の BLOB の一覧の取得、BLOB のスナップショットの作成、BLOB のコピーなどの操作を実行できます。さらに、トリガーを使用して BLOB を取得することができます。
Azure Web ジョブ | Web ジョブに接続します。
[Box](app-service-logic-connector-box.md) | Box に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得を実行できます。さらに、トリガーを使用してファイルを取得することができます。
[Chatter](app-service-logic-connector-chatter.md) | Chatter に接続して、メッセージの投稿、検索などの操作を実行できます。さらに、トリガーを使用して新しいメッセージを取得することができます。
[Dropbox](app-service-logic-connector-dropbox.md) | Dropbox に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得を実行できます。さらに、トリガーを使用してファイルを取得することができます。
[Facebook](app-service-logic-connector-facebook.md) | Facebook に接続して、メッセージ、ビデオ、写真、オファーなどを発行することができます。さらに、メッセージ、コメント、イベント、ユーザーのフィードや、本、ゲーム、映画などのユーザーの好みに関する情報を取得することもできます。このコネクタには、ページに投稿された新しいメッセージを取得するためのトリガーも含まれています。
[ファイル](app-service-logic-connector-file.md) | ファイル システムまたはネットワークとの間でファイルの送信またはアップロードやファイルの受信またはダウンロードを行うことができます。このコネクタを使用すると、オンプレミスのファイル サーバーに接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | FTP/FTPS サーバーに接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得を実行できます。さらに、トリガーを使用してファイルを取得することができます。
[HTTP](app-service-logic-connector-http.md) | HTTP リスナーは、HTTP サーバーとして機能するエンドポイントを開き、着信 HTTP 要求をリッスンします。POST、GET、DELETE、および PUT を実行できます。HTTP アクションに API アプリは必要ありません。HTTP アクションは Logic Apps 内でネイティブにサポートされます。プロトコルとして HTTP または HTTPS を使用できます。
[Microsoft Office 365](app-service-logic-connector-office365.md) | Office 365 コネクタを使用すると、電子メールの送受信、予定表の管理などの操作を実行できます。さらに、Office 365 アカウントを使用して連絡先を管理することができます。さらに、電子メールの送受信と取得、予定表のイベントの作成と削除、連絡先の作成、更新、取得、削除などの操作を実行できます。
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Microsoft OneDrive は、ファイル ホスティング サービスです。OneDrive コネクタを使用すると、個人用の Microsoft OneDrive に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | オンプレミスの Microsoft SharePoint Server または SharePoint Online に接続して、ドキュメントやリスト項目を管理できます。さらに、ドキュメントやリスト項目を作成、更新、取得、削除することもできます。既定の資格情報、OAuth 2.0、Windows 認証、およびフォームベース認証などのさまざまな認証方法がサポートされています。
[Microsoft SQL Server](app-service-logic-connector-sql.md) | オンプレミスの SQL Server または Azure SQL Database に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Yammer に接続します。このコネクタには、メッセージの投稿アクションや新しいメッセージを取得するトリガーが含まれます。
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| POP3 サーバーに接続します。このコネクタには、添付ファイルを含む電子メールを取得するためのトリガーが含まれています。
[QuickBooks](app-service-logic-connector-quickbooks.md) | このコネクタを使用すると、Intuit QuickBooks のさまざまなエンティティ (顧客、項目、請求書など) の作成、更新、読み取り、削除、クエリなどの操作を実行できます。
[SFTP](app-service-logic-connector-sftp.md) (SSH ファイル転送プロトコル)| SFTP に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得を実行できます。さらに、トリガーを使用してファイルを取得することができます。
[SMTP](app-service-logic-connector-smtp.md) (簡易メール転送プロトコル) | SMTP サーバーに接続して、添付ファイルを含む電子メールを送信します。
[Slack](app-service-logic-connector-slack.md) | 
[Salesforce](app-service-logic-connector-salesforce.md) | Salesforce コネクタは、Salesforce アカウントのさまざまなエンティティ (アカウント、潜在顧客、営業案件、事例など) を管理します。さらに、さまざまなエンティティの作成、更新、upsert、クエリ、削除などの操作を実行できます。
[SugarCRM](app-service-logic-connector-sugarcrm.md) | SugarCRM Online に接続して、さまざまな種類のモジュール (アカウント、連絡先、製品など) の作成、更新、読み取り、削除などの操作を実行できます。
[Twilio](app-service-logic-connector-twilio.md) | Twilio に接続して、メッセージの送信、メッセージの取得、メッセージの一覧の取得、使用状況の一覧の取得、使用可能な無料通話番号の取得、使用可能な携帯電話番号の取得、使用可能な市内番号の取得、着信電話番号の一覧の取得、着信電話番号の取得、着信電話番号の追加などの操作を行うことができます。
[Twitter](app-service-logic-connector-twitter.md) | Twitter に接続して、ユーザー タイムラインの取得、ツイートの検索、フォロワーの取得、友人の取得、ユーザーの検索、ホーム タイムラインの取得、メンション タイムラインの取得、ツイートの投稿、ユーザーへのツイートの投稿、ダイレクト メッセージの送信などの操作を実行できます。さらに、Twitter コネクタでは、キーワードによるツイートの取得、ユーザー ハンドルによるツイートの取得、ハッシュタグによるツイートの取得などのトリガーも使用します。
待機 | このコネクタを使用して、アプリの実行を遅らせることができます。アプリの実行を、指定した時間分遅らせることも、特定の時点まで遅らせることもできます。


## Premium コネクタ
次の表に、Premium コネクタで使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API Apps を示します。

名前 | 説明
------------- | -------------
AS2 コネクタ | AS2 コネクタは、企業間の通信において AS2 トランスポート プロトコルを使用してメッセージを送受信できます。データは、デジタル証明書と暗号化を使用してインターネット経由で安全かつ確実に転送されます。
BizTalk EDIFACT | EDIFACT API アプリは、企業間通信で EDIFACT プロトコルを使用して、メッセージを送受信します。
BizTalk X12 | BizTalk X12 | X12 API アプリは、企業間通信で X12 プロトコルを使用して、メッセージを送受信します。
BizTalk 取引先管理 | 取引先管理 API アプリは、パートナー、契約、および契約で使用されるスキーマと証明書を使用して、企業間リレーションシップを定義および保持します。これらのリレーションシップは、AS2、EDIFACT、および X 12 API アプリを使用して適用されます。
BizTalk JSON Encoder | アプリでの JSON データと XML データの相互運用に役立つエンコーダーおよびデコーダーです。JSON インスタンスを指定して XML に変換したり、その逆方向に変換したりできます。
[BizTalk ルール](app-service-logic-use-biztalk-rules.md) | BizTalk ルールは、組織の構造、運用、および戦略を定義および制御します。ビジネス ポリシーは、関連付けられているアプリケーションを再コンパイルして再デプロイすることなく更新できます。
DB2 コネクタ | Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM DB2 データベースに接続します。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。<br/><br/>トリガーはありません。アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。
Informix | Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM Informix データベースに接続します。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。<br/><br/>トリガーはありません。アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。
MQ | Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM WebSphere MQ Server Version 8 に接続します。オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。コネクタには、Microsoft Client for MQ も含まれています。<br/><br/>トリガーはありません。アクションはありません。<br/><br/><strong>注</strong> 現在のところ、Logic Apps で使用することはできません。
[Oracle Database](app-service-logic-connector-oracle.md) | Oracle Database コネクタを使用すると、オンプレミスの Oracle Database に接続して、データベース テーブルのエントリを作成、更新、取得、削除できます。
[SAP](app-service-logic-connector-sap.md) | SAP コネクタは、オンプレミスの SAP サーバーに接続し、RFC、BAPI、tRFC を呼び出して、IDOC を送信します。
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | フラット ファイル データ (Excel、csv など) と XML データ間の相互運用性を提供します。この API アプリによって、フラット ファイル インスタンスを XML に、またはその逆方向に変換できます。
[BizTalk Transform Service](app-service-logic-transform-xml-documents.md) | BizTalk Transform API アプリは、データの形式を変換します。また、既存のマップ (.trfm ファイル) のアップロード、ソース スキーマとターゲット スキーマ間のリンクを表示するマップのグラフィカル表示などの操作を実行できます。さらに、サンプルの入力 XML コンテンツを使用した "テスト" 機能を使用することができます。文字列操作、条件付き割り当て、算術式、日付時刻フォーマッタ、ループなどの各種の組み込み関数も用意されています。
[BizTalk XML Validator](app-service-logic-xml-validator.md) | Validator API アプリは、定義済みの XML スキーマに対して XML データを検証します。既存のスキーマを使用することも、フラット ファイルのインスタンス、JSON のインスタンス、または既存のコネクタに基づいてスキーマを生成することもできます。
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Extractor API アプリは、特定の XPath に基づいて XML コンテンツを検索し、データを抽出します。

## トリガーとしてのコネクタ
いくつかのコネクタは、Logic Apps のトリガーとして提供されています。これらのトリガーには、次の 2 種類があります。

1. ポーリング トリガー: このトリガーは、指定された頻度でサービスをポーリングして、新しいデータをチェックします。新しいデータを利用できる場合、そのデータを入力値としてロジック アプリの新しいインスタンスが実行されます。同じデータが複数回使用されることを防ぐために、ロジック アプリで読み取られて渡されるデータのクリーンアップをトリガーで実行される場合があります。このようなコネクタには、ファイル、SQL、Azure Storage があります。
2. プッシュ トリガー: このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンします。その後、ロジック アプリの新しいインスタンスをトリガーします。このようなコネクタには、HTTP リスナーや Twitter があります。

## アクションとしてのコネクタ
コネクタは、ロジック アプリ内でアクションとして使用することもできます。アクションは、実行時に使用するロジック アプリ内のデータの検索に役立ちます。たとえば、注文を処理するときに、顧客に関する情報を求めて SQL データベースのデータを検索することが必要になる場合があります。または、変換先のデータの書き込み、更新、または削除が必要になる場合があります。これらを実行するために、コネクタによって提供されるアクションを使用できます。アクションは、Swagger メタデータで定義されているとおりに、API Apps 内の操作と対応付けられています。

## 独自のコネクタと API Apps の作成
[コネクタと API Apps のリファレンス](http://aka.ms/appservicesconnectorreference)<br/> [Azure App Service API アプリ トリガー](../app-service-api/app-service-api-dotnet-triggers.md)


## その他のコネクタ

[コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)<br/> [Azure App Service でのハイブリッド接続マネージャーの使用](app-service-logic-hybrid-connection-manager.md)

<!---HONumber=62-->