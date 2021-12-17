---
title: 組み込みのトリガーとアクション
description: 組み込みのトリガーとアクションを使用することで、Azure Logic Apps を使用して、アプリ、データ、サービス、システムを統合する自動ワークフローを作成し、ワークフローを制御し、データを管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 305f23d802324f5d974caaaf49afc072ff9d54f6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071023"
---
# <a name="built-in-triggers-and-actions-in-azure-logic-apps"></a>Azure Logic Apps の組み込みのトリガーとアクション

[組み込みのトリガーとアクション](apis-list.md)により、[ワークフローのスケジュールと構造の制御](#control-workflow)、[独自のコードの実行](#run-code-from-workflows)、[データの管理または操作](#manage-or-manipulate-data)、ワークフロー内の他のタスクの実行を行う手段が提供されます。 [マネージド コネクタ](managed.md)とは異なり、多くの組み込み操作は、特定のサービス、システム、またはプロトコルに関連付けられていません。 たとえば、繰り返しトリガーを使用すると、ほぼどのようなワークフローでもスケジュールに従って開始できます。 または、要求トリガーを使用することで、呼び出されるまでワークフローを待機させることもできます。 すべての組み込み操作は Azure Logic Apps でネイティブに実行され、ほとんどの場合、その使用前に接続を作成する必要はありません。

サービス、システム、およびプロトコルの数が少ない場合、Azureロジックアプリ には、Azure API Management、Azure アプリサービス、Azure Functions、他の Azure Logic Apps Logic App ワークフローを呼び出すための組み込みの操作が用意されています。 使用できる数と範囲は、マルチテナントの Azure Logic Apps で実行される従量課金プランベースのロジック アプリ リソースを作成するか、シングル テナントの Azure Logic Apps で実行する Standard プランベースのロジック アプリ リソースを作成するかによって異なります。 詳細については、[シングルテナントとマルチテナント、および統合サービス環境 (ISE)](../logic-apps/single-tenant-overview-compare.md) に関するページを参照してください。 ほとんどの場合、組み込みのバージョンの方がパフォーマンス、機能、価格などに優れています。

例えば、シングルテナントのロジックアプリを作成する場合は、組み込み操作と[マネージコネクタ操作](managed.md)の両方をいくつかのサービス (具体的には、azure Blob、azure Event Hubs、Azure Cosmos DB、azure Service Bus、DB2、MQ、および SQL Server) で利用できます。 一部の組み込み操作は、ロジックアプリのリソースの種類に対してのみ使用できます。 例えば、Batch 操作は現在、消費ロジックアプリワークフローでのみ使用できます。 ほとんどの場合、組み込みのバージョンの方がパフォーマンス、機能、価格などに優れています。

次の一覧では、[組み込みのトリガーとアクション](#general-built-in-triggers-and-actions)で実行できるタスクの一部についてのみ説明します。

- カスタムおよび詳細なスケジュールを使用してワークフローを実行します。 スケジュール設定の詳細については、[Azure Logic Apps のコネクタの概要にある「繰り返しの動作」セクション](apis-list.md#recurrence-behavior)を参照してください。

- ワークフローの構造を、たとえばループと条件を使用して、整理および制御します。

- 変数、日付、データ操作、コンテンツ変換、バッチ操作を処理します。

- HTTP トリガーとアクションを使用して、他のエンドポイントと通信します。

- 要求を受信して応答します。

- 独自の関数 (Azure Functions)、Web アプリ (Azure App Services)、API (Azure API Management)、要求を受け取ることができる他の Azure Logic Apps ワークフローなどを呼び出します。

## <a name="general-built-in-triggers-and-actions"></a>汎用の組み込みのトリガーとアクション

Azure Logic Apps には、次の組み込みのトリガーとアクションが用意されています。

:::row:::
    :::column:::
        [![スケジュール アイコン][schedule-icon]][schedule-doc]
        \
        \
        [**スケジュール**][schedule-doc]
        \
        \
        [**繰り返し**][schedule-recurrence-doc]: 指定された繰り返しに基づいてワークフローをトリガーします。
        \
        \
        [**スライディング ウィンドウ**][schedule-sliding-window-doc]: 連続したチャンクのデータを処理する必要があるワークフローをトリガーします。
        \
        \
        [**遅延**][schedule-delay-doc]: 指定された期間、ワークフローを一時停止します。
        \
        \
        [**延期期限**][schedule-delay-until-doc]: 指定された日時までワークフローを一時停止します。
    :::column-end:::
    :::column:::
        [![HTTP トリガーとアクション アイコン][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        HTTP トリガーまたはアクションを使用して、HTTP または HTTPS エンドポイントを呼び出します。
        \
        \
        こちらの他の組み込みの HTTP トリガーとアクションを使用することもできます。
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP および Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![要求トリガー アイコン][http-request-icon]][http-request-doc]
        \
        \
        [**要求**][http-request-doc]
        \
        \
        [**HTTP 要求の受信時**][http-request-doc]: 別のワークフロー、アプリ、またはサービスからの要求を待機します。 このトリガーによって、スケジュールに基づいてチェックしたりポーリングしたりしなくても、ワークフローを呼び出すことができます。
        \
        \
        [**応答**][http-request-doc]: 同じワークフローで **[HTTP 要求の受信時]** トリガーによって受信された要求に応答します。
    :::column-end:::
    :::column:::
        [![バッチ アイコン][batch-icon]][batch-doc]<br>(*消費ロジックアプリのみ*)     \
        \
        [**バッチ**][batch-doc]
        \
        \
        [**メッセージのバッチ処理**][batch-doc]: バッチ内のメッセージを処理するワークフローをトリガーします。
        \
        \
        [**バッチ処理するメッセージの送信**][batch-doc]: **[メッセージのバッチ処理]** トリガーで現在開始されている既存のワークフローを呼び出します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![STFP-SSH アイコン][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        SSH を使用してインターネットからアクセス可能な SFTP サーバーに接続して、ファイルとフォルダーを操作できるようにします。
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="service-based-built-in-trigger-and-actions"></a>サービスベースの組み込みのトリガーとアクション

Azure Logic Apps には、次のサービス用の組み込みアクションが用意されています。

:::row:::
    :::column:::
        [![Azure API Management アイコン][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        [Azure API Management](../api-management/api-management-key-concepts.md) を使用して、定義、管理、発行する API で独自のトリガーとアクションを呼び出します。 <p><p>**注**: [API Management で従量課金レベル](../api-management/api-management-features.md)を使用している場合はサポートされません。
    :::column-end:::
    :::column:::
        [![Azure App Services アイコン][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        [Azure App Service](../app-service/overview.md) で作成してホストするアプリ (API Apps や Web Apps など) を呼び出します。
        \
        \
        Swagger が含まれている場合、Azure Logic Apps では、これらのアプリで定義されているトリガーとアクションは、他のファースト クラスのトリガーとアクションのように表示されます。
    :::column-end:::
    :::column:::
        [![Azure Blob アイコンアイコン][azure-blob-storage-icon]][azure-app-services-doc]
        \
        \
        [**Azure BLOB**][azure-blob-storage-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        Azure Storage アカウントに接続して、BLOB コンテンツの作成と管理ができるようにします。
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB アイコン][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB**][azure-cosmos-db-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        Azure Cosmos DB ドキュメントにアクセスして管理できるように、Azure Cosmos DB に接続します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Functions アイコン][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure 関数**][azure-functions-doc]
        \
        \
        [Azure でホストされている関数](../azure-functions/functions-overview.md)を呼び出して、ワークフロー内で独自の "*コード スニペット*" (C# または Node.js) を実行します。
    :::column-end:::
    :::column:::
        [![Azure Logic Apps アイコン][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        **[HTTP 要求の受信時]** という名前の要求トリガーで始まる他のワークフローを呼び出します。
    :::column-end:::
    :::column:::
        [![Azure Service Bus アイコン][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        非同期メッセージ、キュー、セッション、トピック、およびトピックサブスクリプションを管理します。
    :::column-end:::
    :::column:::
        [![IBM DB2 アイコン][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**DB2**][ibm-db2-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        クラウドまたはオンプレミスの IBM DB2 に接続します。 行の更新、テーブルの取得などを行います。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs アイコン][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Event Hubs**][azure-event-hubs-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        イベントハブを使用してイベントを使用および公開します。 たとえば、ロジック アプリから Event Hubs を使用して出力を取得し、リアルタイム分析プロバイダーに送信できます。
    :::column-end:::
    :::column:::
        [![IBM MQ アイコン][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**MQ**][ibm-mq-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        メッセージを送受信するために、オンプレミスまたは Azure の IBM MQ に接続します。
    :::column-end:::
    :::column:::
        [![SQL Server アイコン][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]<br>(*標準ロジックアプリのみ*)     \
        \
        オンプレミスの SQL Server、またはクラウド内の Azure SQL Database に接続して、レコードの管理、ストアド プロシージャの実行、クエリの実行を行えるようにします。 <p>**注**: シングルテナント Azure Logic Apps では、SQL の組み込みコネクタ操作とマネージド コネクタ操作の両方を行えますが、マルチテナント Azure Logic Apps ではマネージド コネクタ操作のみを行えます。 <p>詳細については、「[Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較](../logic-apps/single-tenant-overview-compare.md)」を確認してください。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>ワークフローからコードを実行する

Azure Logic Apps には、ワークフローで独自のコードを実行するために次の組み込みアクションが用意されています。

:::row:::
    :::column:::
        [![Azure Functions アイコン][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure 関数**][azure-functions-doc]
        \
        \
        [Azure でホストされている関数](../azure-functions/functions-overview.md)を呼び出して、ワークフロー内で独自の "*コード スニペット*" (C# または Node.js) を実行します。
    :::column-end:::
    :::column:::
        [![インライン コード アクション アイコン][inline-code-icon]][inline-code-doc]
        \
        \
        [**インライン コード**][inline-code-doc]
        \
        \
        [**JavaScript コードの実行**][inline-code-doc]: ワークフロー内で独自のインライン JavaScript "*コード スニペット*" を追加して実行します。
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>ワークフローを制御する

Azure Logic Apps には、ワークフローでアクションを構成および制御するための次の組み込みアクションが用意されています。

:::row:::
    :::column:::
        [![条件アクション アイコン][condition-icon]][condition-doc]
        \
        \
        [**Condition**][condition-doc]
        \
        \
        条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。
    :::column-end:::
    :::column:::
        [![For Each アクション アイコン][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        配列内のすべての項目に対して同じアクションを実行します。
    :::column-end:::
    :::column:::
        [![スコープ アクション アイコン][scope-icon]][scope-doc]
        \
        \
        [**Name**][scope-doc]
        \
        \
        アクションを *スコープ* にグループ化します。スコープ内のアクションが実行を完了すると、スコープ独自のステータスが取得されます。
    :::column-end:::
    :::column:::
        [![switch アクション アイコン][switch-icon]][switch-doc]
        \
        \
        [**Switch**][switch-doc]
        \
        \
        アクションを *ケース* にグループ化します。既定のケースを除き、ケースには、一意の値が割り当てられます。 割り当てられた値が式、オブジェクト、またはトークンの結果に一致するケースのみを実行します。 一致が存在しない場合は、既定のケースを実行します。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![終了アクション アイコン][terminate-icon]][terminate-doc]
        \
        \
        [**Terminate**][terminate-doc]
        \
        \
        アクティブに実行中のロジック アプリ ワークフローを停止します。
    :::column-end:::
    :::column:::
        [![Until アクション アイコン][until-icon]][until-doc]
        \
        \
        [**Until**][until-doc]
        \
        \
        指定された条件が true になるまで、または特定の状態が変化するまで、アクションを繰り返します。
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>データを管理または操作する

Azure Logic Apps には、データ出力とその形式を操作するための次の組み込みアクションが用意されています。

:::row:::
    :::column:::
        [![データ操作アイコン][data-operations-icon]][data-operations-doc]
        \
        \
        [**データ操作**][data-operations-doc]
        \
        \
        データの操作を実行します。
        \
        \
        **作成**: さまざまな型の複数の入力から単一の出力を作成します。
        \
        \
        **CSV テーブルの作成**: JSON オブジェクトの配列からコンマ区切り値 (CSV) テーブルを作成します。
        \
        \
        **HTML テーブルの作成**: JSON オブジェクトの配列から HTML テーブルを作成します。
        \
        \
        **アレイのフィルター処理**: 別の配列内にある、条件を満たす項目から配列を作成します。
        \
        \
        **結合**: 配列内のすべての項目から 1 つの文字列を作成し、それらの項目を指定の区切り記号で区切ります。
        \
        \
        **JSON の解析**: JSON コンテンツ内のプロパティとその値からわかりやすいトークンを作成して、ユーザーがそれらのプロパティをワークフロー内で使用できるようにします。
        \
        \
        **Select**:別の配列内の項目または値を変換し、それらの項目を指定のプロパティにマッピングすることにより、JSON オブジェクトで配列を作成します。
    :::column-end:::
    :::column:::
        ![日時日付/時刻アクション アイコン][date-time-icon]
        \
        \
        **日付と時刻**
        \
        \
        タイムスタンプの操作を実行します。
        \
        \
        **時間への追加**: 指定した数の単位をタイムスタンプに追加します。
        \
        \
        **タイム ゾーンの変換**: タイムスタンプをソース タイム ゾーンからターゲット タイム ゾーンに変換します。
        \
        \
        **現在の時刻**: 現在のタイムスタンプを文字列として返します。
        \
        \
        **未来の時間の取得**: 現在のタイムスタンプに指定した時刻単位を加えて返します。
        \
        \
        **過去の時間の取得**: 現在のタイムスタンプから指定した時刻単位を引いて返します。
        \
        \
        **時間からの減算**: タイムスタンプから時間単位数を減算します。
    :::column-end:::
    :::column:::
        [![変数アクション アイコン][variables-icon]][variables-doc]
        \
        \
        [**変数**][variables-doc]
        \
        \
        変数の操作を実行します。
        \
        \
        **配列変数に追加**: 変数によって保管される配列内に、値を最後の項目として挿入します。
        \
        \
        **文字列変数に追加**: 変数によって保管される文字列内に、値を最後の文字として挿入します。
        \
        \
        **変数の値を減らす**: 変数の値を一定値だけ減らします。
        \
        \
        **変数の値を増やす**: 変数の値を一定値だけ増やします。
        \
        \
        **変数を初期化する**: 変数を作成し、そのデータ型と初期値を宣言します。
        \
        \
        **変数の設定**: 既存の変数に異なる値を代入します。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-built-in-actions"></a>統合アカウントの組み込みアクション

Azure Logic Apps には、次の組み込みアクションが用意されています。これらは、マルチテナント、従量課金プランベースの Azure Logic Apps を使用する場合に統合アカウントが必要なものと、シングルテナント、Standard プランベースの Azure Logic Apps を使用する場合は統合アカウントが不要なものがあります。

> [!NOTE]
> マルチテナント、従量課金プランベースの Azure Logic Apps で統合アカウント アクションを使用する前に、[ロジック アプリ リソースを統合アカウントにリンクする](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)必要があります。 ただし、シングルテナント、Standard プランベースの Azure Logic Apps の場合、Liquid 操作や XML 操作など、一部の統合アカウント操作では、ロジック アプリ リソースを統合アカウントにリンクする必要はありません。 これらのアクションを使用するには、Liquid マップ、XML マップ、または XML スキーマが必要です。これらは、Azure portal でのそれぞれのアクションを通じてアップロードすることも、Visual Studio Code プロジェクトの **Artifacts** フォルダーに、それぞれの **Maps** および **Schemas** フォルダーを使用して追加することもできます。

:::row:::
    :::column:::
        [![フラット ファイルのデコード アイコン][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**フラット ファイルのデコード**][flat-file-decode-doc]
        \
        \
        取引先にコンテンツを送信する前に XML をエンコードします。
    :::column-end:::
    :::column:::
        [![フラット ファイルのエンコード アイコン][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**フラット ファイルエンコード**][flat-file-encode-doc]
        \
        \
        取引先からコンテンツを受け取った後、XML をデコードします。
    :::column-end:::
    :::column:::
        [![統合アカウント アイコン][integration-account-icon]][integration-account-doc]
        \
        \
        [**統合アカウント成果物の検索**<br>(*マルチテナントのみ*)][integration-account-doc]
        \
        \
        統合アカウントの取引先、契約、スキーマなどの成果物のカスタム メタデータを取得します。
    :::column-end:::
    :::column:::
        [![Liquid 操作アイコン][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid 操作**][json-liquid-transform-doc]
        \
        \
        Liquid のテンプレートを使用して次の形式を変換します。 <p><p>- JSON から JSON へ <br>- JSON からテキストへ <br>- XML から JSON へ <br>- XML からテキストへ
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![XML の変換アイコン][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML の変換**][xml-transform-doc]
        \
        \
        ソースの XML 形式を別の XML 形式に変換します。
    :::column-end:::
    :::column:::
        [![XML 検証アイコン][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML 検証**][xml-validate-doc]
        \
        \
        指定されたスキーマに対して XML ドキュメントを検証します。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Logic Apps から呼び出しできるカスタム API を作成する](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-in icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
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
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sql-server-icon]: ./media/apis-list/sql.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Built-in integration account connector icons -->
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[xml-transform-icon]: ./media/apis-list/xml-transform.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API を管理および発行するための Azure API Management サービス インスタンスを作成します。"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "App Service API Apps を使用してロジック アプリを統合します。"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage コネクタを使用して BLOB コンテナーのファイルを管理します。"
[azure-cosmos-db-doc]: ./connectors-create-api-cosmos-db.md "Azure Cosmos DB ドキュメントにアクセスして管理できるように Azure Cosmos DB に接続する"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs に接続して、ロジック アプリと Event Hubs 間でイベントを送受信できるようにします"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Azure Functions を使用してロジック アプリを統合します。"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus キュー、トピック、およびトピックサブスクリプションからのメッセージ管理"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "グループ、またはバッチとしてメッセージを処理します。"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "配列のフィルター処理や CSV と HTML のテーブルの作成などのデータ操作を実行します。"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "配列内のすべての項目に対して同じアクションを実行します。"
[http-doc]: ./connectors-native-http.md "ロジック アプリから HTTP または HTTPS エンドポイントを呼び出す"
[http-request-doc]: ./connectors-native-reqres.md "ロジック アプリで HTTP 要求を受信する"
[http-response-doc]: ./connectors-native-reqres.md "ロジック アプリで HTTP 要求に応答する"
[http-swagger-doc]: ./connectors-native-http-swagger.md "ロジック アプリから REST エンドポイントを呼び出す"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP または HTTPS エンドポイントからの特定のイベントを待機する"
[ibm-db2-doc]: ./connectors-create-api-db2.md "クラウドまたはオンプレミスの IBM DB2 に接続します。行の更新、テーブルの取得などを行います。"
[ibm-mq-doc]: ./connectors-create-api-mq.md "オンプレミスまたは Azure 内の IBM MQ に接続し、メッセージを送受信します。"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "ロジック アプリから JavaScript コード スニペットを追加して実行する"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "入れ子になったワークフローを使用してロジック アプリを統合します。"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "クエリ アクションで、配列の選択とフィルター処理を行います。"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "スケジュールに基づいてロジック アプリを実行する"
[schedule-delay-doc]: ./connectors-native-delay.md "次のアクションの実行を遅らせる"
[schedule-delay-until-doc]: ./connectors-native-delay.md "次のアクションの実行を遅らせる"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "定期的なスケジュールでロジック アプリを実行する"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "連続したチャンク内のデータを処理する必要があるロジック アプリを実行する"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "アクションをグループに編成します。グループ内のアクションの実行が完了すると、グループ独自のステータスが取得されます。"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH を使用して SFTP アカウントに接続します。ファイルのアップロード、取得、削除などを実行します。"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database または SQL Server に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除します"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "アクションをケースに編成します。ケースには、一意の値が割り当てられます。値が式、オブジェクト、またはトークンの結果に一致するケースのみを実行します。一致が存在しない場合は、既定のケースを実行します。"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "アクティブに実行中のロジック アプリのワークフローを停止またはキャンセルします。"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "指定された条件が true になるまで、または特定の状態が変化するまで、アクションを繰り返します。"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "変数に対する操作を実行します。たとえば、文字列または配列の変数に対する初期化、設定、増分、減分、追加などです。"

<!--Built-in integration account doc links-->
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "エンタープライズ統合フラット ファイルについて説明します"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "統合アカウント アーティファクトのメタデータを管理します"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid テンプレートを使用して JSON を変換します"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML メッセージ変換します"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML メッセージを検証します"
