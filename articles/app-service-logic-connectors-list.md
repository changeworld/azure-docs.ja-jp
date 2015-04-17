<properties 
	pageTitle="コネクタと API Apps の一覧 | Azure" 
	description="Azure App Service マイクロサービス アーキテクチャのコネクタと API Apps について説明します。" 
	services="app-service" 
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
	ms.date="03/24/2015" 
	ms.author="mandia"/>


# Microsoft Azure App Service のコネクタと API Apps の一覧

次の表に、Microsoft によって作成された使用可能なすべてのコネクタと API アプリをアルファベット順に示します。 

	名前  | 説明
------------- | ------------- 
AS2 コネクタ | AS2 コネクタは、企業間の通信において AS2 トランスポート プロトコルを使用してメッセージを送受信できます。データは、デジタル証明書と暗号化を使用してインターネット経由で安全かつ確実に転送されます。<br/><br/>[企業間のコネクタ](app-service-logic-b2b-connectors.md)
Azure HDInsight | Azure HDInsight は、Apache Hadoop クラスターをクラウドにデプロイしてプロビジョニングするためのサービスです。ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークとなります。このコネクタは、Azure に Hadoop クラスターを作成し、Hive、Pig、MapReduce、ストリーミング MapReduce などの異なる Hadoop ジョブを送信します。このコネクタを使用すると、クラスターのスピン、ジョブの送信のほか、ジョブの完了を待機することができます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Azure Mobile Services | Azure Mobile Services は、モバイル アプリのバックエンド機能を有効にする Azure サービスのセットです。このコネクタを使用すると、Azure Mobile Services に接続して、エントリを作成、更新、取得、削除できます。さらに、テーブルに対してカスタム API を呼び出すこともできます<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Azure のサービス バス | Azure Service Bus は、あらゆるものを接続するための、汎用のクラウド ベースのメッセージング システムです。Azure Service Bus では、拡張性と信頼性を高めるために、疎結合方式でメッセージを交換できます。このコネクタを使用すると、キュー、トピックなどの Service Bus エンティティからメッセージを送信したり、キュー、サブスクリプションなどの Service Bus エンティティからメッセージを受信したりできます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Azure Storage BLOB | Azure Storage BLOB は、テキストやバイナリ データなどの大量の非構造化データを格納します。これらのデータには、世界中のどこからでもアクセスできます。このコネクタを使用すると、BLOB ストレージに接続して、BLOB のアップロード、BLOB の取得、BLOB の削除、コンテナー内の BLOB の一覧の取得、BLOB のスナップショットの作成、BLOB のコピーなどの操作を実行できます。さらに、トリガーを使用して BLOB を取得することができます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Azure Storage Table | Azure Storage Table サービスは、大量の構造化データを格納します。このサービスは、構造化された非リレーショナル データを格納するのに最適な NoSQL データストアです。このコネクタを使用すると、Azure Storage テーブルに接続して、エンティティの取得、エンティティの照会、エンティティの挿入、エンティティの更新、エンティティの削除などの操作を実行できます。さらに、トリガーを使用してデータを取得することができます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
BizTalk EDIFACT | EDIFACT API アプリは、企業間通信で EDIFACT プロトコルを使用して、メッセージを送受信します。<br/><br/>[企業間コネクタ](app-service-logic-b2b-connectors.md)
BizTalk X12 | X12 API アプリは、企業間通信で X12 プロトコルを使用して、メッセージを送受信します。<br/><br/>[企業間コネクタ](app-service-logic-b2b-connectors.md)
BizTalk Flat File Encoder | フラット ファイル データ (Excel、csv など) と XML データ間の相互運用性を提供します。この API アプリを使用すると、フラット ファイル インスタンスと XML の間で相互に変換できます。<br/><br/>[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)
BizTalk JSON Encoder | JSON データと XML データ間の相互運用性を提供します。この API アプリを使用すると、JSON インスタンスと XML の間で相互に変換できます。<br/><br/>[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)
BizTalk ルール | BizTalk ルールは、組織の構造、運用、および戦略を定義および制御します。ビジネス ポリシーは、関連付けられているアプリケーションを再コンパイルして再デプロイすることなく更新できます。<br/><br/>[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)
BizTalk 取引先管理 | 取引先管理 API アプリは、パートナー、契約、および契約で使用されるスキーマと証明書を使用して、企業間リレーションシップを定義および保持します。これらのリレーションシップは、AS2、EDIFACT、および X 12 API アプリを使用して適用されます。<br/><br/>[企業間コネクタ](app-service-logic-b2b-connectors.md)
BizTalk Transform Service | BizTalk Transform API は、データの形式を変換します。また、既存のマップ (.trfm ファイル) のアップロード、ソース スキーマとターゲット スキーマ間のリンクを表示するマップのグラフィカル表示などの操作を実行できます。さらに、サンプルの入力 XML コンテンツを使用した "テスト" 機能を使用することができます。文字列操作、条件付き割り当て、算術式、日付時刻フォーマッタ、ループなどの各種の組み込み関数も用意されています。<br/><br/>[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)
BizTalk XML Validator | Validator API アプリは、定義済みの XML スキーマに対して XML データを検証します。既存のスキーマを使用することも、フラット ファイルのインスタンス、JSON のインスタンス、または既存のコネクタに基づいてスキーマを生成することもできます。<br/><br/>[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)
BIzTalk XPath Extractor | Extractor API アプリは、特定の XPath に基づいて XML コンテンツを検索し、データを抽出します。<br/><br/>[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)
Box | Box は、ファイル共有サービスです。Box コネクタを使用すると、Box に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Chatter | Chatter は、エンタープライズ向けのソーシャル ネットワークです。Chatter コネクタを使用すると、Chatter に接続して、メッセージの投稿、検索などの操作を実行できます。さらに、トリガーを使用して新しいメッセージを取得することができます。<br/><br/>[ソーシャル コネクタ](app-service-logic-social-connectors.md)
DropBox | Dropbox は、ファイル ホスティング サービスです。このコネクタを使用すると、Dropbox に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Facebook | Facebook は、ソーシャル ネットワーキング サービスです。Facebook コネクタを使用すると、Facebook に接続して、メッセージ、ビデオ、写真、オファーなどを発行することができます。さらに、メッセージ、コメント、イベント、ユーザーのフィードや、本、ゲーム、映画などのユーザーの好みに関する情報を取得することもできます。このコネクタには、ページに投稿された新しいメッセージを取得するためのトリガーも含まれています。<br/><br/>[ソーシャル コネクタ](app-service-logic-social-connectors.md)
ファイル  | ファイル コネクタを使用すると、ファイル システムまたはネットワークとの間でファイルの送信またはアップロード、またはファイルの受信またはダウンロードを行うことができます。このコネクタを使用すると、オンプレミスのファイル サーバーに接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。<br/><br/>[プロトコル コネクタ](app-service-logic-protocol-connectors.md)
FTP<br/>FTPS | FTP (ファイル転送プロトコル) は、ファイルをホスト間で転送するために使用される一般的なネットワーク プロトコルです。FTP コネクタを使用すると、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。<br/><br/>[プロトコル コネクタ](app-service-logic-protocol-connectors.md)
HTTP<br/>HTTPS | HTTP リスナーは、HTTP サーバーとして機能するエンドポイントを開き、着信 HTTP 要求をリッスンします。<br/><br/>[プロトコル コネクタ](app-service-logic-protocol-connectors.md)
HTTP<br/>HTTPS | HTTP コネクタは、HTTP または HTTPS プロトコルを使用して HTTP サーバーにデータを送信します。<br/><br/>[プロトコル コネクタ](app-service-logic-protocol-connectors.md)
Marketo | Marketo は、受信マーケティング、ソーシャル マーケティング、CRM、およびその他の関連サービスを提供する自動マーケティング ソフトウェアを開発しています。Marketo コネクタを使用すると、Marketo に接続して、潜在顧客の作成および更新、潜在顧客の変更の取得、キャンペーンのスケジュール設定、キャンペーンの要求、潜在顧客の取得、キャンペーンまたは一覧の情報の取得、一覧への潜在顧客の追加、一覧からの潜在顧客の削除などの操作を実行できます。<br/><br/>[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)
Microsoft Office 365 | Office 365 コネクタを使用すると、電子メールの送受信、予定表の管理などの操作を実行できます。さらに、Office 365 アカウントを使用して連絡先を管理することができます。さらに、電子メールの送受信と取得、予定表のイベントの作成と削除、および連絡先の作成、更新、取得、および削除などの操作を実行できます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Microsoft OneDrive | Microsoft OneDrive は、ファイル ホスティング サービスです。OneDrive コネクタ  を使用すると、個人用の Microsoft OneDrive に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Microsoft SharePoint | Microsoft SharePoint コネクタを使用すると、オンプレミスの Microsoft SharePoint Server または SharePoint Online に接続して、ドキュメントやリスト項目を管理できます。さらに、ドキュメントやリスト項目を作成、更新、取得、削除することもできます。既定の資格情報、OAuth 2.0、Windows 認証、およびフォームベース認証などのさまざまな認証方法がサポートされています。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Microsoft SQL Server | Microsoft SQL コネクタは、オンプレミスの SQL Server または Azure SQL Database に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Microsoft Yammer | Microsoft Yammer は、エンタープライズ向けのソーシャル ネットワークです。Yammer コネクタは、Yammer に接続します。このコネクタには、メッセージの投稿アクションや新しいメッセージを取得するトリガーが含まれます。<br/><br/>[ソーシャル コネクタ](app-service-logic-social-connectors.md)
MongoDB | MongoDB コネクタを使用すると、Mongo サーバー データベース (クラウドまたはオンプレミス) に接続して、MongoDB コレクションのエントリを作成、更新、取得、および削除することができます。<br/><br/>[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)
Oracle Database | Oracle Database コネクタを使用すると、オンプレミスの Oracle Database に接続して、  データベース テーブルのエントリを作成、更新、取得、削除できます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
POP3<br/>IMAP | POP3 (Post Office Protocol) は、電子メール クライアントがメール サーバーから電子メールを取得するために使用するプロトコルです。POP3 コネクタは、POP3 サーバーに接続します。このコネクタには、添付ファイルを含む電子メールを取得するためのトリガーが含まれています。<br/><br/>[プロトコル コネクタ](app-service-logic-protocol-connectors.md)
QuickBooks | QuickBooks コネクタを使用すると、Intuit QuickBooks のさまざまなエンティティ (顧客、項目、請求書など) の作成、更新、読み取り、削除、照会などの操作を実行できます。<br/><br/>[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)
Salesforce | Salesforce コネクタは、Salesforce アカウントのさまざまなエンティティ (アカウント、潜在顧客、営業案件、事例など) を管理します。さらに、さまざまなエンティティの作成、更新、upsert、照会、削除などの操作を実行できます。<br/><br/>[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)
SAP | SAP コネクタは、オンプレミスの SAP サーバーに接続し、RFC、BAPI、および tRFC を呼び出して、IDOC を送信します。<br/><br/>[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)
SFTP | SFTP (SSH ファイル転送プロトコル) は、ファイルを安全に転送するために使用される一般的なプロトコルです。SFTP コネクタを使用すると、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。<br/><br/>[プロトコル コネクタ](app-service-logic-protocol-connectors.md)
SMTP | SMTP (簡易メール転送プロトコル) は、メール クライアントとメール サーバーの間で電子メールを送信するために使用されるプロトコルです。SMTP コネクタは、SMTP サーバーに接続して、添付ファイルを含む電子メールを送信します。<br/><br/>[プロトコル コネクタ](app-service-logic-protocol-connectors.md)
SugarCRM | SugarCRM コネクタを使用すると、SugarCRM Online に接続して、さまざまな種類のモジュール (アカウント、連絡先、製品など) の作成、更新、読み取り、削除などの操作を実行できます。<br/><br/>[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)
Twilio | Twilio は、通信指向の SaaS サービスです。Twilio コネクタを使用すると、Twilio に接続して、メッセージの送信、メッセージの取得、メッセージの一覧の取得、使用状況の一覧の取得、使用可能な無料通話番号の取得、使用可能な携帯電話番号の取得、使用可能な市内番号の取得、着信電話番号の一覧の取得、着信電話番号の取得、着信電話番号の追加などの操作を行うことができます。<br/><br/>[ソーシャル コネクタ](app-service-logic-social-connectors.md)
Twitter | Twitter は、140 文字までの短いメッセージをやり取りできるオンライン ソーシャル ネットワーキング サービスです。Twitter コネクタを使用すると、Twitter に接続して、ユーザー タイムラインの取得、ツイートの検索、フォロワーの取得、友人の取得、ユーザーの検索、ホーム タイムラインの取得、メンション タイムラインの取得、ツイートの投稿、ユーザーへのツイートの投稿、ダイレクト メッセージの送信などの操作を実行できます。さらに、Twitter コネクタでは、キーワードによるツイートの取得、ユーザー ハンドルによるツイートの取得、ハッシュタグによるツイートの取得などのトリガーも使用します。<br/><br/>[ソーシャル コネクタ](app-service-logic-social-connectors.md)


## その他のコネクタ

[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)<br/>
[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)<br/>
[企業間コネクタ](app-service-logic-b2b-connectors.md)<br/>
[ソーシャル コネクタ](app-service-logic-social-connectors.md)<br/>
[プロトコル コネクタ](app-service-logic-protocol-connectors.md)<br/>
[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)

<!--HONumber=49-->