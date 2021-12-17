---
title: シングルテナントの Azure Logic Apps でランタイムと環境の設定を編集する
description: シングルテナントの Azure Logic Apps でロジック アプリのランタイムと環境の設定を変更します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: eec377641eca7dd31ab04e9f6cdee949352d4a61
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491148"
---
# <a name="edit-host-and-app-settings-for-logic-apps-in-single-tenant-azure-logic-apps"></a>シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する

"*シングルテナント*" の Azure Logic Apps を使用する場合、ロジック アプリの "*アプリ設定*" で、そのロジック アプリの "*すべてのワークフロー*" に影響を与えるグローバル構成オプションを指定します。 ただし、これらの設定は、このようなワークフローが "*ローカル開発環境*" で実行される場合に "*のみ*" 適用されます。 ローカルでの実行中、これらのアプリ設定は、環境間で変わることが多い値に対してローカル開発ツールで使用される "*ローカル環境変数*" として、ワークフローからアクセスできます。 たとえば、これらの値には接続文字列を含めることができます。 Azure にデプロイするとき、アプリ設定は無視され、デプロイには含まれません。

また、ロジック アプリには "*ホスト設定*" もあります。これは、"*ローカルと Azure のどちらで実行される場合でも*" そのロジック アプリ内の "*すべてのワークフロー*" に適用されるランタイム構成の設定と値を指定するもので、例として、スループット、容量、データ サイズなどの既定値があります。

<a name="app-settings-parameters-deployment"></a>

## <a name="app-settings-parameters-and-deployment"></a>アプリ設定、パラメーター、デプロイ

"*マルチテナント*" の Azure Logic Apps でのデプロイは、Azure Resource Manager テンプレート (ARM テンプレート) が基になっており、それによってロジック アプリとインフラストラクチャの両方のリソース プロビジョニングが組み合わされて処理されます。 この設計では、さまざまな開発、テスト、運用環境でロジック アプリの環境変数を維持する必要がある場合に、課題が生じます。 ARM テンプレート内のすべては、デプロイ時に定義されます。 1 つの変数を変更するだけでよい場合も、すべてを再デプロイする必要があります。

"*シングルテナント*" の Azure Logic Apps の場合は、アプリとインフラストラクチャの間でリソースのプロビジョニングを分離できるため、デプロイが簡単になります。 "*パラメーター*" を使用して、環境間で変わる可能性のある値を抽象化できます。 ワークフローで使用するパラメーターを定義することで、まずワークフローの設計に集中し、後で環境固有の変数を挿入できます。 アプリ設定とパラメーターを使用して、実行時に環境変数を呼び出して参照できます。 そうすることで、頻繁に再デプロイする必要がなくなります。

アプリ設定は、Azure Key Vault と統合できます。 接続文字列やキーなど、[セキュリティで保護された文字列を直接参照](../app-service/app-service-key-vault-references.md)できます。 デプロイ時に環境変数を定義できる Azure Resource Manager テンプレート (ARM テンプレート) と同様に、[ロジック アプリのワークフロー定義](/azure/templates/microsoft.logic/workflows)内にアプリ設定を定義できます。 そうしておいて、接続エンドポイントやストレージ文字列などの動的に生成されるインフラストラクチャ値を取得できます。 ただし、アプリ設定にはサイズの制限があり、Azure Logic Apps の特定の領域からは参照できません。

デプロイ用にロジック アプリを設定する方法の詳細については、次のドキュメントを参照してください。

- [シングルテナントの Azure Logic Apps 用に環境間で変わるワークフロー内の値のパラメーターを作成する](parameterize-workflow-app.md)
- [シングルテナント ベースのロジック アプリ用の DevOps デプロイの概要](devops-deployment-single-tenant-azure-logic-apps.md)
- [シングルテナント ベースのロジック アプリ用の DevOps デプロイを設定する](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="visual-studio-code-project-structure"></a>Visual Studio Code プロジェクトの構造

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="reference-local-settings-json"></a>

## <a name="reference-for-app-settings---localsettingsjson"></a>アプリ設定のリファレンス - local.settings.json

Visual Studio Code で、ロジック アプリ プロジェクトのルート レベルにある **local.settings.json** ファイルには、ローカル開発環境での実行中にそのロジック アプリ内の "*すべてのワークフロー*" に影響を与える、グローバル構成オプションが含まれています。 ワークフローがローカルで実行されている場合、これらの設定はローカル環境変数としてアクセスされます。それらの値は、多くの場合、ワークフローを実行するさまざまな環境間で変わる可能性があります。 これらの設定を表示および管理するには、「[アプリ設定の管理 - local.settings.json](#manage-app-settings)」をご確認ください。

Azure Logic Apps のアプリ設定は、Azure Functions または Azure Web Apps のアプリ設定と同様に機能します。 以前にこれらの他のサービスを使用したことがある場合は、既にアプリ設定に慣れていることもあるでしょう。 詳細については、「[Azure Functions のアプリ設定のリファレンス](../azure-functions/functions-app-settings.md)」と[「Azure Functions Core Tools の操作」の「ローカル設定ファイル」](../azure-functions/functions-develop-local.md#local-settings-file)をご確認ください。

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `AzureWebJobsStorage` | なし | Azure ストレージ アカウントの接続文字列を設定します。 |
| `Workflows.<workflowName>.FlowState` | なし | <*workflowName*> の状態を設定します。 |
| `Workflows.<workflowName>.RuntimeConfiguration.RetentionInDays` | なし | <*workflowName*> の操作オプションを設定します。 |
| `Workflows.Connection.AuthenticationAudience` | なし | Azure でホストされている接続を認証する対象ユーザーを設定します。 |
| `Workflows.WebhookRedirectHostUri` | なし | Webhook コールバック URL に使用するホスト名を設定します。 |
| `WEBSITE_LOAD_ROOT_CERTIFICATES` | なし | 信頼するルート証明書のサムプリントを設定します。 |
||||

<a name="manage-app-settings"></a>

## <a name="manage-app-settings---localsettingsjson"></a>アプリ設定の管理 - local.settings.json

アプリ設定を追加、更新、または削除するには、Visual Studio Code、Azure portal、Azure CLI、または ARM (Bicep) の各テンプレートについて、以下のセクションを選択してご確認ください。 ロジック アプリ固有のアプリ設定については、「[使用可能なアプリ設定のリファレンス ガイド - local.settings.json](#reference-local-settings-json)」をご確認ください。

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code でロジック アプリのアプリ設定を確認するには、次の手順に従います。

1. ロジック アプリ プロジェクトのルート プロジェクト レベルで、**local.settings.json** ファイルを見つけて開きます。

1. `Values` オブジェクトで、ロジック アプリのアプリ設定を確認します。

   これらの設定の詳細については、「[使用可能なアプリ設定のリファレンス ガイド - local.settings.json](#reference-local-settings-json)」をご確認ください。

アプリ設定を追加するには、次の手順に従います。

1. **local.settings.json** ファイル内で `Values` オブジェクトを見つけます。

1. `Values` オブジェクトで、Visual Studio Code でローカルに実行するときに適用するアプリ設定を追加します。 一部の設定を使用すると、特定のワークフローの設定を追加できます。次に例を示します。

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "Workflows.WorkflowName1.FlowState" : "Disabled",
         <...>
     }
   }
   ```

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Azure portal でシングルテナント ベースのロジック アプリのアプリ設定を確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の検索ボックスでロジック アプリを検索し、開きます。

1. ロジック アプリのメニューにある **[設定]** で、 **[構成]** を選択します。

1. **[構成]** ページの **[アプリケーション設定]** タブで、ロジック アプリのアプリ設定を確認します。

   これらの設定の詳細については、「[使用可能なアプリ設定のリファレンス ガイド - local.settings.json](#reference-local-settings-json)」をご確認ください。

1. すべての値を表示するには、 **[値を表示する]** を選択します。 または、1 つの値を表示するには、その値を選択します。

設定を追加するには、次の手順に従います。

1. **[アプリケーション設定]** タブの **[アプリケーション設定]** で、 **[新しいアプリケーション設定]** を選択します。

1. **[名前]** に、新しい設定の "*キー*" または名前を入力します。

1. **[値]** には、新しい設定の値を入力します。

1. 新しい "*キーと値*" のペアを作成する準備ができたら、 **[OK]** を選択します。

:::image type="content" source="./media/edit-app-settings-host-settings/portal-app-settings-values.png" alt-text="シングルテナント ベースのロジック アプリ用のアプリ設定が表示された Azure portal と構成ウィンドウを示すスクリーンショット。" lightbox="./media/edit-app-settings-host-settings/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して現在のアプリ設定を確認するには、コマンド `az logicapp config appsettings list` を実行します。 このコマンドに `--name -n` および `--resource-group -g` パラメーターを必ず含めてください。次に例を示します。

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

これらの設定の詳細については、「[使用可能なアプリ設定のリファレンス ガイド - local.settings.json](#reference-local-settings-json)」をご確認ください。

Azure CLI を使用してアプリ設定を追加または更新するには、コマンド `az logicapp config appsettings set` を実行します。 このコマンドに `--name n` および `--resource-group -g` パラメーターを必ず含めてください。 たとえば、次のコマンドを使用して、`CUSTOM_LOGIC_APP_SETTING` という名前のキーと値 `12345` を持つ設定を作成します。

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

---

<a name="reference-host-json"></a>

## <a name="reference-for-host-settings---hostjson"></a>ホスト設定のリファレンス - host.json

Visual Studio Code で、ロジック アプリ プロジェクトのルート レベルにある **host.json** メタデータ ファイルには、ローカルと Azure のどちらで実行されている場合でもロジック アプリ リソース内の "*すべてのワークフロー*" に適用される、ランタイム設定と既定値が含まれています。 これらの設定を表示および管理するには、「[ホスト設定の管理 - host.json](#manage-host-settings)」をご確認ください。 また、関連する制限の情報については、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md#definition-limits)に関するドキュメントも参照してください。

<a name="job-orchestration"></a>

### <a name="job-orchestration-throughput"></a>ジョブ オーケストレーションのスループット

これらの設定は、シングルテナントの Azure Logic Apps でワークフロー操作を実行するためのスループットと容量に影響します。

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Jobs.BackgroundJobs.DispatchingWorkersPulseInterval` | `00:00:01` <br>(1 秒) | 前のポーリングでジョブが返されなかった場合にジョブ ディスパッチャーでジョブ キューをポーリングする間隔を設定します。 ジョブ ディスパッチャーは、前のポーリングでジョブが返された直後にキューをポーリングします。 |
| `Jobs.BackgroundJobs.NumWorkersPerProcessorCount` | `192` 個のディスパッチャー ワーカー インスタンス | プロセッサ コアあたりの "*ディスパッチャー ワーカー インスタンス*" または "*ジョブ ディスパッチャー*" の数を設定します。 この値は、コアあたりのワークフロー実行数に影響します。 |
| `Jobs.BackgroundJobs.NumPartitionsInJobTriggersQueue` | `1` 個のジョブ キュー | ジョブで処理する、ジョブ ディスパッチャーによって監視されるジョブ キューの数を設定します。 この値は、ジョブ キューが存在するストレージ パーティションの数にも影響します。 |
| `Jobs.BackgroundJobs.NumPartitionsInJobDefinitionsTable` | `4` 個のジョブ パーティション | ジョブ定義テーブル内のジョブ パーティションの数を設定します。 この値を使用して、パーティション ストレージの制限によって実行スループットがどれだけ影響を受けるかを制御します。 |
||||

<a name="run-duration-history"></a>

### <a name="run-duration-and-history"></a>実行の継続時間と履歴

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.FlowRetentionThreshold` | `90.00:00:00` <br>(90 日) | 実行の開始後、ワークフローの実行履歴を保持する時間。 |
| `Runtime.Backend.FlowRunTimeout` | `90.00:00:00` <br>(90 日) | タイムアウトを強制するまでの、ワークフローの実行を継続できる時間。 <p><p>**重要**: この値は必ず `Runtime.FlowRetentionThreshold` の値と等しいかそれより小さくする必要があります。 そうしないと、関連付けられているジョブが完了する前に実行履歴が削除される可能性があります。 |
||||
   
<a name="run-actions"></a>

### <a name="run-actions"></a>実行アクション

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.ActionJobExecutionTimeout` | `00:10:00` <br>(10 分) | 実行するワークフロー アクション ジョブがタイムアウトして再試行するまでの時間を設定します。 |
||||

<a name="inputs-outputs"></a>

### <a name="inputs-and-outputs"></a>入力と出力

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.FlowRunActionJob.MaximumActionResultSize` | `209715200` バイト | アクションで保持できる入力と出力の組み合わせの最大サイズをバイト単位で設定します。 |
| `Runtime.ContentLink.MaximumContentSizeInBytes` | `104857600` 文字 | トリガーまたはアクションで入力または出力に含めることができる最大サイズを文字数で設定します。 |
||||

<a name="pagination"></a>

### <a name="pagination"></a>改ページ位置の自動修正

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPageCount` | `1000` ページ | 操作で改ページがサポートされ、有効になっている場合に、実行時に返すまたは処理するページの最大数を設定します。 |
||||

<a name="chunking"></a>

### <a name="chunking"></a>チャンキング

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumContentLengthInBytesForPartialContent` | `1073741824` バイト | 操作でチャンク処理がサポートされ、有効になっている場合に、ダウンロードまたはアップロードされるコンテンツの最大サイズをバイト単位で設定します。 |
| `Runtime.FlowRunRetryableActionJobCallback.MaxChunkSizeInBytes` | `52428800` バイト | 操作でチャンク処理がサポートされ、有効になっている場合に、各コンテンツ チャンクの最大サイズをバイト単位で設定します。 |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumRequestCountForPartialContent` | `1000` 個の要求 | 操作でチャンク処理がサポートされ、有効になっている場合に、1 回のアクション実行でコンテンツをダウンロードするために行うことができる要求の最大数を設定します。 |
||||

<a name="trigger-concurrency"></a>

### <a name="trigger-concurrency"></a>トリガーのコンカレンシー

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Trigger.MaximumRunConcurrency` | 実行数 `100` | トリガーによって開始できる同時実行の最大数を設定します。 この値は、トリガーの同時実行の定義に表示されます。 |
| `Runtime.Trigger.MaximumWaitingRuns` | 実行数 `200` | 同時実行の最大数に達した後に待機できる実行の最大数を設定します。 この値は、トリガーの同時実行の定義に表示されます。 |
||||

<a name="for-each-loop"></a>

### <a name="for-each-loops"></a>For each ループ

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Backend.FlowDefaultForeachItemsLimit` | `100000` <br>(10 万個の配列項目) | "*ステートフル ワークフロー*" に対して、`For each` ループ内で処理する配列項目の最大数を設定します。 |
| `Runtime.Backend.Stateless.FlowDefaultForeachItemsLimit` | `100` 項目 | "*ステートレス ワークフロー*" に対して、`For each` ループ内で処理する配列項目の最大数を設定します。 |
| `Runtime.Backend.ForeachDefaultDegreeOfParallelism` | `20` イテレーション | `For each` ループ内のコンカレント イテレーション (並列処理の次数) の既定の数を設定します。 順次実行するには、値を `1` に設定します。 |
| `Runtime.Backend.FlowDefaultSplitOnItemsLimit` | `100000` <br>(10 万個の配列項目) | `SplitOn` の設定に基づいて、バッチ解除つまり複数のワークフロー インスタンスへの分割を行う配列項目の最大数を設定します。 |
||||

<a name="until-loop"></a>

### <a name="until-loops"></a>Until ループ

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Backend.MaximumUntilLimitCount` | `5000` イテレーション | "*ステートフル ワークフロー*" の場合は、`Until` アクションの `Count` プロパティの可能な最大数を設定します。 |
| `Runtime.Backend.Stateless.MaximumUntilLimitCount` | `100` イテレーション | "*ステートレス ワークフロー*" の場合は、`Until` アクションの `Count` プロパティの可能な最大数を設定します。 |
| `Runtime.Backend.Stateless.FlowRunTimeout` | `00:05:00` <br>(5 分) | ステートレス ワークフロー内の `Until` ループの最大待機時間を設定します。 |
||||

<a name="variables"></a>

### <a name="variables"></a>変数

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Backend.DefaultAppendArrayItemsLimit` | `100000` <br>(10 万個の配列項目) | 配列型の変数内の項目の最大数を設定します。 |
| `Runtime.Backend.VariableOperation.MaximumVariableSize` | ステートフル ワークフロー: `104857600` 文字 | ステートフル ワークフローで使用した場合に、変数に格納できるコンテンツの最大サイズを文字数で設定します。 |
| `Runtime.Backend.VariableOperation.MaximumStatelessVariableSize` | ステートレス ワークフロー: `1024` 文字 | ステートレス ワークフローで使用した場合に、変数に格納できるコンテンツの最大サイズを文字数で設定します。 |
||||

<a name="http-webhook"></a>

### <a name="http-operations"></a>HTTP 操作

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Backend.HttpOperation.RequestTimeout` | `00:03:45` <br>(3 分 45 秒) | HTTP トリガーとアクションの要求タイムアウト値を設定します。 |
| `Runtime.Backend.HttpOperation.MaxContentSize` | `104857600` バイト | HTTP トリガーとアクションの最大要求サイズをバイト単位で設定します。 |
| `Runtime.Backend.HttpOperation.DefaultRetryCount` | 再試行回数 `4` | HTTP トリガーとアクションの既定の再試行回数を設定します。 |
| `Runtime.Backend.HttpOperation.DefaultRetryInterval` | `00:00:07` <br>(7 秒) | HTTP トリガーとアクションの既定の再試行間隔を設定します。 |
| `Runtime.Backend.HttpOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 時間) | HTTP トリガーとアクションの最大再試行間隔を設定します。 |
| `Runtime.Backend.HttpOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 秒) | HTTP トリガーとアクションの最小再試行間隔を設定します。 |
||||

<a name="http-webhook"></a>

### <a name="http-webhook-operations"></a>HTTP Webhook 操作

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Backend.HttpWebhookOperation.RequestTimeout` | `00:02:00` <br>(2 分) | HTTP Webhook トリガーとアクションの要求タイムアウト値を設定します。 |
| `Runtime.Backend.HttpWebhookOperation.MaxContentSize` | `104857600` バイト | HTTP Webhook トリガーとアクションの最大要求サイズをバイト単位で設定します。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryCount` | 再試行回数 `4` | HTTP Webhook トリガーとアクションの既定の再試行回数を設定します。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryInterval` | `00:00:07` <br>(7 秒) | HTTP Webhook トリガーとアクションの既定の再試行間隔を設定します。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 時間) | HTTP Webhook トリガーとアクションの最大再試行間隔を設定します。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 秒) | HTTP Webhook トリガーとアクションの最小再試行間隔を設定します。 |
| `Runtime.Backend.HttpWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1 時間) | HTTP Webhook トリガーとアクションジョブの既定のウェイクアップ間隔を設定します。 |
||||

<a name="built-in-azure-functions"></a>

### <a name="built-in-azure-functions-operations"></a>組み込みの Azure Functions 操作

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Backend.FunctionOperation.RequestTimeout` | `00:03:45` <br>(3 分 45 秒) | Azure Functions アクションの要求タイムアウト値を設定します。 |
| `Runtime.Backend.FunctionOperation.MaxContentSize` | `104857600` バイト | Azure Functions アクションの最大要求サイズをバイト単位で設定します。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryCount` | 再試行回数 `4` | Azure Functions アクションの既定の再試行回数を設定します。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryInterval` | `00:00:07` <br>(7 秒) | Azure Functions アクションの既定の再試行間隔を設定します。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 時間) | Azure Functions アクションの最大再試行間隔を設定します。 |
| `Runtime.Backend.FunctionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 秒) | Azure Functions アクションの最小再試行間隔を設定します。 |
||||

<a name="built-in-sql"></a>

### <a name="built-in-sql-operations"></a>組み込みの SQL 操作

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.ServiceProviders.Sql.QueryExecutionTimeout` | `00:00:30` <br>(30 秒) | SQL サービス プロバイダーの操作の要求タイムアウト値を設定します。 |
||||

<a name="built-in-service-bus"></a>

### <a name="built-in-azure-service-bus-operations"></a>組み込みの Azure Service Bus 操作

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `ServiceProviders.ServiceBus.MessageSenderOperationTimeout` | `00:01:00` <br>(1 分) | 組み込みの Service Bus 操作でメッセージを送信するときのタイムアウトを設定します。 |
| `Runtime.ServiceProviders.ServiceBus.MessageSenderPoolSizePerProcessorCount` | `64` 個のメッセージ送信元 | メッセージ送信側プール内で使用するプロセッサ コアあたりの Azure Service Bus メッセージ送信元の数を設定します。 |
||||

<a name="managed-api-connector"></a>

### <a name="managed-api-connector-operations"></a>マネージド API コネクタの操作

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.Backend.ApiConnectionOperation.RequestTimeout` | `00:02:00` <br>(2 分) | マネージド API コネクタのトリガーとアクションの要求タイムアウト値を設定します。 |
| `Runtime.Backend.ApiConnectionOperation.MaxContentSize` | `104857600` バイト | マネージド API コネクタのトリガーとアクションの最大要求サイズをバイト単位で設定します。 |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryCount` | 再試行回数 `4` | マネージド API コネクタのトリガーとアクションの既定の再試行回数を設定します。 |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryInterval` | `00:00:07` <br>(7 秒) | マネージド API コネクタのトリガーとアクションの既定の再試行間隔を設定します。 |
| `Runtime.Backend.ApiWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 日) | マネージド API コネクタの Webhook トリガーとアクションの最大再試行間隔を設定します。 |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 秒) | マネージド API コネクタのトリガーとアクションの最小再試行間隔を設定します。 |
| `Runtime.Backend.ApiWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1 日) | マネージド API コネクタの Webhook トリガーおよびアクション ジョブの既定のウェイクアップ間隔を設定します。 |
||||

<a name="blob-storage"></a>

### <a name="blob-storage"></a>BLOB ストレージ

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.ContentStorage.RequestOptionsServerTimeout` | `00:00:30` <br>(30 秒) | Azure Logic Apps ランタイムからの BLOB 要求のタイムアウト値を設定します。 |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:02:00` <br>(2 分) | Azure Logic Apps ランタイムからのテーブルおよびキュー ストレージ要求について、再試行を含む操作タイムアウト値を設定します。 |
| `Runtime.ContentStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2 秒) | BLOB ストレージに送信される再試行のバックオフ間隔を設定します。 |
| `Runtime.ContentStorage.RequestOptionsMaximumAttempts` | 再試行回数 `4` | テーブルおよびキュー ストレージに送信される再試行の最大数を設定します。 |
||||

<a name="store-inline-or-blob"></a>

### <a name="store-content-inline-or-use-blobs"></a>コンテンツをインラインで格納するか、BLOB を使用する

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.FlowRunEngine.ForeachMaximumItemsForContentInlining` | `20` 項目 | `For each` ループの実行中、各項目の値は、他のメタデータと一緒にテーブル ストレージ内にインラインで、または BLOB ストレージに個別に格納されます。 他のメタデータと一緒にインラインで格納する項目の数を設定します。 |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPagesForContentInlining` | `20` ページ | BLOB ストレージに格納する前に、テーブル ストレージにインライン コンテンツとして格納するページの最大数を設定します。 |
| `Runtime.FlowTriggerSplitOnJob.MaximumItemsForContentInlining` | `40` 項目 | `SplitOn` 設定で配列項目が複数のワークフロー インスタンスに分割される場合、各項目の値は、他のメタデータと一緒にテーブル ストレージ内にインラインで、または BLOB ストレージに個別に格納されます。 インラインで格納する項目の数を設定します。 |
| `Runtime.ScaleUnit.MaximumCharactersForContentInlining` | `8192` 文字 | BLOB ストレージに格納する前に、テーブル ストレージにインラインで格納する操作の入力および出力文字数の最大数を設定します。 |
||||

<a name="table-queue-storage"></a>

### <a name="table-and-queue-storage"></a>テーブルおよびキュー ストレージ

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.DataStorage.RequestOptionsServerTimeout` | `00:00:16` <br>(16 秒) | Azure Logic Apps ランタイムからのテーブルおよびキュー ストレージ要求のタイムアウト値を設定します。 |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:00:45` <br>(45 秒) | Azure Logic Apps ランタイムからのテーブルおよびキュー ストレージ要求について、再試行を含む操作タイムアウト値を設定します。 |
| `Runtime.DataStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2 秒) | テーブルおよびキュー ストレージに送信される再試行のバックオフ間隔を設定します。 |
| `Runtime.DataStorage.RequestOptionsMaximumAttempts` | 再試行回数 `4` | テーブルおよびキュー ストレージに送信される再試行の最大数を設定します。 |
||||

<a name="retry-policy"></a>

### <a name="retry-policy-for-all-other-operations"></a>他のすべての操作の再試行ポリシー

| 設定 | 既定値 | 説明 |
|---------|---------------|-------------|
| `Runtime.ScaleMonitor.MaxPollingLatency` | `00:00:30` <br>(30 秒) | 実行時のスケーリングの最大ポーリング待機時間を設定します。 |
| `Runtime.Backend.Operation.MaximumRetryCount` | 再試行回数 `90` | ワークフロー操作の再試行ポリシー定義における最大再試行回数を設定します。 |
| `Runtime.Backend.Operation.MaximumRetryInterval` | `01:00:00:01` <br>(1 日と 1 秒) | ワークフロー操作の再試行ポリシー定義における最大間隔を設定します。 |
| `Runtime.Backend.Operation.MinimumRetryInterval` | `00:00:05` <br>(5 秒) | ワークフロー操作の再試行ポリシー定義における最小間隔を設定します。 |
||||

<a name="manage-host-settings"></a>

## <a name="manage-host-settings---hostjson"></a>ホスト設定の管理 - host.json

ホスト設定を追加、更新、または削除できます。これは、"*ローカルと Azure のどちらで実行される場合でも*" そのロジック アプリ内の "*すべてのワークフロー*" に適用される、ランタイム構成の設定と値を指定するもので、例として、スループット、容量、データ サイズなどの既定値があります。 ロジック アプリ固有のホスト設定については、「[使用可能なランタイムおよびデプロイ設定 - host.json](#reference-host-json)」をご確認ください。

### <a name="visual-studio-code---hostjson"></a>Visual Studio Code - host.json

Visual Studio Code でロジック アプリのホスト設定を確認するには、次の手順に従います。

1. ロジック アプリ プロジェクトのルート プロジェクト レベルで、**host.json** ファイルを見つけて開きます。

1. `extensions` オブジェクトの `workflows` および `settings` で、ロジック アプリに以前に追加されたホスト設定があれば確認します。 そうでなければ、`extensions` オブジェクトはファイル内に表示されません。

   ホスト設定の詳細については、「[使用可能なホスト設定のリファレンス ガイド - host.json](#reference-host-json)」をご確認ください。

ホスト設定を追加するには、次の手順に従います。

1. **host.json** ファイル内で、`extensionBundle` オブジェクトに `extensions` オブジェクトを追加します。これには、`workflow` および `settings` オブジェクトが含まれます。次に例を示します。

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. `settings` オブジェクトに、ワークフローがローカルまたは Azure のどちらで実行される場合でも、ロジック アプリ内のすべてのワークフローに使用するホスト設定を含むフラット リストを追加します。次に例を示します。

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

### <a name="azure-portal---hostjson"></a>Azure portal - host.json

Azure portal でシングルテナント ベースのロジック アプリのホスト設定を確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の検索ボックスでロジック アプリを検索し、開きます。

1. ロジック アプリのメニューの **[開発ツール]** で、 **[高度なツール]** を選択します。

1. **[高度なツール]** ページで、 **[移動]** を選択します。これにより、ロジック アプリ用の **Kudu** 環境が開きます。

1. Kudu のツール バーで、 **[Debug console]\(デバッグ コンソール\)** メニューの **[CMD]** を選択します。

1. Azure portal でロジック アプリを停止します。

   1. ロジック アプリのメニューで、 **[概要]** を選択します。

   1. **[概要]** ペインのツール バーで、 **[停止]** を選択します。

1. ロジック アプリのメニューの **[開発ツール]** で、 **[高度なツール]** を選択します。

1. **[高度なツール]** ペインで、 **[移動]** を選択します。これにより、ロジック アプリ用の Kudu 環境が開きます。

1. Kudu のツール バーで、 **[Debug console]\(デバッグ コンソール\)** メニューを開き、 **[CMD]** を選択します。

   コンソール ウィンドウが開き、コマンド プロンプトを使用して **wwwroot** フォルダーを参照できるようになります。 または、コンソール ウィンドウの上に表示されるディレクトリ構造を参照することもできます。

1. **wwwroot** フォルダーへの次のパスを参照します: `...\home\site\wwwroot`。

1. コンソール ウィンドウの上にあるディレクトリ テーブルで、**host.json** ファイルの横にある **[編集]** を選択します。

1. **host.json** ファイルが開いた後、ロジック アプリに以前に追加されたホスト設定があれば確認します。

   ホスト設定の詳細については、「[使用可能なホスト設定のリファレンス ガイド - host.json](#reference-host-json)」をご確認ください。

設定を追加するには、次の手順に従います。

1. 設定を追加または編集する前に、Azure portal でロジック アプリを停止します。

   1. ロジック アプリのメニューで、 **[概要]** を選択します。
   1. **[概要]** ペインのツール バーで、 **[停止]** を選択します。

1. **host.json** ファイルに戻ります。 `extensionBundle` オブジェクトに、`extensions` オブジェクトを追加します。これには、`workflow` および `settings` オブジェクトが含まれます。次に例を示します。

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. `settings` オブジェクトに、ワークフローがローカルまたは Azure のどちらで実行される場合でも、ロジック アプリ内のすべてのワークフローに使用するホスト設定を含むフラット リストを追加します。次に例を示します。

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

1. 完了したら、忘れずに **[保存]** を選択してください。

1. 次に、ロジック アプリを再起動します。 ロジック アプリの **[概要]** ページに戻り、 **[再起動]** を選択します。

---

## <a name="next-steps"></a>次の手順

- [シングルテナントの Azure Logic Apps 用に環境間で変わるワークフロー内の値のパラメーターを作成する](parameterize-workflow-app.md)
- [シングルテナントの Azure Logic Apps 用に DevOps のデプロイを設定する](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
