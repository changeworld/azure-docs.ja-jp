---
title: Azure Monitor プラットフォームのメトリックとログを異なる宛先に送信するための診断設定を作成する
description: 診断設定を使用して、Azure Monitor プラットフォーム メトリックとログを Azure Monitor ログ、Azure Storage、または Azure Event Hubs に送信します。
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: aa49185d8941038ab100a0a480061cf63560f439
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705457"
---
# <a name="create-diagnostic-settings-to-send-azure-monitor-platform-logs-and-metrics-to-different-destinations"></a>Azure Monitor プラットフォームのログとメトリックを異なる宛先に送信するための診断設定を作成する

この記事では、Azure プラットフォームのメトリックとログを異なる宛先に送信するための診断設定を作成し、構成する方法について詳しく説明します。

[プラットフォームのメトリック](./metrics-supported.md)は、既定では、構成なしで [Azure Monitor のメトリック](./data-platform-metrics.md)に自動的に送信されます。

Azure のアクティビティ ログやリソース ログなど、[プラットフォームのログ](./platform-logs-overview.md)から、Azure リソースとそれらが依存する Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 アクティビティ ログは単独で存在しますが、他の場所にルーティングすることができます。  リソース ログは、宛先にルーティングされるまで収集されません。

各 Azure リソースには、次の条件を定義する独自の診断設定が必要です。

- ソース - 設定に定義された宛先に送信するメトリックとログ データの種類。 使用可能な種類は、リソースの種類によって異なります。
- 宛先 - 1 つ以上の送信先。

1 つの診断設定では、各送信先を 1 つだけ定義することができます。 特定の種類の複数の送信先 (たとえば、2 つの異なる Log Analytics ワークスペース) にデータを送信する場合は、複数の設定を作成します。 各リソースには、最大 5 つの診断設定を作成できます。

次のビデオでは、診断設定を使用してプラットフォーム ログをルーティングする手順について説明します。
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

## <a name="sources"></a>変換元

ソース オプションを次に示します。

### <a name="metrics"></a>メトリック

**AllMetrics** 設定を使用して、リソースのプラットフォームのメトリックを追加の宛先にルーティングします。 このオプションは、すべてのリソース プロバイダーに存在するとは限りません。  

### <a name="logs"></a>ログ

ログについては、個別にルーティングするログ カテゴリを選択するか、カテゴリ グループを選択できます。

> [!NOTE]
> カテゴリ グループは、メトリックには適用されません。 すべてのリソースに利用可能なカテゴリ グループがあるとは限りません。

**カテゴリ グループ** を使用すると、個別のログ カテゴリを選択する代わりに、定義済みのグループに基づいてリソース ログを動的に収集できます。 Microsoft が、すべての Azure サービスで特定のユース ケースを監視するのに役立つグループを定義しています。 時間の経過と共に、新しいログが展開されたり、評価が変更されたりして、グループ内のカテゴリが更新される可能性があります。 ログ カテゴリがカテゴリ グループに追加または削除されると、診断設定を更新しなくても、ログ コレクションが自動的に変更されます。

カテゴリ グループを使用する場合、次のようになりました。 
- 個々のカテゴリの種類に基づいてリソース ログを個別に選択できなくなりました
- Azure Storage に送信されたログに保持設定を適用できなくなりました

現時点では、次の 2 つのカテゴリグループがあります。
- **すべて** - リソースによって提供されるすべてのリソース ログ。
- **監査** - データまたはサービスの設定に対する顧客の操作を記録するすべてのリソース ログ。

## <a name="destinations"></a>変換先
プラットフォーム ログとメトリックは、次の表の送信先に送信できます。 

| 到着地 | 説明 |
|:---|:---|
| [Log Analytics ワークスペース](../logs/design-logs-deployment.md) | メトリックは、ログ形式に変換されます。 このオプションは、すべてのリソースの種類で使用できるとは限りません。 Azure Monitor ログ ストア (Log Analytics から検索可能) に送信すると、既存のログ データを使用してクエリ、アラート、および視覚化に統合できます。  
| [Azure Storage アカウント](../../storage/blobs/index.yml) | Azure ストレージ アカウントにログとメトリックをアーカイブすると、監査、スタティック分析、またはバックアップに役立ちます。 Azure Monitor ログや Log Analytics ワークスペースと比較すると、Azure ストレージはコストが低く、ログを無期限に保持することができます。  | 
| [Event Hubs](../../event-hubs/index.yml) | Event Hubs にログとメトリックを送信すると、サードパーティ製の SIEM やその他の Log Analytics ソリューションなどの外部システムにデータをストリーミングできます。  |
| [Azure Monitor パートナーとの統合](../../partner-solutions/overview.md)| Azure Monitor と Microsoft 以外のその他の監視プラットフォームとの間の特別な統合。 パートナーのいずれかを既に使用している場合に便利です。  |

## <a name="requirements-and-limitations"></a>要件と制限事項

### <a name="metrics-as-a-source"></a>ソースとしてのメトリック
メトリックのエクスポートには、いくつかの制限があります。

- **診断設定を介した多次元メトリックの送信は現在サポートされていません** - ディメンションのあるメトリックは、ディメンション値全体が集計され、フラット化された一次元メトリックとしてエクスポートされます。 *例*: ブロックチェーンに関する "IOReadBytes" メトリックは、ノード レベルごとに探索してグラフ化できます。 ところが、診断設定を介してエクスポートすると、エクスポートされたメトリックには、すべてのノードに対するすべての読み取りバイト数が表示されます。
- **すべてのメトリックが診断設定でエクスポート可能であるとは限りません** - 内部の制限により、すべてのメトリックが Azure Monitor ログ/Log Analytics にエクスポートできるわけではありません。 詳細については、[サポートされるメトリックの一覧](./metrics-supported.md)のエクスポート可能な列を参照してください

特定のメトリックについてこれらの制限を回避するには、[メトリック REST API](/rest/api/monitor/metrics/list) を使用して手動で抽出し、[Azure Monitor データ コレクター API](../logs/data-collector-api.md) を使用して Azure Monitor ログにインポートします。

### <a name="activity-log-as-a-source"></a>ソースとしてのアクティビティ ログ

> [!IMPORTANT]
> アクティビティ ログの診断設定を作成する前に、まずレガシ構成を無効にする必要があります。 詳細については、「[従来の収集方法](../essentials/activity-log.md#legacy-collection-methods)」を参照してください。


### <a name="destination-limitations"></a>宛先の制限事項

診断設定の送信先は、診断設定の作成前に作成する必要があります。 設定を構成するユーザーが両方のサブスクリプションに対して適切な Azure RBAC アクセスを持っている限り、送信先はログを送信するリソースと同じサブスクリプションに属している必要はありません。 Azure Lighthouse を使用すると、別の Azure Active Directory テナントのワークスペースに診断設定を送信することもできます。 次の表では、リージョン制限など、送信先別の固有要件をまとめてあります。

| 到着地 | 要件 |
|:---|:---|
| Log Analytics ワークスペース | ワークスペースは、監視対象のリソースと同じリージョンにする必要がありません。|
<<<<<<< HEAD
| Event Hubs | 名前空間の共有アクセス ポリシーでは、ストリーミング メカニズムが保有するアクセス許可が定義されます。 Event Hubs にストリーミングするには、管理、送信、リッスンの各アクセス許可が必要です。 ストリーミングを含めるように診断設定を更新するには、その Event Hubs の承認規則に対する ListKey アクセス許可が必要です。<br><br>イベント ハブ名前空間は、リソースがリージョン別の場合、監視対象のリソースと同じリソースにする必要があります。 |
| Azure ストレージ アカウント | データへのアクセスをより適切に制御できるように、他の非監視データが格納されている既存のストレージ アカウントは使用しないでください。 アクティビティ ログとリソース ログを一緒にアーカイブする場合は、中央の場所にすべての監視データを保持するために、同じストレージ アカウントを使用できます。<br><br>データを不変ストレージに送信するには、「[BLOB ストレージの不変ポリシーを設定および管理する](../../storage/blobs/storage-blob-immutability-policies-manage.md)」の説明に従って、ストレージ アカウントの不変ポリシーを設定します。 この記事のすべての手順に従う必要があります。これには、保護された追加 BLOB の書き込みの有効化が含まれます。<br><br>ストレージ アカウントは、リソースがリージョン別の場合、監視対象のリソースと同じリージョンにする必要があります。 |
=======
| Azure ストレージ アカウント | データへのアクセスをより適切に制御できるように、他の非監視データが格納されている既存のストレージ アカウントは使用しないでください。 アクティビティ ログとリソース ログを一緒にアーカイブする場合は、中央の場所にすべての監視データを保持するために、同じストレージ アカウントを使用できます。<br><br>データを不変ストレージに送信するには、「[BLOB ストレージの不変ポリシーを設定および管理する](../../storage/blobs/immutable-policy-configure-version-scope.md)」の説明に従って、ストレージ アカウントの不変ポリシーを設定します。 保護された追加 BLOB 書き込みの有効化を含め、このリンク先の記事のすべての手順に従う必要があります。<br><br>ストレージ アカウントは、リソースがリージョン別の場合、監視対象のリソースと同じリソースにする必要があります。|
| Event Hubs | 名前空間の共有アクセス ポリシーでは、ストリーミング メカニズムが保有するアクセス許可が定義されます。 Event Hubs にストリーミングするには、管理、送信、リッスンの各アクセス許可が必要です。 ストリーミングを含めるように診断設定を更新するには、その Event Hubs の承認規則に対する ListKey アクセス許可が必要です。<br><br>イベント ハブ名前空間は、リソースがリージョン別の場合、監視対象のリソースと同じリソースにする必要があります。 <br><br> 仮想ネットワークが有効になっている場合、診断設定から Event Hubs リソースにアクセスできません。 *[信頼された Microsoft サービスを許可]* を有効にして Event Hubs のこのファイアウォール設定をバイパスすることで、Azure Monitor (診断設定) サービスから Event Hubs リソースにアクセスできるようにする必要があります。|
| パートナー統合 | パートナーによって異なります。  詳細については、[Azure Monitor パートナー統合に関するドキュメント](../../partner-solutions/overview.md)を参照してください。  
>>>>>>> repo_sync_working_branch

### <a name="azure-data-lake-storage-gen2-as-a-destination"></a>宛先としての Azure Data Lake Storage Gen2

> [!NOTE]
> Azure Data Lake Storage Gen2 アカウントは、Azure portal では有効なオプションとして表示されていますが、診断設定の送信先としては現在サポートされていません。

## <a name="create-in-azure-portal"></a>Azure Portal での作成

Azure portal では、Azure Monitor メニューから、またはリソースのメニューから診断設定を構成できます。

1. Azure portal で診断設定を構成する場所は、リソースによって異なります。

   - リソースが 1 つの場合は、リソースのメニューで、 **[監視]** の **[診断設定]** をクリックします。

        ![Azure portal のリソース メニューの [監視] セクションのスクリーンショット。[診断設定] が強調表示されています。](media/diagnostic-settings/menu-resource.png)

   - リソースが 1 つまたは複数の場合は、Azure Monitor メニューで、 **[設定]** の **[診断設定]** をクリックし、次にリソースをクリックします。

        ![[Azure Monitor] メニューの [設定] セクションのスクリーンショット。[診断設定] が強調表示されています。](media/diagnostic-settings/menu-monitor.png)

   - アクティビティ ログの場合は、 **[Azure Monitor]** メニューの **[アクティビティ ログ]** 、 **[診断設定]** の順にクリックします。 アクティビティ ログのレガシ構成が無効になっていることを確認してください。 詳細については、「[既存の設定を無効にする](./activity-log.md#legacy-collection-methods)」を参照してください。

        ![[Azure Monitor] メニューのスクリーンショット。アクティビティ ログが選択されており、[監視 - アクティビティ ログ] メニュー バーで [診断設定] が強調表示されています。](media/diagnostic-settings/menu-activity-log.png)

2. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 **[診断設定の追加]** をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/diagnostic-settings/add-setting.png)

   リソースに対する既存の設定が存在する場合は、構成済みの設定の一覧が表示されます。 **[診断設定を追加する]** をクリックして新しい設定を追加するか、または **[設定の編集]** を選択して既存の設定を編集します。 各設定には、各送信先の種類を 1 つだけ含めることができます。

   ![診断設定の追加 - 既存の設定が存在する](media/diagnostic-settings/edit-setting.png)

3. 設定にまだ名前がない場合は、名前を付けます。

      :::image type="Add diagnostic setting" source="media/diagnostic-settings/setting-new-blank.png" alt-text="新しい診断設定を追加する":::

4. **ルーティングするログとメトリック** - ログの場合は、カテゴリ グループを選択するか、後で指定した宛先に送信するデータのカテゴリごとに個々のボックスをオンにします。 カテゴリの一覧は、Azure サービスごとに異なります。 メトリックを Azure Monitor ログにも保存する場合は、 *[allMetrics]* を選択します。 

5. **[宛先の詳細]** - 各送信先のチェック ボックスをオンにします。 各チェックボックスをオンにすると、さらに情報を追加できるオプションが表示されます。

      ![Log Analytics または Event Hubs への送信](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - サブスクリプションとワークスペースを入力します。  ワークスペースがない場合は、[先に進む前に作成する](../logs/quick-create-workspace.md)必要があります。

    1. **イベント ハブ** - 次の条件を指定します。
       - イベント ハブが含まれるサブスクリプション
       - イベント ハブの名前空間 - まだ持っていない場合は、[作成する](../../event-hubs/event-hubs-create.md)必要があります
       - すべてのデータの送信先のイベント ハブの名前 (省略可能)。 名前を指定しない場合は、ログ カテゴリごとにイベント ハブが作成されます。 複数のカテゴリを送信する場合は、名前を指定して、作成するイベント ハブの数を制限することができます。 詳細については、「[Azure Event Hubs のクォータと制限](../../event-hubs/event-hubs-quotas.md)」を参照してください。
       - イベント ハブ ポリシー (省略可能) - ポリシーによってストリーミング メカニズムのアクセス許可が定義されます。 詳細については、[Event Hubs の機能](../../event-hubs/event-hubs-features.md#publisher-policy)に関するページを参照してください。

    1. **ストレージ** - サブスクリプション、ストレージ アカウント、アイテム保持ポリシーを選択します。

        ![ストレージへの送信](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > アイテム保持ポリシーは 0 に設定し、[Azure Storage のライフサイクル ポリシー](../../storage/blobs/lifecycle-management-policy-configure.md)を使用するか、またはスケジュールされたジョブを使用してストレージからデータを削除することを検討してください。 このような方針を用いることで、多くの場合、動作の一貫性が向上します。 
        >
        > まず、ストレージをアーカイブ用に使用している場合は、一般的にデータを 365 日以上保持することをお勧めします。 2 つ目の方法として、0 より大きいアイテム保持ポリシーを選択すると、保存時に有効期限日がログに付加されます。 保存後にログの日付を変更することはできません。 たとえば、*WorkflowRuntime* のアイテム保持ポリシーを 180 日に設定し、24 時間後に 365 日に設定した場合、最初の 24 時間の間に保存されたログは 180 日後に自動的に削除されますが、その種類の後続のすべてのログは、365 日後に自動的に削除されます。 後でアイテム保持ポリシーを変更しても、最初の 24 時間のログが 365 日間保持されるようにはなりません。

     1. **パートナー統合** - まず、サブスクリプションにパートナー統合をインストールする必要があります。 構成オプションはパートナーによって異なります。 詳細については、[Azure Monitor パートナーとの統合](../../partner-solutions/overview.md)に関するページを参照してください。 
    
6. **[保存]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されると、ログが指定の送信先にストリーミングされます。 イベントが生成されてから、それが [Log Analytics ワークスペースに表示される](../logs/data-ingestion-time.md)まで、15 分ほどかかる場合があります。

## <a name="create-using-powershell"></a>PowerShell を使用して作成する

[Azure PowerShell](../powershell-samples.md) を使用して診断設定を作成するには、[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) コマンドレットを使用します。 パラメーターの説明については、このコマンドレットのドキュメントを参照してください。

> [!IMPORTANT]
> この方法を Azure アクティビティ ログに使用することはできません。 代わりに、[Azure Monitor での Resource Manager テンプレートを使用した診断設定の作成](./resource-manager-diagnostic-settings.md)に関するページを参照して、Resource Manager テンプレートを作成し、それを PowerShell を使用してデプロイします。

3 つの送信先すべてを使用して診断設定を作成するための PowerShell コマンドレットの例を次に示します。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Azure CLI を使用して作成する

[Azure CLI](/cli/azure/monitor) を使用して診断設定を作成するには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) コマンドを使用します。 パラメーターの説明については、このコマンドのドキュメントを参照してください。

> [!IMPORTANT]
> この方法を Azure アクティビティ ログに使用することはできません。 代わりに、[Azure Monitor での Resource Manager テンプレートを使用した診断設定の作成](./resource-manager-diagnostic-settings.md)に関するページを参照して、Resource Manager テンプレートを作成し、それを CLI を使用してデプロイします。

3 つの送信先すべてを使用して診断設定を作成するための CLI コマンドの例を次に示します。 構文は、クライアントによって若干異なります。

# <a name="cmd"></a>[CMD](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Resource Manager テンプレートを使用して作成する
Resource Manager テンプレートを使用して診断設定を作成または更新するには、「[Azure Monitor の診断設定用の Resource Manager テンプレートのサンプル](./resource-manager-diagnostic-settings.md)」を参照してください。

## <a name="create-using-rest-api"></a>REST API を使用して作成する
[Azure Monitor REST API](/rest/api/monitor/) を使用して診断設定を作成または更新するには、「[診断設定](/rest/api/monitor/diagnosticsettings)」を参照してください。

## <a name="create-at-scale-using-azure-policy"></a>Azure Policy を使用して大規模に作成する

診断設定は Azure リソースごとに作成する必要があるため、Azure Policy を使用して、各リソースの作成時に診断設定を自動的に作成することができます。 Azure リソースの種類にはそれぞれ、診断設定に一覧表示する必要がある一意のカテゴリのセットがあります。 このため、リソースの種類ごとに個別のポリシー定義が必要です。 一部のリソースの種類には、変更せずに割り当てることができる組み込みのポリシー定義があります。 その他のリソースの種類については、カスタム定義を作成する必要があります。 

リソース ログ カテゴリ グループの追加により、ログ カテゴリの変更に応じて動的に更新されるオプションを選択できるようになりました。  詳細については、この記事で前述した[診断設定のソース](#sources)を参照してください。 

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Azure Monitor 用の組み込みポリシー定義
リソースの種類ごとに 2 つの組み込みポリシー定義があります。1 つは Log Analytics ワークスペースへの送信用で、もう 1 つはイベント ハブへの送信用です。 一方の場所のみが必要な場合は、そのポリシーをリソースの種類に割り当てます。 両方が必要な場合は、両方のポリシー定義をリソースに割り当てます。

たとえば、次の画像は、Azure Data Lake Analytics の組み込みの診断設定ポリシー定義を示しています。

![Data Lake Analytics 用の 2 つの組み込み診断設定ポリシー定義を示す [Azure Policy Definitions]\(Azure Policy 定義\) ページの部分的なスクリーンショット。](media/diagnostic-settings/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>カスタム ポリシー定義
組み込みポリシーがないリソースの種類の場合、カスタム ポリシー定義を作成する必要があります。 これは、既存の組み込みポリシーをコピーし、リソースの種類に合わせて変更することによって、Azure portal で、手動で行うことができます。 ただし、PowerShell ギャラリーにあるスクリプトを使用して、プログラムによってポリシーを作成する方が効率的です。

[Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) スクリプトを使用すると、PowerShell または Azure CLI を使用してインストールできる特定のリソースの種類用ポリシー ファイルを作成できます。 診断設定のカスタム ポリシー定義を作成するには、次の手順を使用します。

1. [Azure PowerShell](/powershell/azure/install-az-ps) がインストールされていることを確認します。
2. 次のコマンドを使用してスクリプトをインストールします。
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. ログの送信先を指定するパラメーターを使用してスクリプトを実行します。 サブスクリプションとリソースの種類を指定するように求められます。 

   たとえば、Log Analytics ワークスペースとイベント ハブにログを送信するポリシー定義を作成するには、次のコマンドを使用します。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

   または、コマンドでサブスクリプションとリソースの種類を指定することもできます。 たとえば、SQL Server データベースの Log Analytics ワークスペースとイベント ハブにログを送信するポリシー定義を作成するには、次のコマンドを使用します。

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. このスクリプトによって、ポリシー定義ごとに個別のフォルダーが作成されます。 各フォルダーには、*azurepolicy.json*、*azurepolicy.rules.json*、*azurepolicy.parameters.json* という名前の 3 つのファイルが含まれます。 Azure portal でポリシーを手動で作成する場合は、*azurepolicy.json* にはポリシー定義全体が含まれているため、その内容をコピーして貼り付けることができます。 PowerShell または Azure CLI で他の 2 つのファイルを使用して、コマンド ラインからポリシー定義を作成します。

   次の例は、PowerShell と Azure CLI の両方からポリシー定義をインストールする方法を示しています。 各例には、組み込みポリシー定義を使用して新しいポリシー定義をグループ化するために、**監視** カテゴリを指定するメタデータが含まれています。

   ```azurepowershell
   New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
   ```

   ```azurecli
   az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
   ```

### <a name="initiative"></a>イニシアティブ
一般的な戦略では、ポリシー定義ごとに割り当てを作成する代わりに、各 Azure サービスの診断設定を作成するためのポリシー定義を含むイニシアティブを作成します。 お使いの環境の管理方法に応じて、イニシアティブと管理グループ、サブスクリプション、またはリソース グループ間の割り当てを作成します。 この戦略には次の利点があります。

- リソースの種類ごとに複数の割り当てを作成する代わりに、イニシアティブに対して単一の割り当てを作成します。 同じイニシアティブを複数の監視グループ、サブスクリプション、またはリソース グループに使用します。
- 新しいリソースの種類または送信先を追加する必要が生じたら、イニシアティブを変更します。 たとえば、最初の要件では、データを Log Analytics ワークスペースのみに送信する必要があったが、後でイベント ハブを追加する必要が生じた場合などです。 新しい割り当てを作成するのではなく、イニシアティブを変更します。

イニシアティブの作成の詳細については、「[イニシアティブ定義の作成と割り当て](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition)」を参照してください。 次の推奨事項を検討してください。

- **[カテゴリ]** を **[監視]** に設定して、関連する組み込みおよびカスタムのポリシー定義を使用してグループ化します。
- イニシアティブに含まれるポリシー定義の Log Analytics ワークスペースとイベント ハブの詳細を指定する代わりに、共通のイニシアティブ パラメーターを使用します。 このパラメーターを使用すると、すべてのポリシー定義に共通の値を簡単に指定し、必要に応じてその値を変更することができます。

![イニシアティブ定義の設定を示すスクリーンショット。](media/diagnostic-settings/initiative-definition.png)

### <a name="assignment"></a>割り当て 
監視対象のリソースのスコープに応じて、イニシアティブを Azure 管理グループ、サブスクリプション、またはリソース グループに割り当てます。 [管理グループ](../../governance/management-groups/overview.md)は、特に組織に複数のサブスクリプションがある場合に、ポリシーのスコープ設定に役立ちます。

![Azure portal 内の [Diagnostic settings to Log Analytics workspace]\(Log Analytics ワークスペースに対する診断設定\) の [イニシアティブの割り当て] セクションにある [基本] タブの設定のスクリーンショット。](media/diagnostic-settings/initiative-assignment.png)

イニシアティブ パラメーターを使用すると、イニシアティブ内のすべてのポリシー定義に対して 1 回でワークスペースまたはその他の詳細を指定することができます。 

![[パラメーター] タブのイニシアティブ パラメーターを示すスクリーンショット。](media/diagnostic-settings/initiative-parameters.png)

### <a name="remediation"></a>Remediation
イニシアティブは、作成時に各仮想マシンに適用されます。 [修復タスク](../../governance/policy/how-to/remediate-resources.md)では、イニシアティブ内のポリシー定義が既存のリソースにデプロイされるため、既に作成されているすべてのリソースの診断設定を作成できます。

Azure portal を使用して割り当てを作成するときに、修復タスクを同時に作成することができます。 修復の詳細については、「[Azure Policy を使って準拠していないリソースを修復する](../../governance/policy/how-to/remediate-resources.md)」を参照してください。

![Log Analytics ワークスペースのイニシアティブの修復を示すスクリーンショット。](media/diagnostic-settings/initiative-remediation.png)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="metric-category-is-not-supported"></a>メトリック カテゴリがサポートされていない

診断設定をデプロイすると、"*メトリック カテゴリ 'xxxx' がサポートされていません*" のようなエラー メッセージが表示されます。 以前のデプロイが成功した場合でも、このエラーが表示される場合があります。 

この問題は、Resource Manager テンプレート、診断設定 REST API、Azure CLI、または Azure PowerShell を使用するときに発生します。 Azure portal を介して作成された診断設定は、サポートされているカテゴリ名のみが表示されるため、影響を受けません。

この問題は、基になる API の最近の変更が原因で発生します。 'AllMetrics' 以外のメトリック カテゴリはサポートされておらず、いくつかの特定の Azure サービスの場合を除き、サポートされていませんでした。 以前は、診断設定のデプロイ時に他のカテゴリ名は無視されていました。 これらのカテゴリは、Azure Monitor バックエンドによって 'AllMetrics' にリダイレクトされました。  2021 年 2 月の時点で、提供されたメトリック カテゴリが正確であることを明確に確認するためにバックエンドが更新されました。 この変更が原因で一部のデプロイに失敗しました。

このエラーが発生した場合は、メトリック カテゴリ名をすべて 'AllMetrics' に置き換えるようにデプロイを更新して問題を解決してください。 以前にデプロイで複数のカテゴリを追加していた場合は、'AllMetrics' 参照を持つ 1 つのみを保持する必要があります。 問題が引き続き発生する場合は、Azure portal から Azure サポートにお問い合わせください。 

### <a name="setting-disappears-due-to-non-ascii-characters-in-resourceid"></a>resourceID の ASCII 以外の文字が原因で設定が消える

診断設定では、ASCII 以外の文字 (Preproducción など) を含む resourceID はサポートされていません。 Azure でリソースの名前を変更することはできません。唯一のオプションは、ASCII 以外の文字を使用せずに新しいリソースを作成する方法です。 文字がリソース グループ内にある場合は、その下のリソースを新しいリソースに移動できます。 それ以外の場合は、リソースを再作成する必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure でのプラットフォーム ログの詳細について読む](./platform-logs-overview.md)
