---
title: Azure Logic Apps のコネクタ
description: Azure Logic Apps のコネクタ (組み込み、マネージド、オンプレミス、統合アカウント、ISE、エンタープライズ コネクタなど) を使用してワークフローを自動化する
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: f2a2ee7a2806a753ffd159c91ed782634e74c704
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838669"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps のコネクタ

コネクタは、Azure Logic Apps からその他のアプリ、サービス、システム、プロトコル、およびプラットフォームでの、イベント、データ、およびアクションへのすばやいアクセスを提供します。 ロジック アプリでコネクタを使用して、クラウドおよびオンプレミス アプリの機能を拡張すると、タスクの実行時に、作成したデータや既に所有しているデータを使用できます。

Logic Apps には[数百のコネクタ](https://docs.microsoft.com/connectors)が用意されていますが、この記事では、*人気が高く、より多く使用されている*コネクタについて説明します。これらのコネクタは、データと情報を処理するために、数千のアプリと数百万の実行で正常に使用されています。 すべてのコネクタと、トリガー、アクション、制限など各コネクタの参照情報については、[コネクタの概要](https://docs.microsoft.com/connectors)に関するページにあるコネクタ参照ページをご覧ください。 また、[トリガーとアクション](#triggers-actions)、[Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)、[Logic Apps の価格の詳細](https://azure.microsoft.com/pricing/details/logic-apps/)も確認してください。

> [!TIP]
> コネクタがないサービスまたは API を統合するには、HTTP などのプロトコル経由で直接サービスを呼び出すか、[カスタム コネクタ](#custom)を作成します。

## <a name="connector-types"></a>コネクタの種類

コネクタは、組み込みトリガーおよびアクションとして、またはマネージド コネクタとして利用できます。

<a name="built-in"></a>

* [**組み込み**](#built-ins):組み込みトリガーおよびアクションは、Azure Logic Apps に "ネイティブ" であり、ロジック アプリに対して次のタスクを実行するのに役立ちます。

  * カスタムおよび詳細なスケジュールで実行します。

  * ロジック アプリのワークフロー (ループや条件など) を整理および制御し、さらに変数とデータ操作を行います。

  * 他のエンドポイントと通信します。

  * 要求を受信して応答します。

  * Azure 関数、Azure API Apps (Web Apps)、Azure API Management を使用して管理および公開する独自の API、および要求を受け取ることができる入れ子にしたロジック アプリを呼び出します。

<a name="managed-connectors"></a>

* [**マネージド コネクタ**](#managed-api-connectors):Microsoft によってデプロイおよび管理されるこれらのコネクタでは、Office 365、Azure Blob Storage、SQL Server、Dynamics、Salesforce、SharePoint など、クラウド サービスとオンプレミス システムの一方または両方にアクセスするためのトリガーとアクションが提供されます。 一部のコネクタでは特に企業間 (B2B) 通信シナリオがサポートされており、ロジック アプリにリンクされた[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)が必要です。 特定のコネクタを使う前にまず、Azure Logic Apps によって管理される接続を作成することが必要な場合があります。

  たとえば、Microsoft BizTalk Server を使っている場合、ロジック アプリでは、[BizTalk Server オンプレミス コネクタ](#on-premises-connectors)を使って BizTalk Server に接続し、通信を行うことができます。 [統合アカウント コネクタ](#integration-account-connectors)を使用すると、ロジック アプリで BizTalk のような操作を拡張または実行できます。

  コネクタは、標準またはエンタープライズのいずれかとして分類されます。 [エンタープライズ コネクタ](#enterprise-connectors) では、追加コストを払うことで、SAP、IBM MQ、IBM 3270 などのエンタープライズ システムへのアクセスが提供されます。 コネクタが標準かエンタープライズかを判断するには、[コネクタの概要](https://docs.microsoft.com/connectors)に関する記事で示されている各コネクタのリファレンス ページにある技術的な詳細をご覧ください。

  これらのカテゴリを使ってコネクタを識別することもできますが、一部のコネクタは複数のカテゴリに属している場合があります。 たとえば、SAP はエンタープライズ コネクタでもありオンプレミス コネクタでもあります。

  |   |   |
  |---|---|
  | [**マネージド コネクタ**](#managed-api-connectors) | Azure Blob Storage、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online などのサービスを使用するロジック アプリを作成します。 |
  | [**オンプレミス コネクタ**](#on-premises-connectors) | [オンプレミス データ ゲートウェイ][gateway-doc]をインストールして設定した後、これらのコネクタを使用すると、ロジック アプリは、SQL Server、SharePoint Server、Oracle DB、ファイル共有などのオンプレミス システムにアクセスできます。 |
  | [**統合アカウント コネクタ**](#integration-account-connectors) | 統合アカウントを作成して料金を支払うと使用可能になります。これらのコネクタは、XML を変換および検証したり、フラット ファイルをエンコードおよびデコードしたり、AS2、EDIFACT、および X12 プロトコルを使用して企業間 (B2B) メッセージを処理したりします。 |
  |||

  > [!IMPORTANT]
  > Gmail コネクタを使用する場合、ロジック アプリで制限なしにこのコネクタを使用できるのは、G-Suite ビジネス アカウントだけです。 Gmail コンシューマー アカウントを持っている場合は、Google によって承認された特定のサービスのみでこのコネクタを使用できるほか、[認証に使用する Google クライアント アプリを Gmail コネクタで作成する](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)ことができます。 詳細については、[Azure Logic Apps での Google コネクタのデータ セキュリティとプライバシー ポリシー](../connectors/connectors-google-data-security-privacy-policy.md)に関する記事を参照してください。

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>統合サービス環境から接続する

Azure 仮想ネットワーク内のリソースに直接アクセスする必要があるロジック アプリの場合は、専用のリソースでロジック アプリをビルド、デプロイ、および実行できる、分離された[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成できます。 ロジック アプリ デザイナーで、ISE 内でロジック アプリに使用するコネクタを参照する場合、組み込みトリガーおよびアクションに **CORE** ラベルが表示されますが、一部のコネクタには **ISE** ラベルが表示されます。

* **CORE**:このラベルが付いた組み込みトリガーおよびアクションは、ロジック アプリと同じ ISE 内で動作します。次に例を示します。

  ![ISE コネクタの例](./media/apis-list/example-core-connector.png)

* **ISE**:このラベルが付いたマネージド コネクタは、ロジック アプリと同じ ISE 内で動作します。次に例を示します。

  ![ISE コネクタの例](./media/apis-list/example-ise-connector.png)

  Azure 仮想ネットワークに接続されているオンプレミス システムがある場合、ISE を使用すると、ロジック アプリで[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を使用せずに、そのシステムに直接アクセスすることができます。 代わりに、そのシステムの **ISE** コネクタ (使用可能な場合)、HTTP アクション、または[カスタム コネクタ](#custom)を使用できます。 **ISE** コネクタがないオンプレミス システムの場合は、オンプレミスのデータ ゲートウェイを使用します。 使用可能な ISE コネクタを確認するには、「[ISE コネクタ](#ise-connectors)」を参照してください。

* **CORE** または **ISE** ラベルのない他のすべてのコネクタは、引き続き使用でき、グローバルなマルチテナント Logic Apps サービスで実行できます。次に例を示します。

  ![マルチテナント コネクタの例](./media/apis-list/example-multi-tenant-connector.png)

ISE 実行されるロジック アプリとそのコネクタは、これらのコネクタが実行されている場所には関係なく、固定価格プランまたは使用量ベースの価格プランに従います。 詳細については、次のページを参照してください。

* [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)
* [Logic Apps の価格の詳細](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>組み込み

Logic Apps は、組み込みトリガーおよびアクションを備えているため、スケジュールベースのワークフローを作成し、ロジック アプリで他のアプリやサービスと通信することができます。また、ロジック アプリでワークフローを制御し、データの管理や操作もできます。

|   |   |   |   |
|---|---|---|---|
| [![API アイコン][schedule-icon]<br>**スケジュール**][schedule-doc] | - [**繰り返し**トリガー][schedule-recurrence-doc]を使用して、指定した繰り返しでロジック アプリを実行します。基本的なスケジュールから詳細なスケジュールまで指定できます。 <p>- [**スライディング ウィンドウ** トリガー][schedule-sliding-window-doc]を使用して、連続したチャンクのデータを処理する必要があるロジック アプリを実行します。 <p>- [**遅延**アクション][schedule-delay-doc]を使用して、指定した期間、ロジック アプリを一時停止します。 <p>- [**延期期限**アクション][schedule-delay-until-doc]を使用して、指定した日時までロジック アプリを一時停止します。 | [![API アイコン][batch-icon]<br>**バッチ**][batch-doc] | - **[メッセージのバッチ処理]** トリガーを使用して、メッセージをバッチで処理します。 <p>- **[バッチへのメッセージの送信]** アクションを使用して、既存のバッチ トリガーを持つロジック アプリを呼び出します。 |
| [![API アイコン][http-icon]<br>**HTTP**][http-doc] | HTTP のトリガーとアクションを使用して HTTP または HTTPS エンドポイントを呼び出します。 その他の HTTP 組み込みトリガーおよびアクションには [HTTP + Swagger][http-swagger-doc] と [HTTP + Webhook][http-webhook-doc] が含まれます。 | [![API アイコン][http-request-icon]<br>**要求**][http-request-doc] | - 他のアプリやサービスからロジック アプリを呼び出し可能にしたり、Event Grid リソース イベントでロジック アプリをトリガーしたり、 **[要求]** トリガーによる Azure Security Center アラートへの応答でロジック アプリをトリガーしたりします。 <p>- **[応答]** アクションを使用して、アプリやサービスに応答を送信します。 |
| [![API アイコン][azure-api-management-icon]<br>**Azure API<br>Management**][azure-api-management-doc] | Azure API Management で管理および発行する独自の API によって定義されたトリガーとアクションを呼び出します。 | [![API アイコン][azure-app-services-icon]<br>**Azure App <br>Services**][azure-app-services-doc] | Azure App Service でホストされている Azure API アプリまたは Web アプリを呼び出します。 Swagger が含まれている場合、これらのアプリで定義されているトリガーとアクションは、他のファースト クラスのトリガーおよびアクションのように表示されます。|
| [![API アイコン][azure-logic-apps-icon]<br>**Azure Logic <br>Apps**][nested-logic-app-doc] | **要求**トリガーで開始するその他のロジック アプリを呼び出します。 |
|||||

### <a name="run-code-from-logic-apps"></a>ロジック アプリからコードを実行する

Logic Apps には、ロジック アプリのワークフローで独自のコードを実行するための組み込みアクションが用意されています。

|   |   |   |   |
|---|---|---|---|
| [![API アイコン][azure-functions-icon]<br>**Azure Functions**][azure-functions-doc] | カスタム コード スニペット (C# または Node.js) をロジック アプリから実行する Azure 関数を呼び出します。 | [![API アイコン][inline-code-icon]<br>**インライン コード**][azure-functions-doc] | ロジック アプリから JavaScript コード スニペットを追加して実行します。 |
|||||

### <a name="control-workflow"></a>ワークフローを制御する

Logic Apps では、ロジック アプリのワークフローでアクションを構成および制御するための組み込みアクションが提供されています。

|   |   |   |   |
|---|---|---|---|
| [![組み込みアイコン][condition-icon]<br>**条件**][condition-doc] | 条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。 | [![組み込みアイコン][for-each-icon]<br>**For each**][for-each-doc] | 配列内のすべての項目に対して同じアクションを実行します。 |
| [![組み込みアイコン][scope-icon]<br>**スコープ**][scope-doc] | アクションを*スコープ*にグループ化します。スコープ内のアクションが実行を完了すると、スコープ独自のステータスが取得されます。 | [![組み込みアイコン][switch-icon]<br>**スイッチ**][switch-doc] | アクションを*ケース*にグループ化します。既定のケースを除き、ケースには、一意の値が割り当てられます。 割り当てられた値が式、オブジェクト、またはトークンの結果に一致するケースのみを実行します。 一致が存在しない場合は、既定のケースを実行します。 |
| [![組み込みアイコン][terminate-icon]<br>**終了**][terminate-doc] | アクティブに実行中のロジック アプリ ワークフローを停止します。 | [![組み込みアイコン][until-icon]<br>**期限**][until-doc] | 指定された条件が true になるまで、または特定の状態が変化するまで、アクションを繰り返します。 |
|||||

### <a name="manage-or-manipulate-data"></a>データを管理または操作する

Logic Apps では、データ出力とその形式を操作するための組み込みアクションが提供されています。

|   |   |
|---|---|
| [![組み込みアイコン][data-operations-icon]<br>**データの操作**][data-operations-doc] | データの操作を実行します。 <p>- **作成**:さまざまな型の複数の入力から単一の出力を作成します。 <br>- **CSV テーブルの作成**:JSON オブジェクトの配列からコンマ区切り値 (CSV) テーブルを作成します。 <br>- **HTML テーブルの作成**:JSON オブジェクトの配列から HTML テーブルを作成します。 <br>- **配列のフィルター処理**:別の配列内にある、条件を満たす項目から配列を作成します。 <br>- **結合**:配列内のすべての項目から 1 つの文字列を作成し、それらの項目を指定の区切り記号で区切ります。 <br>- **JSON の解析**:JSON コンテンツ内のプロパティとその値からわかりやすいトークンを作成して、ユーザーがそれらのプロパティをワークフロー内で使用できるようにします。 <br>- **選択**:別の配列内の項目または値を変換し、それらの項目を指定のプロパティにマッピングすることにより、JSON オブジェクトで配列を作成します。 |
| ![組み込みアイコン][date-time-icon]<br>**日付/時刻** | タイムスタンプを使用して操作を実行します。 <p>- **時間への追加**:指定した数の単位をタイムスタンプに追加します。 <br>- **タイム ゾーンの変換**:タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。 <br>- **現在の時刻**:現在のタイムスタンプを文字列として返します。 <br>- **未来の時間の取得**:現在のタイムスタンプに指定した時刻単位を加えて返します。 <br>- **過去の時間の取得**:現在のタイムスタンプから指定した時刻単位を引いて返します。 <br>- **時間からの減算**:タイムスタンプから時間単位数を減算します。 |
| [![組み込みアイコン][variables-icon]<br>**変数**][variables-doc] | 変数の操作を実行します。 <p>- **配列変数に追加**:変数によって保管される配列内に、値を最後の項目として挿入します。 <br>- **文字列変数に追加**:変数によって保管される文字列内に、値を最後の文字として挿入します。 <br>- **変数の値を減らす**:変数の値を一定値だけ減らします。 <br>- **変数の値を増やす**:変数の値を一定値だけ増やします。 <br>- **変数を初期化する**:変数を作成し、そのデータ型と初期値を宣言します。 <br>- **変数の設定**:既存の変数に異なる値を代入します。 |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>マネージド コネクタ

Logic Apps には、次のサービスまたはシステムでタスク、プロセス、およびワークフローを自動化するためによく使用される標準コネクタが用意されています。

|   |   |   |   |
|---|---|---|---|
| [![API アイコン][azure-service-bus-icon]<br>**Azure Service Bus**][azure-service-bus-doc] | Logic Apps で最もよく使用されているコネクタを使用して非同期メッセージ、セッション、およびトピック サブスクリプションを管理します。 | [![API アイコン][sql-server-icon]<br>**SQL Server**][sql-server-doc] | オンプレミスの SQL Server、またはクラウド内の Azure SQL Database に接続して、レコードの管理、ストアド プロシージャの実行、クエリの実行を行えるようにします。 |
| [![API アイコン][azure-blob-storage-icon]<br>**Azure Blob<br>Storage**][azure-blob-storage-doc] | ストレージ アカウントに接続して、BLOB コンテンツの作成と管理ができるようにします。 | [![API アイコン][office-365-outlook-icon]<br>**Office 365<br>Outlook**][office-365-outlook-doc] | Office 365 メール アカウントに接続して、メール、タスク、カレンダー イベントおよび会議、連絡先、要求などの作成と管理ができるようにします。 |
| [![API アイコン][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | SSH を使用してインターネットからアクセス可能な SFTP サーバーに接続して、ファイルとフォルダーを操作できるようにします。 | [![API アイコン][sharepoint-online-icon]<br>**SharePoint<br>Online**][sharepoint-online-doc] | SharePoint Online に接続して、ファイル、添付ファイル、フォルダーなどを管理できるようにします。 | 
| [![API アイコン][dynamics-365-icon]<br>**Dynamics 365<br>** ][dynamics-365-doc] | Dynamics 365 アカウントに接続して、レコードや項目などの作成と管理ができるようにします。 | [![API アイコン][azure-queues-icon]<br>**Azure <br>キュー**][azure-queues-doc] | Azure Storage アカウントに接続して、キューとメッセージの作成と管理ができるようにします |
| [![API アイコン][ftp-icon]<br>**FTP**][ftp-doc] | インターネットからアクセス可能な FTP サーバーに接続して、ファイルとフォルダーを操作できるようにします。 | [![API アイコン][file-system-icon]<br>**ファイル <br>システム**][file-system-doc] | オンプレミスのファイル共有に接続して、ファイルの作成と管理ができるようにします。 |
| [![API アイコン][azure-event-hubs-icon]<br>**Azure Event Hubs**][azure-event-hubs-doc] | イベント ハブを通じてイベントを使用したり、発行したりします。 たとえば、ロジック アプリから Event Hubs を使用して出力を取得し、リアルタイム分析プロバイダーに送信できます。 | [![API アイコン][azure-event-grid-icon]<br>**Azure Event**<br>**Grid**][azure-event-grid-doc] | Azure やサードパーティのリソースが変更されたときなどに、Event Grid が発行するイベントを監視します。 |
| [![API アイコン][salesforce-icon]<br>**Salesforce**][salesforce-doc] | Salesforce アカウントに接続して、レコード、ジョブ、オブジェクトなどの項目の作成と管理ができるようにします。 | [![API アイコン][twitter-icon]<br>**Twitter**][twitter-doc] | Twitter アカウントに接続して、ツイート、フォロワー、タイムラインなどを管理できるようにします。 ツイートは、SQL、Excel、または SharePoint に保存します。 |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>オンプレミス コネクタ

ここでは、オンプレミス システムのデータやリソースにアクセスするために Logic Apps で提供されていてよく使用される標準コネクタを紹介します。 オンプレミス システムへの接続を作成するには、最初に[オンプレミス データ ゲートウェイのダウンロード、インストール、設定][gateway-doc]を行う必要があります。 このゲートウェイは、セキュリティで保護された通信チャネルを提供します。必要なネットワーク インフラストラクチャを設定する必要はありません。

|   |   |   |   |   |
|---|---|---|---|---|
| [![API アイコン][biztalk-server-icon]<br>**BizTalk** <br>**Server**][biztalk-server-doc] | [![API アイコン][file-system-icon]<br>**ファイル <br>システム**][file-system-doc] | [![API アイコン][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc] | [![API アイコン][ibm-informix-icon]<br>**IBM** <br>**Informix**][ibm-informix-doc] | [![API アイコン][mysql-icon]<br>**MySQL**][mysql-doc] |
| [![API アイコン][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc] | [![API アイコン][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc] | [![API アイコン][sharepoint-server-icon]<br>**SharePoint <br>Server**][sharepoint-server-doc] | [![API アイコン][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [![API アイコン][teradata-icon]<br>**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>統合アカウント コネクタ

Logic Apps では、Azure の Enterprise Integration Pack (EIP) で利用可能な[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を作成して料金を支払うときに、ロジック アプリで企業間 (B2B) ソリューションを作成するための標準コネクタが提供されています。 このアカウントを使用すると、パーティー、取引先契約、マップ、スキーマ、証明書などの B2B 成果物を作成したり、保管したりできます。 これらの成果物を使用するには、ロジック アプリを統合アカウントに関連付けます。 現在、BizTalk Server を使用している場合は、これらのコネクタが既になじみがあるように見えるかもしれません。

|   |   |   |   |
|---|---|---|---|
| [![API アイコン][as2-icon]<br>**AS2 <br>のデコード**][as2-doc] | [![API アイコン][as2-icon]<br>**AS2 <br>のエンコード**][as2-doc] | [![API アイコン][edifact-icon]<br>**EDIFACT <br>のデコード**][edifact-decode-doc] | [![API アイコン][edifact-icon]<br>**EDIFACT <br>のエンコード**][edifact-encode-doc] |
| [![API アイコン][flat-file-decode-icon]<br>**フラット ファイル<br>のデコード**][flat-file-decode-doc] | [![API アイコン][flat-file-encode-icon]<br>**フラット ファイル<br>のエンコード**][flat-file-encode-doc] | [![API アイコン][integration-account-icon]<br>**統合<br>アカウント**][integration-account-doc] | [![API アイコン][liquid-icon]<br>**Liquid** <br>**変換**][json-liquid-transform-doc] |
| [![API アイコン][x12-icon]<br>**X12 <br>のデコード**][x12-decode-doc] | [![API アイコン][x12-icon]<br>**X12 <br>のエンコード**][x12-encode-doc] | [![API アイコン][xml-transform-icon]<br>**XML** <br>**変換**][xml-transform-doc] | [![API アイコン][xml-validate-icon]<br>**XML <br>検証**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>エンタープライズ コネクタ

Logic Apps では、SAP や IBM MQ などのエンタープライズ システムにアクセスするため、以下のエンタープライズ コネクタが提供されています。

|   |   |   |
|---|---|---|
| [![API アイコン][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [![API アイコン][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc] | [![API アイコン][sap-icon]<br>**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE コネクタ

分離された [統合サービス環境 (ISE)](#integration-service-environment) を作成して実行するロジック アプリの場合、ロジック アプリ デザイナーでは **CORE** ラベルを使用して ISE 内で実行する組み込みトリガーおよびアクションを識別します。 ISE 内で実行するマネージド コネクタには **ISE** ラベルが表示されますが、グローバルなマルチテナント Logic Apps サービスで実行するコネクタには、いずれのラベルも表示されません。 次の一覧は、現在 ISE バージョンがあるコネクタを示しています。

|   |   |   |   |   |
|---|---|---|---|---|
[![API アイコン][as2-icon]<br>**AS2**][as2-doc] | [![API アイコン][azure-automation-icon]<br>**Azure <br>Automation**][azure-automation-doc] | [![API アイコン][azure-blob-storage-icon]<br>**Azure Blob<br>Storage**][azure-blob-storage-doc] | [![API アイコン][azure-cosmos-db-icon]<br>**Azure Cosmos <br>DB**][azure-cosmos-db-doc] | [![API アイコン][azure-event-hubs-icon]<br>**Azure Event <br>Hubs**][azure-event-hubs-doc] |
[![API アイコン][azure-event-grid-icon]<br>**Azure Event <br>Grid**][azure-event-grid-doc] | [![API アイコン][azure-file-storage-icon]<br>**Azure File<br>Storage**][azure-file-storage-doc] | [![API アイコン][azure-key-vault-icon]<br>**Azure Key <br>Vault**][azure-key-vault-doc] | [![API アイコン][azure-monitor-logs-icon]<br>**Azure Monitor <br>ログ**][azure-monitor-logs-doc] | [![API アイコン][azure-service-bus-icon]<br>**Azure Service <br>Bus**][azure-service-bus-doc] |
| [![API アイコン][azure-sql-data-warehouse-icon]<br>**Azure SQL Data <br>Warehouse**][azure-sql-data-warehouse-doc] | [![API アイコン][azure-table-storage-icon]<br>**Azure Table <br>Storage**][azure-table-storage-doc] | [![API アイコン][azure-queues-icon]<br>**Azure <br>キュー**][azure-queues-doc] | [![API アイコン][edifact-icon]<br>**EDIFACT**][edifact-doc] | [![API アイコン][file-system-icon]<br>**ファイル <br>システム**][file-system-doc] |
| [![API アイコン][ftp-icon]<br>**FTP**][ftp-doc] | [![API アイコン][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [![API アイコン][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc] | [![API アイコン][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc] | [![API アイコン][sap-icon]<br>**SAP**][sap-connector-doc] |
| [![API アイコン][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | [![API アイコン][smtp-icon]<br>**SMTP**][smtp-doc] | [![API アイコン][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc] | [![API アイコン][x12-icon]<br>**X12**][x12-doc] |
||||||

詳細については、以下のトピックを参照してください。

* [Azure Logic Apps から Azure Virtual Network リソースにアクセスする](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>トリガーとアクションの種類

コネクタは、*トリガー*、*アクション*、またはその両方を提供できます。 *トリガー*はすべてのロジック アプリの最初の手順であり、通常トリガーを起動し、ロジック アプリの実行を開始するイベントを指定します。 たとえば、FTP コネクタには、"ファイルが追加されるか、変更されたとき" にロジック アプリを起動するトリガーがあります。 一部のトリガーは、指定されたイベントまたはデータを定期的にチェックし、指定されたイベントやデータを検出したときに起動します。 他のトリガーは、待機し、特定のイベントが発生した場合や新しいデータを使用できるようになった場合にただちに起動します。 トリガーは、ロジック アプリに必要なデータも渡します。 ロジック アプリでは、ワークフロー全体でそのデータを読み取り、使用することができます。 たとえば、Twitter コネクタには、ロジック アプリのワークフローにツイートの内容を渡す "新しいツイートが投稿されたら" トリガーがあります。

トリガーの起動後、Azure Logic Apps は、ロジック アプリのインスタンスを作成し、ロジック アプリのワークフローで*アクション*の実行を開始します。 アクションは、ロジック アプリのワークフローでトリガーを追跡し、タスクを実行する手順です。 たとえば、SQL データベースから顧客データを取得し、後のアクションでそのデータを処理するロジック アプリを作成できます。

次に、Azure Logic Apps が提供する一般的な種類のトリガーを示します。

* *繰り返しトリガー*:このトリガーは、指定したスケジュールで実行され、特定のサービスまたはシステムに緊密に関連付けられません。

* *ポーリング トリガー*:このトリガーは、指定したスケジュールに基づいて、特定のサービスまたはシステムを定期的にポーリングし、新しいデータまたは特定のイベントが発生したかどうかをチェックします。 新しいデータが使用可能になるか、または特定のイベントが発生した場合、トリガーはロジック アプリの新しいインスタンスを実行し、それにより、入力として渡されたデータを使用できるようになります。

* *プッシュ トリガー*:このトリガーは、新しいデータまたはイベントの発生を待機し、リッスンします。 新しいデータが使用可能になるか、またはイベントが発生した場合、トリガーはロジック アプリの新しいインスタンスを作成して実行し、それにより、入力として渡されたデータを使用できるようになります。

<a name="connections"></a>

## <a name="connector-configuration"></a>コネクタの構成

各コネクタのトリガーとアクションは、ユーザーが構成できる独自のプロパティを提供します。 多くのコネクタでは、ロジック アプリでトリガーやアクションを使用する前に、最初に対象のサービスまたはシステムへの*接続*を作成し、認証資格情報またはその他の構成の詳細を指定する必要があります。 たとえば、ユーザーに代わってデータにアクセスしたり、投稿したりするために Twitter アカウントへの接続を承認する必要があります。

Azure Active Directory (Azure AD) OAuth を使用するコネクタの場合、接続の作成は、Office 365、Salesforce、GitHub などのサービスへのサインインを意味し、そこでアクセス トークンが[暗号化](../security/fundamentals/encryption-overview.md)され、Azure シークレット ストアに安全に保存されます。 FTP や SQL などのその他のコネクタでは、サーバーのアドレス、ユーザー名、およびパスワードなどの構成の詳細を含む接続が必要です。 これらの接続構成の詳細も暗号化され、安全に保存されます。 [Azure での暗号化](../security/fundamentals/encryption-overview.md)の詳細を参照してください。

接続は、サービスまたはシステムが許可する限り、ターゲットのサービスまたはシステムにアクセスすることができます。 Office 365 や Dynamics など、Azure AD OAuth 接続を使用するサービスについては、アクセス トークンは Azure Logic Apps によって無制限に更新されます。 他のサービスでは、Azure Logic Apps がトークンを更新せずに使用できる期間に制限が設けられる場合があります。 一般に、一部のアクションでは、パスワードの変更などのすべてのアクセス トークンが無効になります。

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>カスタム API とコネクタ

カスタム コードを実行する API や、コネクタとして使用できない API を呼び出すには、[カスタム API Apps を作成](../logic-apps/logic-apps-create-api-app.md)して、Logic Apps プラットフォームを拡張します。 "*任意の*" REST または SOAP ベース API 用の[カスタム コネクタを作成](../logic-apps/custom-connector-overview.md)することもできます。その場合、これらの API は、Azure サブスクリプションの任意のロジック アプリで使用できるようになります。 Azure 内でカスタム API Apps またはコネクタを公開し、誰でも使用できるようにするには、[Microsoft の認定を受けるためにコネクタを提出](../logic-apps/custom-connector-submit-certification.md)してください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) にデプロイして実行したロジック アプリは、Azure 仮想ネットワーク内のリソースに直接アクセスできます。 オンプレミス データ ゲートウェイを必要とするカスタム コネクタを ISE の外部で作成した場合は、ISE 内のロジック アプリでもそれらのコネクタを使用できます。
>
> ISE 内で作成されたカスタム コネクタは、オンプレミス データ ゲートウェイでは動作しません。 ただし、これらのコネクタでは、ISE がホストされている Azure 仮想ネットワークに接続されているオンプレミス データ ソースに直接アクセスできます。 そのため、ISE 内のロジック アプリでは、ほとんどの場合、それらのリソースと通信するときにデータ ゲートウェイは不要です。
>
> ISE の作成について詳しくは、[Azure Logic Apps から Azure Virtual Network への接続](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

* [コネクタの完全な一覧](https://docs.microsoft.com/connectors)を表示する
* [初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [ロジック アプリのカスタム コネクタを作成する](https://docs.microsoft.com/connectors/custom-connectors/)
* [ロジック アプリ用のカスタム API を作成する](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "オンプレミス データ ゲートウェイを使用して、ロジック アプリからオンプレミスのデータに接続する"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API を管理および発行するための Azure API Management サービス インスタンスを作成します。"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "App Service API Apps を使用してロジック アプリを統合します。"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Azure Functions を使用してロジック アプリを統合します。"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "グループ、またはバッチとしてメッセージを処理します。"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "配列内のすべての項目に対して同じアクションを実行します。"
[http-doc]: ./connectors-native-http.md "ロジック アプリから HTTP または HTTPS エンドポイントを呼び出す"
[http-request-doc]: ./connectors-native-reqres.md "ロジック アプリで HTTP 要求を受信する"
[http-response-doc]: ./connectors-native-reqres.md "ロジック アプリで HTTP 要求に応答する"
[http-swagger-doc]: ./connectors-native-http-swagger.md "ロジック アプリから REST エンドポイントを呼び出す"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP または HTTPS エンドポイントからの特定のイベントを待機する"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "入れ子になったワークフローを使用してロジック アプリを統合します。"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "クエリ アクションで、配列の選択とフィルター処理を行います。"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "スケジュールに基づいてロジック アプリを実行する"
[schedule-delay-doc]: ./connectors-native-delay.md "次のアクションの実行を遅らせる"
[schedule-delay-until-doc]: ./connectors-native-delay.md "次のアクションの実行を遅らせる"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "定期的なスケジュールでロジック アプリを実行する"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "連続したチャンク内のデータを処理する必要があるロジック アプリを実行する"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "アクションをグループに編成します。グループ内のアクションの実行が完了すると、グループ独自のステータスが取得されます。"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "アクションをケースに編成します。ケースには、一意の値が割り当てられます。値が式、オブジェクト、またはトークンの結果に一致するケースのみを実行します。一致が存在しない場合は、既定のケースを実行します。"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "アクティブに実行中のロジック アプリのワークフローを停止またはキャンセルします。"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "指定された条件が true になるまで、または特定の状態が変化するまで、アクションを繰り返します。"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "配列のフィルター処理や CSV と HTML のテーブルの作成などのデータ操作を実行します。"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "変数に対する操作を実行します。たとえば、文字列または配列の変数に対する初期化、設定、増分、減分、追加などです。"

<!--Managed connector doc links-->
[azure-automation-doc]: https://docs.microsoft.com/connectors/azureautomation/ "クラウドおよびオンプレミス インフラストラクチャ用のオートメーション ジョブを作成して管理します"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage コネクタを使用して BLOB コンテナーのファイルを管理します。"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Azure Cosmos DB に接続して、ドキュメントとストアド プロシージャにアクセスできるようにします"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Azure やサードパーティのリソースが変更されたときなど、Event Grid により発行されるイベントを監視します"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs に接続して、ロジック アプリと Event Hubs 間でイベントを送受信できるようにします"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Azure Storage アカウントに接続して、ファイルを作成、更新、取得、削除できるようにします"
[azure-key-vault-doc]: https://docs.microsoft.com/connectors/keyvault/ "シークレットとキーを管理できるように Azure Key Vault に接続します"
[azure-monitor-logs-doc]: https://docs.microsoft.com/connectors/azuremonitorlogs/ "Log Analytics ワークスペースと Application Insights コンポーネントの全体にわたって、Azure Monitor ログに対するクエリを実行します"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Azure Storage アカウントに接続して、キューとメッセージの作成と管理ができるようにします"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus キューとトピックからメッセージを送信したり、Service Bus キューとサブスクリプションからメッセージを受信したりします。"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Azure SQL Data Warehouse に接続して、データを表示できるようにします"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Azure Storage アカウントに接続して、テーブルなどの作成、更新、クエリ実行ができるようにします"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "BizTalk Server に接続して、BizTalk ベースのアプリケーションを Azure Logic Apps と並行して実行できるようにします"
[box-doc]: ./connectors-create-api-box.md "Box に接続します。ファイルのアップロード、取得、削除、一覧表示などを実行します。"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Dropbox に接続します。ファイルのアップロード、取得、削除、一覧表示などを実行します。"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Dynamics CRM Online に接続して、CRM Online データを操作できるようにします"
[facebook-doc]: ./connectors-create-api-facebook.md "Facebook に接続します。タイムラインへの投稿、ページ フィードの取得などを行います。"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "オンプレミスのファイル システムに接続します。"
[ftp-doc]: ./connectors-create-api-ftp.md "FTP/FTPS サーバーに接続して、ファイルのアップロード、取得、削除などの FTP タスクを実行できます。"
[github-doc]: ./connectors-create-api-github.md "GitHub に接続して、問題を追跡できます。"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google カレンダーに接続してカレンダーを管理できます"
[google-drive-doc]: ./connectors-create-api-googledrive.md "GoogleDrive に接続して、データを操作できるようにします"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Google スプレッドシートに接続して、シートを変更できるようにします"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Google タスクに接続して、タスクを管理できるようにします"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM メインフレーム上の 3270 アプリに接続する"
[ibm-db2-doc]: ./connectors-create-api-db2.md "クラウドまたはオンプレミスの IBM DB2 に接続します。行の更新、テーブルの取得などを行います。"
[ibm-informix-doc]: ./connectors-create-api-informix.md "クラウドまたはオンプレミスの Informix に接続します。行の読み取り、テーブルの一覧表示などを実行します。"
[ibm-mq-doc]: ./connectors-create-api-mq.md "オンプレミスまたは Azure 内の IBM MQ に接続し、メッセージを送受信します。"
[instagram-doc]: ./connectors-create-api-instagram.md "Instagram に接続します。イベントをトリガーしたり、イベントに対するアクションを実行したりします。"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "MailChimp アカウントに接続します。メールの管理と自動化を行います。"
[mandrill-doc]: ./connectors-create-api-mandrill.md "通信のために Mandrill に接続します。"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "オンプレミスの MySQL データベースに接続して、データの読み取りと書き込みができるようにします"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Office 365 アカウントに接続して、電子メールの送受信、予定表と連絡先の管理などができるようにします"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "個人用の Microsoft OneDrive に接続して、ファイルのアップロード、削除、一覧表示などができるようにします"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "ビジネス向けの Microsoft OneDrive に接続して、ファイルのアップロード、削除、一覧表示などができるようにします"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle データベースに接続して、行の追加、挿入、削除などができるようにします"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Outlook メールボックスに接続して、電子メール、予定表、連絡先などを管理できるようにします"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "PostgreSQL データベースに接続して、テーブルからデータを読み取ることができるようにします"
[project-online-doc]: ./connectors-create-api-projectonline.md "Microsoft Project Online に接続して、プロジェクト、タスク、リソースなどを管理できるようにします"
[rss-doc]: ./connectors-create-api-rss.md "フィード アイテムを発行および取得したり、RSS フィードに新しいアイテムが発行されたときに操作をトリガーしたりします"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce アカウントに接続します。アカウント、潜在顧客、営業案件などを管理します。"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "オンプレミスの SAP システムに接続します。"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid に接続します。メールの送信と受信者リストの管理を行います。"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH を使用して SFTP アカウントに接続します。ファイルのアップロード、取得、削除などを実行します。"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "SharePoint オンプレミス サーバーに接続します。ドキュメント、リスト アイテムなどを管理します。"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "SharePoint Online に接続します。ドキュメント、リスト アイテムなどを管理します。"
[slack-doc]: ./connectors-create-api-slack.md "Slack に接続し、Slack チャンネルにメッセージを投稿します。"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP サーバーに接続し、添付ファイルを含むメールを送信します。"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "通信のために SparkPost に接続します。"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database または SQL Server に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除します"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Teradata データベースに接続して、テーブルからデータを読み取ります"
[trello-doc]: ./connectors-create-api-trello.md "Trello に接続します。プロジェクトを管理し、どのようなものでも整理してだれとでも共有できます。"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio に接続します。メッセージの送信と取得、利用可能な番号の取得、着信電話番号の管理などを実行します。"
[twitter-doc]: ./connectors-create-api-twitter.md "Twitter に接続します。タイムラインの取得やツイートの投稿などを実行します。"
[yammer-doc]: ./connectors-create-api-yammer.md "Yammer に接続します。メッセージの投稿、新しいメッセージの取得などを実行します。"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube に接続します。ビデオとチャンネルを管理します。"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 プロトコルを使用するメッセージをエンコードおよびデコードします"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT プロトコルを使用するメッセージをエンコードおよびデコードします"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIFACT プロトコルを使用するメッセージをデコードします"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT プロトコルを使用するメッセージをエンコードします"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "統合アカウント アーティファクトのメタデータを管理します"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid テンプレートを使用して JSON を変換します"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 プロトコルを使用するメッセージをエンコードおよびデコードします"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 プロトコルを使用するメッセージをデコードします"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 プロトコルを使用するメッセージをエンコードします"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML メッセージ変換します"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML メッセージを検証します"

