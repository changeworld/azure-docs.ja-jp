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
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Microsoft Azure App Service のコネクタと API Apps の一覧
このトピックでは、マイクロソフトによって作成された使用可能なすべてのコネクタと API アプリを示します。

料金情報および各サービス レベルに含まれる新機能の一覧については、[Azure のアプリ サービスの料金](http://azure.microsoft.com/pricing/details/app-service/)を参照してください。


## Standard コネクタ
次の表に、Standard コネクタと使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API アプリを示します。

<table border="1">
<tr bgcolor="FAF9F9">
        <th>名前</th>
        <th>説明</th>
</tr>

<tr>
<td colspan="2"><strong>アプリ + データ サービス コネクタ</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/">アプリ + データ サービス コネクタ</a>に関するページで、これらのコネクタに関する詳細情報を提供しています。</td>
</tr>

<tr>
<td>Azure HDInsight</td>
<td>Azure HDInsight は、Apache Hadoop クラスターをクラウドにデプロイしてプロビジョニングするためのサービスです。ビッグ データの管理、分析、レポート生成を支援するソフトウェア フレームワークとなります。このコネクタは、Azure に Hadoop クラスターを作成し、Hive、Pig、MapReduce、ストリーミング MapReduce などの異なる Hadoop ジョブを送信します。また、このコネクタを使用して、クラスターのスピン、ジョブの送信、ジョブの完了待機を実行できます。</td>
</tr>

<tr>
<td>Azure のサービス バス</td>
<td>Azure Service Bus は、あらゆるものを接続するための、汎用のクラウド ベースのメッセージング システムです。Azure Service Bus では、拡張性と信頼性を高めるために、疎結合方式でメッセージを交換できます。このコネクタを使用すると、キュー、トピックなどの Service Bus エンティティからメッセージを送信したり、キュー、サブスクリプションなどの Service Bus エンティティからメッセージを受信したりできます。</td>
</tr>

<tr>
<td>Azure Storage BLOB</td>
<td>Azure Storage BLOB は、テキストやバイナリ データなどの大量の非構造化データを格納します。これらのデータには、世界中のどこからでもアクセスできます。このコネクタを使用すると、BLOB ストレージに接続して、BLOB のアップロード、BLOB の取得、BLOB の削除、コンテナー内の BLOB の一覧の取得、BLOB のスナップショットの作成、BLOB のコピーなどの操作を実行できます。さらに、トリガーを使用して BLOB を取得することができます。</td>
</tr>

<tr>
<td>Box</td>
<td>Box は、ファイル共有サービスです。Box コネクタを使用すると、Box に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。</td>
</tr>

<tr>
<td>Dropbox</td>
<td>Dropbox は、ファイル ホスティング サービスです。このコネクタを使用すると、Dropbox に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。</td>
</tr>

<tr>
<td>Microsoft Office 365</td>
<td>Office 365 コネクタを使用すると、電子メールの送受信、予定表の管理などの操作を実行できます。さらに、Office 365 アカウントを使用して連絡先を管理することができます。さらに、電子メールの送受信と取得、予定表のイベントの作成と削除、連絡先の作成、更新、取得、削除などの操作を実行できます。</td>
</tr>

<tr>
<td>Microsoft OneDrive</td>
<td>Microsoft OneDrive は、ファイル ホスティング サービスです。OneDrive コネクタを使用すると、個人用の Microsoft OneDrive に接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>Microsoft SharePoint コネクタを使用すると、オンプレミスの Microsoft SharePoint Server または SharePoint Online に接続して、ドキュメントやリスト項目を管理できます。さらに、ドキュメントやリスト項目を作成、更新、取得、削除することもできます。既定の資格情報、OAuth 2.0、Windows 認証、およびフォームベース認証などのさまざまな認証方法がサポートされています。</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>Microsoft SQL コネクタは、オンプレミスの SQL Server または Azure SQL Database に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除できます。</td>
</tr>

<tr>
<td colspan="2"><strong>ソーシャル コネクタ</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">ソーシャル コネクタ</a>に関するページで、これらのコネクタに関する詳細情報を提供しています。</td>
</tr>

<tr>
<td>Chatter</td>
<td>Chatter は、エンタープライズ向けのソーシャル ネットワークです。Chatter コネクタを使用すると、Chatter に接続して、メッセージの投稿、検索などの操作を実行できます。さらに、トリガーを使用して新しいメッセージを取得することができます。</td>
</tr>

<tr>
<td>Facebook</td>
<td>Facebook は、ソーシャル ネットワーキング サービスです。Facebook コネクタを使用すると、Facebook に接続して、メッセージ、ビデオ、写真、オファーなどを発行することができます。さらに、メッセージ、コメント、イベント、ユーザーのフィードや、本、ゲーム、映画などのユーザーの好みに関する情報を取得することもできます。このコネクタには、ページに投稿された新しいメッセージを取得するためのトリガーも含まれています。</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>Microsoft Yammer は、エンタープライズ向けのソーシャル ネットワークです。Yammer コネクタは、Yammer に接続します。このコネクタには、メッセージの投稿アクションや新しいメッセージを取得するトリガーが含まれます。</td>
</tr>

<tr>
<td>Twilio</td>
<td>Twilio は、通信指向の SaaS サービスです。Twilio コネクタを使用すると、Twilio に接続して、メッセージの送信、メッセージの取得、メッセージの一覧の取得、使用状況の一覧の取得、使用可能な無料通話番号の取得、使用可能な携帯電話番号の取得、使用可能な市内番号の取得、着信電話番号の一覧の取得、着信電話番号の取得、着信電話番号の追加などの操作を行うことができます。</td>
</tr>

<tr>
<td>Twitter</td>
<td>Twitter は、140 文字までの短いメッセージをやり取りできるオンライン ソーシャル ネットワーキング サービスです。Twitter コネクタを使用すると、Twitter に接続して、ユーザー タイムラインの取得、ツイートの検索、フォロワーの取得、友人の取得、ユーザーの検索、ホーム タイムラインの取得、メンション タイムラインの取得、ツイートの投稿、ユーザーへのツイートの投稿、ダイレクト メッセージの送信などの操作を実行できます。さらに、Twitter コネクタでは、キーワードによるツイートの取得、ユーザー ハンドルによるツイートの取得、ハッシュタグによるツイートの取得などのトリガーも使用します。</td>
</tr>

<tr>
<td colspan="2"><strong>プロトコル コネクタ</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">プロトコル コネクタ</a>に関するページで、これらのコネクタに関する詳細情報を提供しています。</td>
</tr>

<tr>
<td>ファイル</td>
<td>ファイル コネクタを使用すると、ファイル システムまたはネットワークとの間でファイルの送信またはアップロード、またはファイルの受信またはダウンロードを行うことができます。このコネクタを使用すると、オンプレミスのファイル サーバーに接続して、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。</td>
</tr>

<tr>
<td>FTP<br/>FTPS</td>
<td>FTP (ファイル転送プロトコル) は、ファイルをホスト間で転送するために使用される一般的なネットワーク プロトコルです。FTP コネクタを使用すると、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。</td>
</tr>

<tr>
<td>HTTP リスナー</td>
<td>HTTP リスナーは、HTTP サーバーとして機能するエンドポイントを開き、着信 HTTP 要求をリッスンします。</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>POP3 (Post Office Protocol) は、電子メール クライアントがメール サーバーから電子メールを取得するために使用するプロトコルです。POP3 コネクタは、POP3 サーバーに接続します。このコネクタには、添付ファイルを含む電子メールを取得するためのトリガーが含まれています。</td>
</tr>

<tr>
<td>SFTP</td>
<td>SFTP (SSH ファイル転送プロトコル) は、ファイルを安全に転送するために使用される一般的なプロトコルです。SFTP コネクタを使用すると、ファイルのアップロード、ファイルの取得、ファイルの削除、ファイルの一覧の取得などの操作を実行できます。さらに、トリガーを使用してファイルを取得することができます。</td>
</tr>

<tr>
<td>SMTP</td>
<td>SMTP (簡易メール転送プロトコル) は、メール クライアントとメール サーバーの間で電子メールを送信するために使用されるプロトコルです。SMTP コネクタは、SMTP サーバーに接続して、添付ファイルを含む電子メールを送信します。</td>
</tr>

<tr>
<td colspan="2"><strong>エンタープライズ コネクタ</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">エンタープライズ コネクタ</a>に関するページで、これらのコネクタに関する詳細情報を提供しています。</td>
</tr>

<tr>
<td>Marketo</td>
<td>Marketo は、受信マーケティング、ソーシャル マーケティング、CRM、およびその他の関連サービスを提供する自動マーケティング ソフトウェアを開発しています。Marketo コネクタを使用すると、Marketo に接続して、潜在顧客の作成および更新、潜在顧客の変更の取得、キャンペーンのスケジュール設定、キャンペーンの要求、潜在顧客の取得、キャンペーンまたは一覧の情報の取得、一覧への潜在顧客の追加、一覧からの潜在顧客の削除などの操作を実行できます。</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>QuickBooks コネクタを使用すると、Intuit QuickBooks のさまざまなエンティティ (顧客、項目、請求書など) の作成、更新、読み取り、削除、照会などの操作を実行できます。</td>
</tr>

<tr>
<td>Salesforce</td>
<td>Salesforce コネクタは、Salesforce アカウントのさまざまなエンティティ (アカウント、潜在顧客、営業案件、事例など) を管理します。さらに、さまざまなエンティティの作成、更新、upsert、照会、削除などの操作を実行できます。</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>SugarCRM コネクタを使用すると、SugarCRM Online に接続して、さまざまな種類のモジュール (アカウント、連絡先、製品など) の作成、更新、読み取り、削除などの操作を実行できます。</td>
</tr>

</table>


## BizTalk EAI の API アプリ
次の表に、マイクロソフトによって作成された使用可能なすべての API アプリを示します。これらは BizTalk EAI Services で使用できます。

	Name  | Description
------------- | -------------
BizTalk Flat File Encoder | フラット ファイル データ (Excel、csv など) と XML データ間の相互運用性を提供します。この API アプリによって、フラット ファイル インスタンスを XML に、またはその逆方向に変換できます。
BizTalk Transform Service | BizTalk Transform API は、データの形式を変換します。また、既存のマップ (.trfm ファイル) のアップロード、ソース スキーマとターゲット スキーマ間のリンクを表示するマップのグラフィカル表示などの操作を実行できます。さらに、サンプルの入力 XML コンテンツを使用した "テスト" 機能を使用することができます。文字列操作、条件付き割り当て、算術式、日付時刻フォーマッタ、ループなどの各種の組み込み関数も用意されています。
BizTalk XML Validator | Validator API アプリは、定義済みの XML スキーマに対して XML データを検証します。既存のスキーマを使用することも、フラット ファイルのインスタンス、JSON のインスタンス、または既存のコネクタに基づいてスキーマを生成することもできます。
BizTalk XPath Extractor | Extractor API アプリは、特定の XPath に基づいて XML コンテンツを検索し、データを抽出します。

その他の情報とこれらの API アプリの詳細については、[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)に関するページを参照してください。


## BizTalk EDI コネクタと API Apps
次の表に、BizTalk EDI Services で使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API アプリを示します。

	Name  | Description
------------- | -------------
AS2 コネクタ | AS2 コネクタは、企業間の通信において AS2 トランスポート プロトコルを使用してメッセージを送受信できます。データは、デジタル証明書と暗号化を使用してインターネット経由で安全かつ確実に転送されます。
BizTalk EDIFACT | EDIFACT API アプリは、企業間通信で EDIFACT プロトコルを使用して、メッセージを送受信します。
BizTalk X12 | BizTalk X12 | X12 API アプリは、企業間通信で X12 プロトコルを使用して、メッセージを送受信します。
BizTalk 取引先管理 | 取引先管理 API アプリは、パートナー、契約、および契約で使用されるスキーマと証明書を使用して、企業間リレーションシップを定義および保持します。これらのリレーションシップは、AS2、EDIFACT、および X 12 API アプリを使用して適用されます。

その他の情報とこれらの API アプリの詳細については、[企業間コネクタ](app-service-logic-b2b-connectors.md)に関するページを参照してください。


## BizTalk ルールの API アプリ
次の表に、マイクロソフトによって作成された使用可能なすべての API アプリを示します。これらは BizTalk ルールで使用できます。

	Name  | Description
------------- | -------------
BizTalk ルール | BizTalk ルールは、組織の構造、運用、および戦略を定義および制御します。ビジネス ポリシーは、関連付けられているアプリケーションを再コンパイルして再デプロイすることなく更新できます。

その他の情報とこの API アプリの詳細については、[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)に関するページを参照してください。


## Premium コネクタ
次の表に、Premium コネクタで使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API アプリを示します。

	Name  | Description
------------- | -------------
DB2 コネクタ | DB2 コネクタは、Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM DB2 データベースに接続します。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Informix | Informix コネクタは、Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM Informix データベースに接続します。Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
MQ | MQ コネクタは、Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM WebSphere MQ Server に接続します。<br/><br/><strong>注:</strong> 現在 Logic Apps で使用することはできません。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
Oracle Database | Oracle Database コネクタを使用すると、オンプレミスの Oracle Database に接続して、データベース テーブルのエントリを作成、更新、取得、削除できます。<br/><br/>[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)
SAP | SAP コネクタは、オンプレミスの SAP サーバーに接続し、RFC、BAPI、および tRFC を呼び出して、IDOC を送信します。<br/><br/>[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)


## 独自のコネクタと API アプリの作成
「[REST API でのコネクタの作成](http://go.microsoft.com/fwlink/p/?LinkId=529766)」を参照してください。


## その他のコネクタ

[BizTalk 統合コネクタ](app-service-logic-integration-connectors.md)<br/> [エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)<br/> [企業間コネクタ ソーシャル コネクタ](app-service-logic-b2b-connectors.md)<br/> [ソーシャル コネクタ](app-service-logic-social-connectors.md)<br/> [プロトコル コネクタ](app-service-logic-protocol-connectors.md)<br/> [App + データ サービス コネクタ](app-service-logic-data-connectors.md)<br/><br/> [コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->