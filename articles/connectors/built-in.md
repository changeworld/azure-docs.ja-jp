---
title: Azure Logic Apps の組み込みのトリガーとアクション
description: 組み込みのトリガーとアクションを使用することで、Azure Logic Apps を使用して、アプリ、データ、サービス、システムを統合する自動ワークフローを作成し、ワークフローを制御し、データを管理します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796336"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Logic Apps の組み込みのトリガーとアクション


[組み込みのトリガーとアクション](apis-list.md)により、[ワークフローのスケジュールと構造の制御](#control-workflow)、[独自のコードの実行](#run-code-from-workflows)、[データの管理または操作](#manage-or-manipulate-data)、ワークフロー内の他のタスクの実行を行う手段が提供されます。 [マネージド コネクタ](managed.md)とは異なり、多くの組み込み操作は、特定のサービス、システム、またはプロトコルに関連付けられていません。 たとえば、繰り返しトリガーを使用すると、ほぼどのようなワークフローでもスケジュールに従って開始できます。 または、要求トリガーを使用することで、呼び出されるまでワークフローを待機させることもできます。 すべての組み込み操作は Logic Apps サービスでネイティブに実行され、ほとんどの場合、その使用前に接続を作成する必要はありません。 

また、Logic Apps には、Azure Service Bus、Azure Functions、SQL、AS2 など、少数のサービス、システム、プロトコル用の組み込み操作も用意されています。 数と範囲は、マルチテナント ロジック アプリとシングルテナント ロジック アプリのどちらを作成するかによって異なります。 一部のケースでは、組み込みバージョンとマネージド コネクタ バージョンの両方を使用できます。 ほとんどの場合、組み込みのバージョンの方がパフォーマンス、機能、価格などに優れています。 たとえば、[AS2 プロトコルを使用して B2B メッセージを交換](../logic-apps/logic-apps-enterprise-integration-as2.md)する場合、(非推奨の) マネージド コネクタ バージョンでのみ使用できる追跡機能が必要でなければ、組み込みバージョンを選択してください。

次の一覧では、[組み込みのトリガーとアクション](#understand-triggers-and-actions)で実行できるタスクの一部についてのみ説明します。

- カスタムおよび詳細なスケジュールを使用してワークフローを実行します。 スケジュール設定の詳細については、[Logic Apps コネクタの概要にある「繰り返しの動作」セクション](apis-list.md#recurrence-behavior)を参照してください。
- ワークフローの構造を、たとえばループと条件を使用して、整理および制御します。
- 変数、日付、データ操作、コンテンツ変換、バッチ操作を処理します。
- HTTP トリガーとアクションを使用して、他のエンドポイントと通信します。
- 要求を受信して応答します。
- 独自の関数 (Azure Functions)、Web アプリ (Azure App Services)、API (Azure API Management)、要求を受け取ることができる他の Logic Apps ワークフローなどを呼び出します。

## <a name="understand-triggers-and-actions"></a>トリガーとアクションを理解する

Logic Apps には、次の組み込みのトリガーとアクションが用意されています。

:::row:::
    :::column:::
        [![Logic Apps 内のスケジュールのアイコン][schedule-icon]][schedule-doc]
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
        [![Logic Apps 内のバッチのアイコン][batch-icon]][batch-doc]
        \
        \
        [**バッチ**][batch-doc]
        \
        \
        [**メッセージのバッチ処理**][batch-doc]: バッチ内のメッセージを処理するワークフローをトリガーします。
        \
        \
        [**バッチ処理するメッセージの送信**][batch-doc]: **[メッセージのバッチ処理]** トリガーで現在開始されている既存のワークフローを呼び出します。
    :::column-end:::
    :::column:::
        [![Logic Apps 内の HTTP のアイコン][http-icon]][http-doc]
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
        [![要求のアイコン][http-request-icon]][http-request-doc]
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
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps 内の Azure API Management のアイコン][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        [Azure API Management](../api-management/api-management-key-concepts.md) を使用して、定義、管理、発行する API で独自のトリガーとアクションを呼び出します。 <p><p>**注**: [API Management で従量課金レベル](../api-management/api-management-features.md)を使用している場合はサポートされません。
    :::column-end:::
    :::column:::
        [![Logic Apps 内の Azure App Service のアイコン][azure-app-services-icon]][azure-app-services-doc]
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
        [![Logic Apps 内の Azure Logic Apps のアイコン][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        **[HTTP 要求の受信時]** という名前の要求トリガーで始まる他のワークフローを呼び出します。
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>ワークフローからコードを実行する

Logic Apps には、ワークフローで独自のコードを実行するための組み込みアクションが用意されています。

:::row:::
    :::column:::
        [![Logic Apps 内の Azure Functions のアイコン][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        [Azure でホストされている関数](../azure-functions/functions-overview.md)を呼び出して、ワークフロー内で独自の "*コード スニペット*" (C# または Node.js) を実行します。
    :::column-end:::
    :::column:::
        [![Logic Apps 内のインライン コードのアイコン][inline-code-icon]][inline-code-doc]
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

Logic Apps では、ワークフローでアクションを構成および制御するための組み込みアクションが提供されています。

:::row:::
    :::column:::
        [![Logic Apps 内の Condition アクションのアイコン][condition-icon]][condition-doc]
        \
        \
        [**Condition**][condition-doc]
        \
        \
        条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。
    :::column-end:::
    :::column:::
        [![Logic Apps 内の For Each アクションのアイコン][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        配列内のすべての項目に対して同じアクションを実行します。
    :::column-end:::
    :::column:::
        [![Logic Apps 内の Scope アクションのアイコン][scope-icon]][scope-doc]
        \
        \
        [**Name**][scope-doc]
        \
        \
        アクションを *スコープ* にグループ化します。スコープ内のアクションが実行を完了すると、スコープ独自のステータスが取得されます。
    :::column-end:::
    :::column:::
        [![Logic Apps 内の Switch アクションのアイコン][switch-icon]][switch-doc]
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
        [![Logic Apps 内の Terminate アクションのアイコン][terminate-icon]][terminate-doc]
        \
        \
        [**Terminate**][terminate-doc]
        \
        \
        アクティブに実行中のロジック アプリ ワークフローを停止します。 
    :::column-end:::
    :::column:::
        [![Logic Apps 内の Until アクションのアイコン][until-icon]][until-doc]
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

Logic Apps では、データ出力とその形式を操作するための組み込みアクションが提供されています。

:::row:::
    :::column:::
        [![Logic Apps 内のデータ操作アクションのアイコン][data-operations-icon]][data-operations-doc]
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
        ![Logic Apps 内の日付と時刻アクションのアイコン][date-time-icon]
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
        [![Logic Apps 内の変数アクションのアイコン][variables-icon]][variables-doc]
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

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps から呼び出しできるカスタム API の作成](/logic-apps/logic-apps-create-api-app)

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


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API を管理および発行するための Azure API Management サービス インスタンスを作成します。"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "App Service API Apps を使用してロジック アプリを統合します。"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Azure Functions を使用してロジック アプリを統合します。"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "グループ、またはバッチとしてメッセージを処理します。"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "条件を評価し、条件が true と false のいずれであるかに基づいて、さまざまなアクションを実行します。"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "配列内のすべての項目に対して同じアクションを実行します。"
[http-doc]: ./connectors-native-http.md "ロジック アプリから HTTP または HTTPS エンドポイントを呼び出す"
[http-request-doc]: ./connectors-native-reqres.md "ロジック アプリで HTTP 要求を受信する"
[http-response-doc]: ./connectors-native-reqres.md "ロジック アプリで HTTP 要求に応答する"
[http-swagger-doc]: ./connectors-native-http-swagger.md "ロジック アプリから REST エンドポイントを呼び出す"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP または HTTPS エンドポイントからの特定のイベントを待機する"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "ロジック アプリから JavaScript コード スニペットを追加して実行する"
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
