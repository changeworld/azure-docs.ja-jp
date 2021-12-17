---
title: Azure Logic Apps のコネクタの概要
description: コネクタと、それが Azure Logic Apps を使用して自動化された統合ワークフローを迅速かつ簡単に構築するためにどのように役立つかについて説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: cccd744e8c123cd9441ff9aca47d2341ea9d80fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679865"
---
# <a name="about-connectors-in-azure-logic-apps"></a>Azure Logic Apps のカスタム コネクタの概要

Azure Logic Apps を使用してワークフローを構築する場合、"*コネクタ*" を使用して他のアプリ、サービス、システム、プロトコル、プラットフォームのデータ、イベント、リソースにすばやく簡単にアクセスすることができます (多くの場合、コードを書く必要はありません)。 コネクタには、ワークフローのステップとして使用できる事前構築済みの操作が用意されています。 Azure Logic Apps には、使用できる数多くのコネクタが用意されています。 アクセス対象のリソースに使用できるコネクタがない場合は、一般的な HTTP 操作を使用してサービスと通信するか、[カスタム コネクタを作成する](#custom-apis-and-connectors)ことができます。

この概要では、コネクタの概要、一般的なしくみ、Azure Logic Apps でよく使用される一般的なコネクタについて説明します。 詳細については、次のドキュメントを確認してください。

* [Azure Logic Apps、Microsoft Power Automate、Microsoft Power Apps のコネクタの概要](/connectors)
* [Azure Logic Apps のコネクタ リファレンス](/connectors/connector-reference/connector-reference-logicapps-connectors)
* [Azure Logic Apps の価格および課金モデル](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps の価格の詳細](https://azure.microsoft.com/pricing/details/logic-apps/)

<<<<<<< HEAD
| 名前 | 説明 |
|------|-------------|
| [![スケジュール組み込みコネクタ][schedule-icon]<br>**スケジュール**][schedule-doc] | - [**繰り返し** トリガー][schedule-recurrence-doc]を使用して、指定した繰り返しでロジック アプリを実行します。基本的なスケジュールから詳細なスケジュールまで指定できます。 <br>- [**スライディング ウィンドウ** トリガー][schedule-sliding-window-doc]を使用して、連続したチャンクのデータを処理する必要があるロジック アプリを実行します。 <br>- [**遅延** アクション][schedule-delay-doc]を使用して、指定した期間、ロジック アプリを一時停止します。 <br>- [**延期期限** アクション][schedule-delay-until-doc]を使用して、指定した日時までロジック アプリを一時停止します。 |
| [![バッチ組み込みコネクタ][batch-icon]<br>**バッチ**][batch-doc] | - **[メッセージのバッチ処理]** トリガーを使用して、メッセージをバッチで処理します。 <br>- **[バッチへのメッセージの送信]** アクションを使用して、既存のバッチ トリガーを持つロジック アプリを呼び出します。 |
| [![HTTP 組み込みコネクタ][http-icon]<br>**HTTP**][http-doc] | HTTP のトリガーとアクションを使用して HTTP または HTTPS エンドポイントを呼び出します。 その他の HTTP 組み込みトリガーおよびアクションには [HTTP + Swagger 組み込みコネクタ][http-swagger-doc]と [HTTP + Webhook][http-webhook-doc] が含まれます。 |
| [![要求組み込みコネクタ][http-request-icon]<br>**要求**][http-request-doc] | - 他のアプリやサービスからロジック アプリを呼び出し可能にしたり、Event Grid リソース イベントでロジック アプリをトリガーしたり、 **[要求]** トリガーによる Azure Security Center アラートへの応答でロジック アプリをトリガーしたりします。 <br>- **[応答]** アクションを使用して、アプリやサービスに応答を送信します。 |
| [![Azure API Management 組み込みコネクタ][azure-api-management-icon]<br>**Azure API <br>Management**][azure-api-management-doc] | Azure API Management で管理および発行する独自の API によって定義されたトリガーとアクションを呼び出します。 |
| [![Azure App Services 組み込みコネクタ][azure-app-services-icon]<br>**Azure App <br>Services**][azure-app-services-doc] | Azure App Service でホストされている Azure API アプリまたは Web アプリを呼び出します。 Swagger が含まれている場合、これらのアプリで定義されているトリガーとアクションは、他のファースト クラスのトリガーおよびアクションのように表示されます。 |
| [![Azure Logic Apps 組み込みコネクタ][azure-logic-apps-icon]<br>**Azure Logic <br>Apps**][nested-logic-app-doc] | **要求** トリガーで開始するその他のロジック アプリを呼び出します。 |
|||
=======
## <a name="what-are-connectors"></a>コネクタとは
>>>>>>> repo_sync_working_branch

コネクタとは、技術的には、基礎となるサービスによって Azure Logic Apps との通信に使用される API のプロキシまたはラッパーです。 このコネクタには、ワークフローでタスクを実行するために使用する操作が用意されています。 操作は、構成できるプロパティがある "*トリガー*" または "*アクション*" として使用できます。 トリガーとアクションの中には、たとえば、ユーザー アカウントへのアクセスを認証するために、まず[基礎となるサービスやシステムへの接続を作成し、構成する](#connection-configuration)必要があるものもあります。

### <a name="triggers"></a>トリガー

<<<<<<< HEAD
| 名前 | 説明 |
|------|-------------|
| [![Azure Functions 組み込みコネクタ][azure-functions-icon]<br>**Azure Functions**][azure-functions-doc] | カスタム コード スニペット (C# または Node.js) をロジック アプリから実行する Azure 関数を呼び出します。 |
| [![インライン コード組み込みコネクタ][inline-code-icon]<br>**インライン コード**][inline-code-doc] | ロジック アプリから JavaScript コード スニペットを追加して実行します。 |
|||

### <a name="control-workflow"></a>ワークフローを制御する

Logic Apps では、ロジック アプリのワークフローでアクションを構成および制御するための組み込みアクションが提供されています。

| 名前 | 説明 |
|------|-------------|
| [![Condition 組み込みアクション][condition-icon]<br>**Condition**][condition-doc] | 条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。 |
| [![For Each 組み込みアクション][for-each-icon]<br>**For each**][for-each-doc] | 配列内のすべての項目に対して同じアクションを実行します。 |
| [![Scope 組み込みアクション][scope-icon]<br>**Scope**][scope-doc] | アクションを *スコープ* にグループ化します。スコープ内のアクションが実行を完了すると、スコープ独自のステータスが取得されます。 |
| [![Switch 組み込みアクション][switch-icon]<br>**Switch**][switch-doc] | アクションを *ケース* にグループ化します。既定のケースを除き、ケースには、一意の値が割り当てられます。 割り当てられた値が式、オブジェクト、またはトークンの結果に一致するケースのみを実行します。 一致が存在しない場合は、既定のケースを実行します。 |
| [![Terminate 組み込みアクション][terminate-icon]<br>**Terminate**][terminate-doc] | アクティブに実行中のロジック アプリ ワークフローを停止します。 |
| [![Until 組み込みアクション][until-icon]<br>**Until**][until-doc] | 指定された条件が true になるまで、または特定の状態が変化するまで、アクションを繰り返します。 |
|||

### <a name="manage-or-manipulate-data"></a>データを管理または操作する

Logic Apps では、データ出力とその形式を操作するための組み込みアクションが提供されています。

| 名前 | 説明 |
|------|-------------|
| [![データ操作組み込みアクション][data-operations-icon]<br>**データ操作**][data-operations-doc] | データの操作を実行します。 <p>- **作成**:さまざまな型の複数の入力から単一の出力を作成します。 <br>- **CSV テーブルの作成**:JSON オブジェクトの配列からコンマ区切り値 (CSV) テーブルを作成します。 <br>- **HTML テーブルの作成**:JSON オブジェクトの配列から HTML テーブルを作成します。 <br>- **配列のフィルター処理**:別の配列内にある、条件を満たす項目から配列を作成します。 <br>- **結合**:配列内のすべての項目から 1 つの文字列を作成し、それらの項目を指定の区切り記号で区切ります。 <br>- **JSON の解析**:JSON コンテンツ内のプロパティとその値からわかりやすいトークンを作成して、ユーザーがそれらのプロパティをワークフロー内で使用できるようにします。 <br>- **選択**:別の配列内の項目または値を変換し、それらの項目を指定のプロパティにマッピングすることにより、JSON オブジェクトで配列を作成します。 |
| ![日付と時刻組み込みアクション][date-time-icon]<br>**日付/時刻** | タイムスタンプを使用して操作を実行します。 <p>- **時間への追加**:指定した数の単位をタイムスタンプに追加します。 <br>- **タイム ゾーンの変換**:タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。 <br>- **現在の時刻**:現在のタイムスタンプを文字列として返します。 <br>- **未来の時間の取得**:現在のタイムスタンプに指定した時刻単位を加えて返します。 <br>- **過去の時間の取得**:現在のタイムスタンプから指定した時刻単位を引いて返します。 <br>- **時間からの減算**:タイムスタンプから時間単位数を減算します。 |
| [![変数組み込みアクション][variables-icon]<br>**変数**][variables-doc] | 変数の操作を実行します。 <p>- **配列変数に追加**:変数によって保管される配列内に、値を最後の項目として挿入します。 <br>- **文字列変数に追加**:変数によって保管される文字列内に、値を最後の文字として挿入します。 <br>- **変数の値を減らす**:変数の値を一定値だけ減らします。 <br>- **変数の値を増やす**:変数の値を一定値だけ増やします。 <br>- **変数を初期化する**:変数を作成し、そのデータ型と初期値を宣言します。 <br>- **変数の設定**:既存の変数に異なる値を代入します。 |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>マネージド コネクタ

Logic Apps には、次のサービスまたはシステムでタスク、プロセス、およびワークフローを自動化するためによく使用される標準コネクタが用意されています。

| 名前 | 説明 |
|------|-------------|
| [![Azure Service Bus マネージド コネクタ][azure-service-bus-icon]<br>**Azure Service Bus**][azure-service-bus-doc] | Logic Apps で最もよく使用されているコネクタを使用して非同期メッセージ、セッション、およびトピック サブスクリプションを管理します。 |
| [![SQL Server マネージド コネクタ][sql-server-icon]<br>**SQL Server**][sql-server-doc] | オンプレミスの SQL Server、またはクラウド内の Azure SQL Database に接続して、レコードの管理、ストアド プロシージャの実行、クエリの実行を行えるようにします。 |
| [![Azure Blob Storage マネージド コネクタ][azure-blob-storage-icon]<br>**Azure Blob<br>Storage**][azure-blob-storage-doc] | ストレージ アカウントに接続して、BLOB コンテンツの作成と管理ができるようにします。 |
| [![Office 365 Outlook マネージド コネクタ][office-365-outlook-icon]<br>**Office 365<br>Outlook**][office-365-outlook-doc] | 職場または学校のメール アカウントに接続して、メール、タスク、カレンダー イベントおよび会議、連絡先、要求などの作成と管理ができるようにします。 |
| [![SFTP-SSH マネージド コネクタ][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | SSH を使用してインターネットからアクセス可能な SFTP サーバーに接続して、ファイルとフォルダーを操作できるようにします。 |
| [![SharePoint Online マネージド コネクタ][sharepoint-online-icon]<br>**SharePoint<br>Online**][sharepoint-online-doc] | SharePoint Online に接続して、ファイル、添付ファイル、フォルダーなどを管理できるようにします。 |
| [![Azure キュー マネージド コネクタ][azure-queues-icon]<br>**Azure <br>Queues**][azure-queues-doc] | Azure Storage アカウントに接続して、キューとメッセージの作成と管理ができるようにします。 |
| [![FTP マネージド コネクタ][ftp-icon]<br>**FTP**][ftp-doc] | インターネットからアクセス可能な FTP サーバーに接続して、ファイルとフォルダーを操作できるようにします。 |
| [![ファイル システム マネージド コネクタ][file-system-icon]<br>**ファイル<br>システム**][file-system-doc] | オンプレミスのファイル共有に接続して、ファイルの作成と管理ができるようにします。 |
| [![Azure Event Hubs マネージド コネクタ][azure-event-hubs-icon]<br>**Azure Event Hubs**][azure-event-hubs-doc] | イベント ハブを通じてイベントを使用したり、発行したりします。 たとえば、ロジック アプリから Event Hubs を使用して出力を取得し、リアルタイム分析プロバイダーに送信できます。 |
| [![Azure Event Grid マネージド コネクタ][azure-event-grid-icon]<br>**Azure Event**<br>**Grid**][azure-event-grid-doc] | Azure やサードパーティのリソースが変更されたときなどに、Event Grid が発行するイベントを監視します。 |
| [![Salesforce マネージド コネクタ][salesforce-icon]<br>**Salesforce**][salesforce-doc] | Salesforce アカウントに接続して、レコード、ジョブ、オブジェクトなどの項目の作成と管理ができるようにします。 |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>オンプレミス コネクタ

オンプレミス システムへの接続を作成するには、最初に[オンプレミス データ ゲートウェイのダウンロード、インストール、設定][gateway-doc]を行う必要があります。 このゲートウェイは、セキュリティで保護された通信チャネルを提供します。必要なネットワーク インフラストラクチャを設定する必要はありません。 

ここでは、オンプレミス システムのデータやリソースにアクセスするために Logic Apps で提供されていてよく使用される "*一部の*" 標準コネクタを紹介します。 オンプレミス コネクタの一覧については、「[サポートされるデータ ソース](../logic-apps/logic-apps-gateway-connection.md#supported-connections)」を参照してください。

:::row:::
    :::column:::
        [![BizTalk Server コネクタ][biztalk-server-icon]<br>**BizTalk** <br>**Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![ファイル システム コネクタ][file-system-icon]<br>**ファイル<br>システム**][file-system-doc]
    :::column-end:::
    :::column:::
        [![DB2 コネクタ][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Informix コネクタ][ibm-informix-icon]<br>**IBM** <br>**Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL コネクタ][mysql-icon]<br>**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB コネクタ][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL コネクタ][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![SharePoint Server コネクタ][sharepoint-server-icon]<br>**SharePoint <br>Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server コネクタ][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Teradata コネクタ][teradata-icon]<br>**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::
=======
"*トリガー*" は、ワークフローを開始するイベントを指定するものであり、どのワークフローでも常に最初のステップになります。 各トリガーはまた、トリガーがイベントを監視したり、それに応答したりする方法を制御する特定の起動パターンに従います。 通常、トリガーは "*ポーリング*" パターンまたは "*プッシュ*" パターンに従いますが、トリガーを両方のバージョンで使用できる場合もあります。

- "*ポーリング トリガー*" を使って、指定されたスケジュールで特定のサービスまたはシステムを定期的にチェックして、新しいデータまたは特定のイベントをチェックします。 新しいデータが利用可能な場合、または特定のイベントが発生した場合、ワークフローの新しいインスタンスが、このトリガーによって作成および実行されます。 この新しいインスタンスから、入力として渡されたデータを使用できます。
- "*プッシュ トリガー*" を使って、新しいデータまたはイベントの発生をリッスンし、ポーリングは行いません。 新しいデータが使用可能になった場合、またはイベントが発生した場合、これらのトリガーではワークフローの新しいインスタンスを作成して実行します。 この新しいインスタンスから、入力として渡されたデータを使用できます。
>>>>>>> repo_sync_working_branch

たとえば、ファイルが FTP サーバーにアップロードされたときに何か処理を行うワークフローを構築したいとします。 ワークフローの最初のステップとして、ポーリング パターンに従う **[ファイルの追加または変更時]** という名前の FTP トリガーを使用できます。 その後、アップロード イベントを定期的にチェックするようにスケジュールを指定できます。

トリガーを使って、入力やその他の必要なデータをワークフローに渡し、後のアクションでそのデータを参照し、ワークフロー全体で使用することもできます。 たとえば、新しい電子メールを受け取ったときにワークフローを開始するために、 **[新しい電子メールが届いたとき]** という名前の Office 365 Outlook トリガーを使用するとします。 このトリガーは、新しい電子メールの内容に沿って、送信者、件名、本文、添付ファイルなどを渡すことができるように構成できます。 ワークフローでは、その後、他のアクションを使用してそれらの情報を処理できます。

### <a name="actions"></a>アクション

"*アクション*" は、トリガーに従い、ワークフローで何らかのタスクを実行する操作です。 ワークフローでは、複数のアクションを使用できます。 たとえば、SQL データベース内の新しい顧客データを検出する SQL トリガーを使用してワークフローを開始するとします。 このトリガーに従って、ワークフローに、その顧客データを取得する SQL アクションを含めることができます。 この SQL アクションに続けて、ワークフローに、そのデータを処理する (必ずしも SQL ではない) 別のアクションを含めることができます。

## <a name="connector-categories"></a>コネクタのカテゴリ

Azure Logic Apps では、ほとんどのトリガーとアクションが "*組み込み*" バージョンまたは "*マネージド コネクタ*" バージョンのどちらかで使用できます。 両方のバージョンで使用できるトリガーとアクションは少数です。 使用できるバージョンは、マルチテナント ロジック アプリと (現在[シングルテナントの Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md) でのみ使用可能な) シングルテナント ロジック アプリのどちらを作成するかによって異なります。

[組み込みのトリガーとアクション](built-in.md)は Logic Apps ランタイムでネイティブに実行され、接続を作成する必要はなく、次の種類のタスクを実行します。

- [ワークフローでコードを実行](built-in.md#run-code-from-workflows)します。
- [データを整理および制御](built-in.md#control-workflow)します。
- [データを管理または操作](built-in.md#manage-or-manipulate-data)します。

[マネージド コネクタ](managed.md)は、Microsoft によってデプロイ、ホスト、および管理されます。 これらのコネクタにより、クラウドサービス、オンプレミス システム、またはその両方に対してトリガーとアクションが提供されます。 マネージド コネクタは、次のカテゴリで使用できます。

- [オンプレミス コネクタ](managed.md#on-premises-connectors)。オンプレミス システムのデータとリソースにアクセスするのに役立ちます。
- [エンタープライズ コネクタ](managed.md#enterprise-connectors)。エンタープライズ システムへのアクセスを提供します。
- [統合アカウント コネクタ](managed.md#integration-account-connectors)。企業間 (B2B) 通信のシナリオをサポートします。
- [統合サービス環境 (ISE) コネクタ](managed.md#ise-connectors)。[ISE でのみ使用できるマネージド コネクタ](#ise-and-connectors)の小さなグループです。

<a name="connection-configuration"></a>

## <a name="connection-configuration"></a>接続の構成

ロジック アプリのリソースや接続を作成または管理するには、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) を使用したロールを通じて付与される、特定の権限が必要です。 Azure サブスクリプションにアクセスできるメンバーには、組み込みまたはカスタマイズされたロールを割り当てることができます。 Azure Logic Apps の具体的なロールは以下のとおりです。

* [ロジック アプリの共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor): ロジック アプリを管理できますが、アクセス権を変更することはできません。

* [ロジック アプリのオペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator):ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。

* [共同作成者](../role-based-access-control/built-in-roles.md#contributor): すべてのリソースを管理するためのフル アクセスが付与されますが、Azure RBAC でロールを割り当てたり、Azure Blueprints で割り当てを管理したり、イメージ ギャラリーを共有したりすることはできません。

  たとえば、ロジック アプリのワークフローで使用する接続の作成と認証を自身で行っていないロジック アプリを使用する必要があるとします。 Azure サブスクリプションには、そのロジック アプリ リソースを含むリソース グループに対する共同作成者のアクセス許可が付与されている必要があります。 ロジック アプリ リソースの作成者には、自動的に共同作成者のアクセス許可が付与されます。

ワークフローでコネクタのトリガーまたはアクションを使用するには、ほとんどのコネクタの場合、まずターゲット サービスまたはシステムへの *接続* を作成しておく必要があります。 ロジック アプリ ワークフロー内で接続を作成するには、アカウントの資格情報、および場合によっては他の接続情報を使用してご自分の ID を認証する必要があります。 たとえば、ワークフローで Office 365 Outlook 電子メール アカウントにアクセスしてそれを操作するには、そのアカウントへの接続を認可しておく必要があります。 組み込み操作とマネージド コネクタの数が少ない場合は、認証情報を提供する代わりに、[マネージド ID を設定して認証に使用する](../logic-apps/create-managed-service-identity.md#triggers-actions-managed-identity)ことができます。

<a name="connection-security-encyrption"></a>

### <a name="connection-security-and-encryption"></a>接続のセキュリティと暗号化

サーバー アドレス、ユーザー名、パスワード、資格情報、シークレットなどの接続構成の詳細は[暗号化され、セキュリティで保護された Azure 環境](../security/fundamentals/encryption-overview.md)に格納されます。 この情報は、ロジック アプリ リソースでのみ使用することができ、リンクされたアクセス確認を使用して適用される接続リソースに対するアクセス許可を持つクライアントによってのみ使用できます。 Office 365、Salesforce、GitHub など、Azure Active Directory Open Authentication (Azure AD OAuth) を使用する接続では、サインインが必要ですが、Azure Logic Apps ではアクセス トークンと更新トークンだけがシークレットとして格納され、サインイン資格情報は格納されません。

サービスまたはシステムが許可する限り、確立された接続から、対象のサービスまたはシステムにアクセスできます。 Azure AD OAuth 接続を使用するサービス (Office 365 や Dynamics など) の場合、Logic Apps サービスではアクセス トークンを無期限に更新します。 その他のサービスでは、Logic Apps が更新なしでトークンを使用できる期間が制限されることがあります。 一部のアクション (パスワードの変更など) では、すべてのアクセス トークンが無効になります。

ワークフロー内から接続を作成しますが、接続は独自のリソース定義を持つ個別の Azure リソースです。 これらの接続リソース定義を確認するには、[Azure から Visual Studio にロジック アプリをダウンロード](../logic-apps/manage-logic-apps-with-visual-studio.md)します。 これは、デプロイの準備がほとんど整っている、パラメーター化された有効なロジック アプリ テンプレートを作成するための最も簡単な方法です。

> [!TIP]
> 組織が Logic Apps コネクタを介して特定のリソースにアクセスすることを許可していない場合は、[Azure Policy](../governance/policy/overview.md) を使用して、[そのような接続を作成する機能をブロックする](../logic-apps/block-connections-connectors.md)ことができます。

ロジック アプリと接続をセキュリティで保護する方法の詳細については、「[Azure Logic Apps におけるアクセスとデータのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md)」を参照してください。

<a name="firewall-access"></a>

### <a name="firewall-access-for-connections"></a>接続のためのファイアウォール アクセス

トラフィックを制限するファイアウォールを使用しており、かつロジック アプリ ワークフローがそのファイアウォール経由で通信する必要がある場合は、そのロジック アプリ ワークフローが存在する Azure リージョン内の Logic Apps サービスまたはランタイムによって使用される[受信](../logic-apps/logic-apps-limits-and-config.md#inbound) IP アドレスと[送信](../logic-apps/logic-apps-limits-and-config.md#outbound) IP アドレスの両方のアクセスを許可するようにファイアウォールを設定する必要があります。 また、ワークフローでマネージド コネクタ (Office 365 Outlook コネクタや SQL コネクタなど) を使用する場合や、カスタム コネクタを使用する場合は、ファイアウォールで、ロジック アプリの Azure リージョン内の "*すべての*" [マネージド コネクタ送信 IP アドレス](/connectors/common/outbound-ip-addresses#azure-logic-apps)のアクセスも許可する必要があります。 詳細については、[ファイアウォールの構成](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)に関するページを確認してください。

## <a name="recurrence-behavior"></a>繰り返しの動作

繰り返しの組み込みトリガー ([繰り返しトリガー](connectors-native-recurrence.md)など) は、Logic Apps ランタイムでネイティブに実行され、最初に接続を作成する必要がある繰り返しの接続ベースのトリガー (Office 365 Outlook コネクタ トリガーなど) とは異なります。

どちらの種類のトリガーでも、特定の開始日時が繰り返しに指定されていない場合は、トリガーの繰り返し設定にかかわらず、ロジック アプリを保存またはデプロイすると、直ちに最初の繰り返しが実行されます。 この動作を回避するには、最初の繰り返しを実行する開始日時を指定してください。

### <a name="recurrence-for-built-in-triggers"></a>組み込みトリガーの繰り返し

繰り返しの組み込みトリガーでは、指定されたタイム ゾーンを含め、設定したスケジュールに従います。 ただし、将来の繰り返しを実行する特定時刻など、その他の詳細なスケジュール オプションが繰り返しに指定されていない場合は、それらの繰り返しは最後のトリガー実行に基づいて行われます。 その結果、ストレージ呼び出し中の待機時間などの要因によって、それらの繰り返しの開始時刻がずれる可能性があります。

詳細については、次のドキュメントを確認してください。

* [Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [繰り返しトリガーを使用して繰り返しタスクおよびワークフローを作成、スケジュール設定、および実行する](connectors-native-recurrence.md)
* [繰り返しの問題をトラブルシューティングする](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>接続ベース トリガーの繰り返し

Office 365 Outlook など、繰り返しの接続ベースのトリガーでは、実行を制御するドライバーはスケジュールだけではありません。 タイム ゾーンにより、最初の開始時刻のみが決定されます。 後続の実行は、繰り返しのスケジュール、最後のトリガーの実行、"および" 実行時間の移動や予期しない動作を引き起こす可能性があるその他の要因によって決まります。次に例を示します。

* より多くのデータがあるサーバーにトリガーがアクセスするかどうか。これは、トリガーによってすぐに取り込みが試行されます。
* トリガーによって発生するエラーまたは再試行。
* ストレージ呼び出し中の待機時間。
* 夏時間 (DST) の開始および終了時に、指定されたスケジュールが維持されない。
* 次回の実行時刻の発生に影響を与える可能性があるその他の要因。

詳細については、次のドキュメントを確認してください。

* [Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [繰り返しの問題をトラブルシューティングする](#recurrence-issues)

<a name="recurrence-issues"></a>

### <a name="troubleshooting-recurrence-issues"></a>繰り返しの問題をトラブルシューティングする

ワークフローが指定した開始時刻に確実に実行され、特に頻度が数日以上の期間である場合、繰り返しが決して見逃されないようにするには、次のソリューションを試してください。

* DST が有効になったときに、ワークフローが引き続き予定時刻に実行されるように、繰り返しを手動で調整します。 そうしないと、開始時刻が DST の開始時には 1 時間先に、DST の終了時には 1 時間前にシフトされます。 詳細および例については、「[夏時間と標準時間での繰り返し](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)」を確認してください。

* **繰り返し** トリガーを使用している場合は、タイム ゾーン、開始日、開始時刻を指定します。 また、 **[設定時刻 (時間)]** および **[設定時刻 (分)]** プロパティ ( **[日]** と **[週]** の頻度でのみ使用可能) で、後続の繰り返しを実行する特定の時刻を構成します。 ただし、一部の時間枠では、時間がシフトするときに引き続き問題が発生することがあります。

* 繰り返しが必ず行われるように、**繰り返し** トリガーの代わりに [**スライディング ウィンドウ** トリガー](connectors-native-sliding-window.md)を使用することを検討してください。

## <a name="custom-apis-and-connectors"></a>カスタム API とコネクタ

カスタム コードを実行する API や、コネクタとして使用できない API を呼び出すには、[カスタム API Apps を作成](../logic-apps/logic-apps-create-api-app.md)して、Logic Apps プラットフォームを拡張します。 "任意の" REST または SOAP ベース API 用の[カスタム コネクタを作成](../logic-apps/custom-connector-overview.md)することもできます。その場合、これらの API は、Azure サブスクリプションの任意のロジック アプリで使用できるようになります。 Azure 内でカスタム API Apps またはコネクタを公開し、誰でも使用できるようにするには、[Microsoft の認定を受けるためにコネクタを提出](/connectors/custom-connectors/submit-certification)してください。

## <a name="ise-and-connectors"></a>ISE とコネクタ

Azure 仮想ネットワーク内のリソースに直接アクセスする必要があるワークフローの場合は、専用のリソースでワークフローをビルド、デプロイ、および実行できる、専用の[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成できます。 ISE の作成の詳細については、[Azure Logic Apps から Azure 仮想ネットワークへの接続](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)に関するページを確認してください。

ISE 内で作成されたカスタム コネクタは、オンプレミス データ ゲートウェイでは動作しません。 ただし、これらのコネクタでは、ISE がホストされている Azure 仮想ネットワークに接続されているオンプレミス データ ソースに直接アクセスできます。 そのため、ISE 内のロジック アプリでは、ほとんどの場合、それらのリソースと通信するときにデータ ゲートウェイは不要です。 ISE の外部で作成したカスタム コネクタがあり、オンプレミス データ ゲートウェイを必要とする場合、ISE 内のロジック アプリから、それらのコネクタを使用できます。

Logic Apps デザイナーでは、組み込みのトリガーとアクションまたは ISE でロジック アプリに使用するマネージド コネクタを参照すると、組み込みのトリガーとアクションでは **CORE** ラベルが表示されるに対して、ISE で動作するように設計されたマネージド コネクタでは **ISE** ラベルが表示されます。

:::row:::
    :::column:::
        ![CORE コネクタの例](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        このラベルが付いた組み込みトリガーおよびアクションは、ロジック アプリと同じ ISE 内で動作します。
    :::column-end:::
    :::column:::
        ![ISE コネクタの例](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        このラベルが付いたマネージド コネクタは、ロジック アプリと同じ ISE 内で動作します。 
        \
        \
        Azure 仮想ネットワークに接続されているオンプレミス システムがある場合は、ISE により、ワークフローは[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を使用しなくても、そのシステムに直接アクセスできます。 代わりに、そのシステムの **ISE** コネクタ (使用可能な場合)、HTTP アクション、または [カスタム コネクタ](#custom-apis-and-connectors)を使用できます。
        \
        \
        **ISE** コネクタのないオンプレミス システムの場合は、オンプレミス データ ゲートウェイを使用します。 使用できる ISE コネクタを見つけるには、[ISE コネクタ](#ise-and-connectors)を確認してください。
    :::column-end:::
    :::column:::
        ![ISE 以外のコネクタの例](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        ラベルなし    \
        \
        ラベルのないその他のすべてのコネクタ (これも引き続き使用できます) の場合は、グローバルなマルチテナント Logic Apps サービスで実行します。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>既知の問題

次の表には、Logic Apps コネクタに関する既知の問題が含まれています。

| エラー メッセージ| 説明 | 解決方法 |
|--------------|-------------|------------|
| `Error: BadGateway. Client request id: '{GUID}'` | このエラーは、 1 つ以上の接続で SFTP や SQL などの Azure Active Directory (Azure AD) OAuth 認証がサポートされていないロジック アプリでタグを更新した場合に発生し、それらの接続が切断されます。 | この動作を回避するには、それらのタグを更新しないようにします。 |
||||

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps から呼び出しできるカスタム API の作成](../logic-apps/logic-apps-create-api-app.md)
