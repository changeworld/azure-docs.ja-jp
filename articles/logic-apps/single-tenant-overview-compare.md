---
title: 概要 - シングルテナント Azure Logic Apps
description: Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境 (ISE) の違いについて説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, azla
ms.topic: conceptual
ms.date: 05/25/2021
ms.openlocfilehash: 1dd4a75cbebeb0244ffd3e0c1f8e2cfcd1968440
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379734"
---
# <a name="single-tenant-versus-multi-tenant-and-integration-service-environment-for-azure-logic-apps"></a>Azure Logic Apps でのシングルテナント、マルチテナント、統合サービス環境の比較

Azure Logic Apps は、アプリ、データ、サービス、およびシステムを統合する自動化された "*ロジック アプリ ワークフロー*" を作成および実行するためのクラウドベースのプラットフォームです。 このプラットフォームを使用すると、エンタープライズおよび企業間 (B2B) シナリオ向けのスケーラビリティの高い統合ソリューションを迅速に開発できます。 ロジック アプリを作成するには、**ロジック アプリ (従量課金)** または **ロジック アプリ (Standard)** のいずれかのリソースの種類を使用します。 従量課金のリソースの種類は、"*マルチテナント*" Azure Logic Apps または "*統合サービス環境*" で実行され、Standard のリソースの種類は "*シングルテナント*" Azure Logic Apps 環境で実行されます。

使用するリソースの種類を選択する前に、この記事を参照して、リソースの種類とサービス環境が相互にどのように比較されるかを確認してください。 次に、シナリオのニーズ、ソリューションの要件、およびワークフローをデプロイ、ホスト、実行する環境に基づいて、使用するのに最適な種類を決定できます。

Azure Logic Apps を初めて使用する場合は、次のドキュメントを参照してください。

* [Azure Logic Apps とは](logic-apps-overview.md)
* ["*ロジック アプリ ワークフロー*" とは](logic-apps-overview.md#logic-app-concepts)

<a name="resource-environment-differences"></a>

## <a name="resource-types-and-environments"></a>リソースの種類と環境

ロジック アプリ ワークフローを作成するには、シナリオ、ソリューションの要件、必要な機能、およびワークフローを実行する環境に基づいて、**ロジック アプリ** のリソースの種類を選択します。

次の表は、**ロジック アプリ (Standard)** と **ロジック アプリ (従量課金)** のリソースの種類の違いを簡単にまとめたものです。 また、ロジック アプリ ワークフローをデプロイするための "*シングルテナント*" 環境と "*マルチテナント*" および "*統合サービス環境 (ISE)* " の比較についても説明します。

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="resource-type-introduction"></a>

## <a name="logic-app-standard-resource"></a>ロジック アプリ (Standard) リソース

**ロジック アプリ (Standard)** のリソースの種類では、再設計されたシングルテナント Azure Logic Apps ランタイムが使用されています。 このコンテナー化されたランタイムでは [Azure Functions 拡張性モデル](../azure-functions/functions-bindings-register.md)が使用されます。また、Azure Functions ランタイムの拡張機能としてホストされています。 この設計により、ロジック アプリ ワークフローの移植性、柔軟性、パフォーマンス向上に加え、Azure Functions プラットフォームと Azure App Service エコシステムから継承されたその他の機能と利点が提供されます。

たとえば、Azure Functions アプリとその関数を実行できる場所であればどこでも、シングルテナント ベースのロジック アプリとそのワークフローを実行できます。 Azure Functions アプリで複数の関数をホストできるのと同様に、Standard のリソースの種類では、複数のワークフローをホストできるリソース構造が導入されています。 1 対多のマッピングでは、同じロジック アプリとテナント内のワークフローによってコンピューティングと処理リソースが共有され、その近接性によってパフォーマンスが向上します。 この構造は、ロジック アプリのリソースとワークフローの間に 1 対 1 のマッピングがある **ロジック アプリ (従量課金)** のリソースとは異なります。

移植性、柔軟性、およびパフォーマンス向上の詳細については、引き続き次のセクションを参照してください。 または、シングルテナントの Azure Logic Apps ランタイムと Azure Functions の拡張性の詳細について、次のドキュメントを参照してください。

* [あらゆる場所で実行される Azure Logic Apps - ランタイムの詳細](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)
* [Azure Functions の概要](../azure-functions/functions-overview.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](../azure-functions/functions-triggers-bindings.md)

<a name="portability"></a>
<a name="flexibility"></a>

### <a name="portability-and-flexibility"></a>移植性と柔軟性

**ロジック アプリ (Standard)** のリソースの種類を使用してロジック アプリを作成する場合は、シングルテナント サービス環境だけでなく、Azure 関数アプリとその関数を実行できる場所であればどこでもワークフローを実行できます。

たとえば、Visual Studio Code を **Azure Logic Apps (Standard)** 拡張機能と共に使用すると、Azure にデプロイせずに、開発環境で "*ローカルに*" ワークフローを開発、ビルド、および実行できます。 コンテナーが必要なシナリオの場合は、ロジック アプリをコンテナー化し、コンテナーとしてデプロイできます。

Azure 内で実行されている既存のりソースに対して開発を行う必要があるマルチテナント モデルと比較すると、これらの機能によって大幅な改善と大きなメリットを得ることができます。 また、**ロジック アプリ (従量課金)** のリソースのデプロイを自動化するためのマルチテナント モデルは、アプリとインフラストラクチャの両方のリソース プロビジョニングを結合して処理する Azure Resource Manager テンプレート (ARM テンプレート) に完全に基づいています。

**ロジック アプリ (Standard)** のリソースの種類では、アプリのデプロイとインフラストラクチャのデプロイを分離できるため、デプロイが容易になります。 シングルテナント Azure Logic Apps ランタイムとワークフローをロジック アプリの一部としてまとめてパッケージ化できます。 ロジック アプリのリソースをビルド、アセンブル、圧縮して、すぐにデプロイできる状態の成果物を生成する一般的な手順またはタスクを使用できます。 インフラストラクチャをデプロイする場合も、ARM テンプレートを使用して、それらの目的で使用する他のプロセスやパイプラインと共に、それらのリソースを別々にプロビジョニングできます。

アプリをデプロイするには、成果物をホスト環境にコピーしてから、アプリを開始してワークフローを実行します。 または、既に使ったことのあるツールとプロセスを使用して、成果物をデプロイ パイプラインに統合します。 そうすることで、開発に使用するテクノロジ スタックに関係なく、独自に選択したツールを使用してデプロイできます。

標準的なビルドとデプロイのオプションを使用することにより、インフラストラクチャのデプロイから切り離して、アプリの開発に集中できます。 その結果、プロジェクト モデルはより汎用的になり、汎用アプリに使用する多くの類似したまたは同じデプロイ オプションを適用できます。 また、アプリ プロジェクトを中心としたデプロイ パイプラインの構築と、運用環境に発行する前に必要なテストと検証の実行に関して、エクスペリエンスがいっそう一貫したものになるという利点もあります。

<a name="performance"></a>

### <a name="performance"></a>パフォーマンス

**ロジック アプリ (Standard)** のリソースの種類を使用すると、同じシングル ロジック アプリとテナント内に複数のワークフローを作成して実行できます。 この 1 対多のマッピングにより、これらのワークフローではコンピューティング、処理、ストレージ、ネットワークなどのリソースが共有され、その近接性によってパフォーマンスが向上します。

**ロジック アプリ (Standard)** のリソースの種類とシングルテナント Azure Logic Apps ランタイムでは、より一般的なマネージド コネクタを組み込み操作として使用できるようにすることで、もう 1 つの大幅な改善が提供されます。 たとえば、Azure Service Bus、Azure Event Hubs、SQL などに対して組み込み操作を使用できます。 一方、マネージド コネクタのバージョンは現在も利用でき、引き続き機能します。

新しい組み込み操作を使用する場合は、"*組み込み接続*" または "*サービス プロバイダー接続*" と呼ばれる接続を作成します。 それに対応するマネージド接続は "*API 接続*" と呼ばれます。これは、Azure リソースとして別個に作成されて実行され、ARM テンプレートを使用してデプロイする必要があります。 組み込み操作とその接続は、ワークフローを実行するのと同じプロセスでローカルに実行されます。 どちらも、シングルテナント Azure Logic Apps ランタイムでホストされます。 その結果、組み込み操作とその接続では、ワークフローとの近接性によってパフォーマンスが向上します。 サービス プロバイダーの接続が同じビルド成果物にパッケージされるため、この設計はデプロイ パイプラインでも適切に機能します。

## <a name="create-build-and-deploy-options"></a>作成、ビルド、およびデプロイのオプション

目的の環境に基づいてロジック アプリを作成するには、複数のオプションがあります。次に例を示します。

**シングルテナント環境**

| オプション | リソースとツール | 詳細情報 |
|--------|---------------------|------------------|
| Azure portal | **ロジック アプリ (Standard)** のリソースの種類 | [シングルテナント Logic Apps の統合ワークフローを作成する - Azure portal](create-single-tenant-workflows-azure-portal.md) |
| Visual Studio Code | [**Azure Logic Apps (Standard)** の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurelogicapps) | [シングルテナント Logic Apps の統合ワークフローを作成する - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) |
| Azure CLI | Logic Apps Azure CLI の拡張機能 | まだ使用できません |
||||

**マルチテナント環境**

| オプション | リソースとツール | 詳細情報 |
|--------|---------------------|------------------|
| Azure portal | **ロジック アプリ (従量課金)** のリソースの種類 | [クイックスタート: マルチテナント Azure Logic Apps で統合ワークフローを作成する - Azure portal](quickstart-create-first-logic-app-workflow.md) |
| Visual Studio Code | [**Azure Logic Apps (従量課金)** の拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) | [クイックスタート: マルチテナント Azure Logic Apps で統合ワークフローを作成する - Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
| Azure CLI | [**Logic Apps Azure CLI** の拡張機能](https://github.com/Azure/azure-cli-extensions/tree/master/src/logic) | - [クイックスタート: マルチテナント Azure Logic Apps で統合ワークフローを作成して管理する - Azure CLI](quickstart-logic-apps-azure-cli.md) <p><p>- [az logic](/cli/azure/logic) |
| Azure Resource Manager | [**ロジック アプリを作成する** Azure Resource Manager (ARM) テンプレート](https://azure.microsoft.com/resources/templates/101-logic-app-create/) | [クイックスタート: マルチテナント Azure Logic Apps で統合ワークフローを作成してデプロイする - ARM テンプレート](quickstart-create-deploy-azure-resource-manager-template.md) |
| Azure PowerShell | [Az.LogicApp モジュール](/powershell/module/az.logicapp) | [Azure PowerShell の概要](/powershell/azure/get-started-azureps) |
| Azure REST API | [Azure Logic Apps REST API](/rest/api/logic) | [Azure Rest API リファレンスの概要](/rest/api/azure) |
||||

**統合サービス環境**

| オプション | リソースとツール | 詳細情報 |
|--------|---------------------|------------------|
| Azure portal | 既存の ISE リソースを含む **ロジック アプリ(従量課金)** のリソースの種類 | [クイックスタート: マルチテナント Azure Logic Apps で統合ワークフローを作成する - Azure portal](quickstart-create-first-logic-app-workflow.md) と同じですが、マルチテナント リージョンではなく、ISE を選択します。 |
||||

**従量課金** と **Standard** のどちらのロジック アプリ リソースを作成するかによって開発エクスペリエンスは異なりますが、デプロイされたすべてのロジック アプリは Azure サブスクリプションの下にありアクセスできます。

たとえば、Azure portal では、 **[ロジック アプリ]** ページには **従量課金** と **Standard** の両方のロジック アプリ リソースの種類が表示されます。 Visual Studio Code では、デプロイされたロジック アプリは Azure サブスクリプションの下に表示されますが、使用された拡張機能、つまり **Azure: ロジック アプリ (従量課金)** と **Azure: ロジック アプリ (Standard)** によってグループ化されています。

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>ステートフルおよびステートレス ワークフロー

**ロジック アプリ (Standard)** のリソースの種類では、同じロジック アプリ内にこれらのワークフローを作成できます。

* *ステートレス*

  前のイベントのデータを保持、確認、または参照する必要がある場合は、ステートフル ワークフローを作成します。 これらのワークフローで、各アクションとその状態の入出力が外部ストレージに保存されます。これにより、各実行が完了した後にその実行の詳細と履歴を確認できます。 ステートフル ワークフローでは、サービス停止が発生した場合に高い回復性を実現できます。 サービスとシステムが復元された後に、中断された実行を保存済みの状態から再構築し、ワークフローを再実行して完了することができます。 ステートフル ワークフローは、ステートレス ワークフローよりもはるかに長い間実行を継続できます。

* *ステートレス*

  後で確認できるように外部ストレージに前のイベントのデータを保存、確認、または参照する必要がない場合は、ステートレス ワークフローを作成します。 これらのワークフローでは、このデータを外部ストレージに転送するのではなく、各アクションとその状態の入出力を "*メモリ内でのみ*" 保存します。 その結果、ステートレス ワークフローで通常は 5 分未満に実行時間が短縮され、パフォーマンスが高速化されて応答時間が短くなり、スループットが向上し、実行コストが削減されます。これは、実行の詳細と履歴が外部ストレージに保持されないためです。 しかし、サービス停止が発生した場合、中断された実行は自動的には復元されないため、呼び出し元は中断された実行を手動で再送信する必要があります。 これらのワークフローは同期的にのみ実行できます。

  デバッグをより容易にするために、ステートレス ワークフローの実行履歴を有効にし (この場合、パフォーマンスに何らかの影響があります)、その後、完了時に実行履歴を無効にすることができます。 詳細については、[Visual Studio Code でのシングルテナント ベースのワークフローの作成](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)または [Azure portal でのシングルテナント ベースのワークフローの作成](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)に関するページを参照してください。

  > [!NOTE]
  > ステートレスなワークフローでは、現在、トリガーではなく Azure にデプロイされている [マネージド コネクタ](../connectors/managed.md)に対する *アクション* のみがサポートされています。 ワークフローを開始するには、[組み込みの Request、Event Hubs、または Service Bus トリガー](../connectors/built-in.md)を選択します。 これらのトリガーは、Azure Logic Apps ランタイムでネイティブに実行されます。 制限付き、使用できない、またはサポートされていないトリガー、アクション、コネクタの詳細については、「[変更された、制限付き、使用できない、またはサポートされていない機能](#limited-unavailable-unsupported)」を参照してください。

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>ステートフルおよびステートレス ワークフローの入れ子になった動作の違い

[Request トリガー](../connectors/connectors-native-reqres.md)、[HTTP Webhook トリガー](../connectors/connectors-native-webhook.md)、または [ApiConnectionWebhook 型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)であり HTTPS 要求を受信できるマネージド コネクタ トリガーを使用することによって、同じ **ロジック アプリ (Standard)** リソースに存在する他のワークフローから [ワークフローを呼び出せるようにする](../logic-apps/logic-apps-http-endpoint.md)ことができます。

親ワークフローで子ワークフローが呼び出された後、入れ子になったワークフローで従うことができる動作パターンを次に示します。

* 非同期ポーリング パターン

  親は最初の呼び出しに対する応答を待機せず、子が実行を終了するまで、子の実行履歴を継続的にチェックします。 既定では、ステートフルなワークフローはこのパターンに従います。これは、[要求タイムアウト制限](../logic-apps/logic-apps-limits-and-config.md)を超える可能性がある、長時間実行される子ワークフローに適しています。

* 同期パターン ("ファイア アンド フォーゲット")

  子は `202 ACCEPTED` 応答を直ちに返すことによって呼び出しを確認し、親は子の結果を待たずに次のアクションに進みます。 代わりに、子の実行が終了すると、親は結果を受信します。 応答アクションを含まない子のステートフルなワークフローは、常に同期パターンに従います。 子ワークフローがステートフルである場合は、実行履歴を確認することができます。

  この動作を有効にするには、ワークフローの JSON 定義で、`operationOptions` プロパティを `DisableAsyncPattern` に設定します。 詳細については、[トリガーとアクションの種類 (操作オプション)](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) に関するページを参照してください。

* トリガーと待機

  子ワークフローがステートレスである場合、親は子からの結果を返す応答を待機します。 このパターンは、組み込みの [HTTP トリガーまたはアクション](../connectors/connectors-native-http.md)を使用して子ワークフローを呼び出す場合と同様に機能します。 応答アクションを含まないステートレスな子ワークフローは直ちに `202 ACCEPTED` 応答を返しますが、親は次のアクションに進む前に子が終了するまで待機します。 これらの動作は、ステートレスな子ワークフローにのみ適用されます。

次の表では、親と子がステートフル、ステートレス、または混合のどのワークフロー型であるかに基づいて、子ワークフローの動作を指定します。

| 親ワークフロー | 子ワークフロー | 子の動作 |
|-----------------|----------------|----------------|
| ステートレス | ステートレス | `"operationOptions": "DisableAsyncPattern"` 設定を使用した非同期または同期 |
| ステートレス | ステートレス | トリガーと待機 |
| ステートレス | ステートレス | Synchronous |
| ステートレス | ステートレス | トリガーと待機 |
||||

<a name="other-capabilities"></a>

## <a name="other-single-tenant-model-capabilities"></a>その他のシングルテナント モデルの機能

シングルテナント モデルと **ロジック アプリ (Standard)** のリソースの種類には、次のような現行と新規の機能が多数含まれています。

* サービスとしてのソフトウェア (SaaS) およびサービスとしてのプラットフォーム (PaaS) のアプリとサービス用の [400 以上のマネージド コネクタ](/connectors/connector-reference/connector-reference-logicapps-connectors)や、オンプレミス システム用のコネクタから、ロジック アプリとそのワークフローを作成します。

  * 組み込み操作として利用できるマネージド コネクタの数は増加しており、他の組み込み操作 (Azure Functions など) と同様に実行できます。 組み込み操作は、シングルテナント Azure Logic Apps ランタイムでネイティブに実行されます。 たとえば、新しい組み込み操作には Azure Service Bus、Azure Event Hubs、SQL Server、MQ などが含まれます。

    > [!NOTE]
    > 組み込みの SQL Server バージョンの場合、 **[クエリの実行]** アクションのみが、[オンプレミス データ ゲートウェイ](logic-apps-gateway-connection.md)を使用せずに Azure 仮想ネットワークに直接接続できます。

  * [シングルテナント Azure Logic Apps 機能拡張フレームワーク](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)を使用して、必要なサービス用の独自の組み込みコネクタを作成できます。 Azure Service Bus や SQL Server などの組み込み操作と似ていますが、現在サポートされていない[カスタム マネージド コネクタ](../connectors/apis-list.md#custom-apis-and-connectors)とは異なり、カスタムの組み込みコネクタはシングルテナント ランタイムと同じプロセスで実行されるため、より高いスループット、短い待機時間、ローカル接続が実現されます。

    作成機能は現在 Visual Studio Code でのみ使用できますが、既定では有効になっていません。 これらのコネクタを作成するには、[自分のプロジェクトを拡張バンドルベース (Node.js) から NuGet パッケージベース (.NET) に切り替え](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)ます。 詳細については、[あらゆる場所で実行される Azure Logic Apps - 組み込みコネクタの拡張機能](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)に関するページを参照してください。

  * 統合アカウントを使わずに、Liquid 操作と XML 操作に B2B アクションを使用することができます。 これらのアクションを使用するには、Liquid マップ、XML マップ、または XML スキーマが必要です。これらは、Azure portal でのそれぞれのアクションを通じてアップロードすることも、Visual Studio Code プロジェクトの **Artifacts** フォルダーに、それぞれの **Maps** および **Schemas** フォルダーを使用して追加することもできます。

  * ロジック アプリがクラウド接続ランタイム エンドポイントに要求を送信するために使用できる Shared Access Signature (SAS) 接続文字列字列が Azure Logic Apps サービスによって生成されるため、**ロジック アプリ (Standard)** リソースはどこでも実行できます。 Azure にデプロイするときにこれらの値を Azure Key Vault に簡単に格納できるように、Logic Apps サービスによりこれらの接続文字列が他のアプリケーション設定と共に保存されます。

    > [!NOTE]
    > 既定では、**ロジック アプリ (Standard)** リソースには、実行時に接続を認証するために自動的に有効にされる [システム割り当てマネージド ID](../logic-apps/create-managed-service-identity.md) があります。 この ID は、接続の作成時に使用する認証資格情報または接続文字列とは異なります。 この ID を無効にした場合、接続は実行時に機能しません。 この設定を表示するには、ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。

* ステートレス ワークフローはメモリ内でのみ実行されます。これにより、実行履歴やアクション間のデータが外部ストレージに保持されないため、処理時間が短縮され、応答が速くなり、スループットが向上し、実行にかかるコストも少なくなります。 必要に応じて、デバッグを容易にするために実行履歴を有効にすることができます。 詳細については、[ステートフルとステートレス ワークフローの比較](#stateful-stateless)に関する記事を参照してください。

* Visual Studio Code 開発環境でローカルにロジック アプリとそのワークフローを実行、テスト、デバッグできます。

  ロジック アプリを実行してテストする前に、ワークフローの **workflow.json** ファイル内にブレークポイントを追加して使用することで、デバッグをより簡単に行うことができます。 しかし、ブレークポイントは、現時点ではトリガーではなく、アクションに対してのみサポートされます。 詳細については、[Visual Studio Code でのシングルテナント ベースのワークフローの作成](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints)に関するページを参照してください。

* ロジック アプリとそのワークフローを Visual Studio Code から、Azure やコンテナーなどのさまざまなホスティング環境に直接発行またはデプロイします。

* Azure サブスクリプションとロジック アプリの設定でサポートされている場合は、[Application Insights](../azure-monitor/app/app-insights-overview.md) を使用してロジック アプリに対して診断ログとトレース機能を有効にします。

* [Azure Functions Premium プラン](../azure-functions/functions-premium-plan.md)を使用して、ロジック アプリを作成してデプロイする場合の Azure Functions と同様に、Azure 仮想ネットワークとのプライベートな接続や統合などのネットワーク機能にアクセスします。 詳細については、次のドキュメントを確認してください。

  * [Azure Functions のネットワーク オプション](../azure-functions/functions-networking-options.md)

  * [あらゆる場所で実行される Azure Logic Apps - Azure Logic Apps を使用したネットワークの可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* **ロジック アプリ (Standard)** リソースの個々のワークフローで使用されるマネージド接続のアクセス キーを再生成します。 このタスクでは、ロジック アプリのリソース レベルではなく、[個々のワークフロー レベルで、**ロジック アプリ (従量課金)** リソースに対して同じ手順を実行します](logic-apps-securing-a-logic-app.md#regenerate-access-keys)。

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>変更された、制限付き、使用できない、またはサポートされていない機能

**ロジック アプリ (Standard)** リソースでは、これらの機能は変更されているか、現在制限されているか、使用できないか、またはサポートされていません。

* **トリガーとアクション**: 組み込みのトリガーとアクションは、シングルテナント Azure Logic Apps ランタイムでネイティブに実行されますが、マネージド コネクタは Azure 内でホストされて実行されます。 スライディング ウィンドウやバッチなど、一部の組み込みトリガーは使用できません。 ステートフルまたはステートレス　ワークフローを開始するには、[組み込みの Recurrence、Request、HTTP、HTTP Webhook、Event Hubs、または Service Bus トリガー](../connectors/apis-list.md)を使用します。 デザイナーで、組み込みのトリガーとアクションは、 **[組み込み]** タブの下に表示されます。

  "*ステートフル*" ワークフローの場合、[マネージド コネクタのトリガーとアクション](../connectors/managed.md)は、以下に示す使用できない操作を除き、 **[Azure]** タブの下に表示されます。 "*ステートレス*" ワークフローの場合、トリガーを選択したいときに **[Azure]** タブは表示されません。 選択できるのは、[マネージド コネクタの "*アクション*" のみで、トリガーはできません](../connectors/managed.md)。 Azure でホストされているマネージド コネクタをステートレス ワークフローに対して有効にすることはできますが、デザイナーには、追加できるマネージド コネクタのトリガーは表示されません。

  > [!NOTE]
  > Visual Studio Code でローカルに実行するには、Webhook ベースのトリガーとアクションに追加の設定が必要です。 詳細については、[Visual Studio Code でのシングルテナント ベースのワークフローの作成](create-single-tenant-workflows-visual-studio-code.md#webhook-setup)に関するページを参照してください。

  * これらのトリガーとアクションは、変更されたか、現在制限されているか、サポートされていないか、使用できません。

    * [オンプレミス データ ゲートウェイの "*トリガー*"](../connectors/managed.md#on-premises-connectors) は使用できませんが、ゲートウェイのアクションは使用 "*できます*"。

    * 組み込みアクションである [[Azure Functions] - [Azure 関数を選択する]](logic-apps-azure-functions.md) は、 **[Azure Function Operations - Call an Azure function]\([Azure 関数の操作] - [Azure 関数を呼び出す]\)** になりました。 このアクションは、現在、**HTTP トリガー** テンプレートから作成された関数に対してのみ機能します。

      Azure portal では、ユーザー エクスペリエンスを通じて接続を作成することによってアクセスできる HTTP トリガー関数を選択できます。 Visual Studio Code を使用してコード ビューまたは **workflow.json** ファイルで関数アクションの JSON 定義を調べる場合、アクションでは `connectionName` 参照を使用して関数が参照されます。 このバージョンでは関数の情報は接続として抽象化されます。これは、Visual Studio Code で接続を作成した後に使用できるようになる、ロジック アプリ プロジェクトの **connections.json** ファイルで確認できます。

      > [!NOTE]
      > シングルテナント モデルの場合、関数アクションではクエリ文字列認証のみがサポートされます。 Azure Logic Apps では、接続を行っているときに関数から既定のキーが取得されます。そのキーはアプリの設定に格納され、関数を呼び出す際の認証に使用されます。
      >
      > マルチテナント モードと同様に、(たとえば、ポータルの Azure Functions エクスペリエンスを通じて) このキーを更新すると、無効なキーが原因で関数アクションは機能しなくなります。 この問題を解決するには、呼び出す関数への接続を再作成するか、新しいキーを使用してアプリの設定を更新する必要があります。

    * 組み込みの [インライン コード アクション](logic-apps-add-run-inline-code.md)は、**インライン コード操作** に名前が変更され、統合アカウントは不要になり、[制限が更新](logic-apps-limits-and-config.md)されました。

    * 組み込みアクションである [[Azure Logic Apps - Choose a Logic App workflow]\([Azure Logic Apps] - [ロジック アプリ ワークフローを選択する]\)](logic-apps-http-endpoint.md) は、 **[Workflow Operations - Invoke a workflow in this workflow app]\([ワークフロー操作] - [このワークフロー アプリでワークフローを呼び出す]\)** になりました。

    * [統合アカウントに対する組み込みの B2B トリガーとアクション](../connectors/managed.md#integration-account-connectors)の一部は使用できません。たとえば、**フラット ファイル** のエンコードやデコード アクションです。

    * 現在、[カスタムのマネージド コネクタ](../connectors/apis-list.md#custom-apis-and-connectors)はサポートされていません。 ただし、Visual Studio Code を使用する場合は、"*カスタムの組み込み操作*" を作成できます。 詳細については、[Visual Studio Code を使用したシングルテナント ベースのワークフローの作成](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)に関するページを参照してください。

* **Visual Studio Code でのデバッグのブレークポイント**: ワークフローの **workflow.json** ファイル内にブレークポイントを追加して使用することはできますが、ブレークポイントは現時点ではトリガーではなく、アクションに対してのみサポートされます。 詳細については、[Visual Studio Code でのシングルテナント ベースのワークフローの作成](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints)に関するページを参照してください。

* **トリガー履歴と実行履歴**: **ロジック アプリ (Standard)** のリソースの種類の場合、Azure portal のトリガー履歴と実行履歴は、ロジック アプリ レベルではなくワークフロー レベルで表示されます。 詳細については、[Azure portal を使用したシングルテナント ベースのワークフローの作成](create-single-tenant-workflows-azure-portal.md)に関するページを参照してください。

* **ズーム コントロール**: 現在、デザイナーでズーム コントロールは使用できません。

* **デプロイ ターゲット**: **ロジック アプリ (Standard)** のリソースの種類は、[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) にも Azure デプロイ スロットにもデプロイできません。

* **Azure API Management**: 現在、**ロジック アプリ (Standard)** のリソースの種類を Azure API Management にインポートすることはできません。 ただし、**ロジックアプリ (従量課金)** のリソースの種類はインポートできます。

<a name="firewall-permissions"></a>

## <a name="strict-network-and-firewall-traffic-permissions"></a>厳格なネットワークとファイアウォール トラフィックのアクセス許可

お使いの環境に、トラフィックを制限する厳しいネットワーク要件またはファイアウォールがある場合、お使いのロジック アプリのワークフローですべてのトリガーまたはアクション接続にアクセスを許可する必要があります。 これらの接続の完全修飾ドメイン名 (FQDN) は、次のトピックの該当セクションでご確認ください。

* [シングルテナント ロジック アプリのファイヤーウォールのアクセス許可 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)
* [シングルテナント ロジック アプリのファイヤーウォールのアクセス許可 - Azure portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)

## <a name="next-steps"></a>次の手順

* [Azure portal でシングルテナント ベースのワークフローを作成する](create-single-tenant-workflows-azure-portal.md)
* [Visual Studio Code でシングルテナント ベースのワークフローを作成する](create-single-tenant-workflows-visual-studio-code.md)

また、シングルテナント Azure Logic Apps でのエクスペリエンスについてご意見をお聞かせください。

* バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
* 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/lafeedback)。
