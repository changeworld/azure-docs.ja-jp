---
title: Azure Monitor の Log Analytics ワークスペースのデータ エクスポート (プレビュー)
description: Log Analytics のデータ エクスポートを使用すると、選択したテーブルのデータを収集する際に Log Analytics ワークスペースから Azure ストレージ アカウントまたは Azure Event Hubs への連続エクスポートが可能になります。
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
author: yossi-y
ms.author: yossiy
ms.date: 10/17/2021
ms.openlocfilehash: 9814c90a60aaa67ff6c1914c28568fb478bd0f87
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488546"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure Monitor の Log Analytics ワークスペースのデータ エクスポート (プレビュー)
Azure Monitor で Log Analytics ワークスペースのデータ エクスポートを使用すると、Log Analytics ワークスペースで選択したテーブルのデータを収集する際に Azure ストレージ アカウントまたは Azure Event Hubs への連続エクスポートが可能になります。 この記事では、この機能の詳細と、ワークスペースでデータ エクスポートを構成する手順について説明します。

## <a name="overview"></a>概要
Log Analytics ワークスペースのデータ エクスポートを構成すると、ワークスペースで選択されたテーブルに送信された新しいデータはすべて、1 時間ごとの追加 BLOB でストレージ アカウントに自動的にエクスポートされるか、ほぼリアルタイムで対象のイベント ハブにエクスポートされます。

![データ エクスポートの概要](media/logs-data-export/data-export-overview.png)

含まれるテーブルのすべてのデータが、フィルターを使用せずにエクスポートされます。 たとえば、*SecurityEvent* テーブルに対してデータ エクスポート ルールを構成すると、その構成時点から、*SecurityEvent* テーブルに送信されたすべてのデータがエクスポートされます。


## <a name="other-export-options"></a>その他のエクスポート オプション
Log Analytics ワークスペースのデータ エクスポートでは、Log Analytics ワークスペースからデータが連続してエクスポートされます。 特定のシナリオでデータをエクスポートするその他のオプションには、次のようなものがあります。

- ロジック アプリを使用したログ クエリからのスケジュールされたエクスポート。 これはデータ エクスポート機能に似ていますが、フィルター処理または集計されたデータを Azure ストレージに送信できます。 ただし、この方法には[ログ クエリの制限](../service-limits.md#log-analytics-workspaces)が適用されます。「[ロジック アプリを使用して Log Analytics ワークスペースから Azure ストレージへデータをアーカイブする](logs-export-logic-app.md)」を参照してください。
- PowerShell スクリプトを使用したローカル コンピューターへのワンタイム エクスポート。 「[Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)」を参照してください。

## <a name="limitations"></a>制限事項

- 構成は、現在、CLI または REST 要求を使用して行うことができます。 Azure portal または PowerShell はまだサポートされていません。
- CLI と REST の `--export-all-tables` オプションはサポートされていないため、削除されます。 エクスポート ルールでテーブルの一覧を明示的に指定する必要があります。
- サポート対象のテーブルは、以下の「[サポート対象のテーブル](#supported-tables)」セクションで指定されているものに限られます。 
- 既存のカスタム ログ テーブルは、エクスポートではサポートされません。 2022 年 3 月に利用可能な新しいカスタム ログ バージョンがサポートされる予定です。
- サポートされていないテーブルがデータ エクスポート ルールに含まれている場合、操作は成功しますが、そのテーブルのデータはテーブルがサポートされるようになるまでエクスポートされません。 
- 存在しないテーブルがデータ エクスポート ルールに含まれている場合、`Table <tableName> does not exist in the workspace` エラーで失敗します。
- ワークスペースでは、最大で 10 個のルールを定義できます。 無効にすると、追加のルールが許可されます。 
- エクスポート先は、ワークスペース内のすべてのエクスポート ルールごとに一意である必要があります。
- エクスポート先は、Log Analytics ワークスペースと同じリージョンに配置されている必要があります。
- テーブル名は、ストレージ アカウントにエクスポートする場合は 60 文字以内、イベント ハブの場合は 47 文字以内で指定できます。 これよりも長い名前のテーブルはエクスポートされません。
- データ エクスポートはすべてのリージョンで使用できますが、現在は次のリージョンでサポートされています。 
    - オーストラリア中部
    - オーストラリア東部
    - オーストラリア南東部
    - ブラジル南部
    - カナダ中部
    - インド中部
    - 米国中部
    - 東アジア
    - 米国東部
    - 米国東部 2
    - フランス中部
    - ドイツ中西部
    - 東日本
    - 韓国中部
    - 米国中北部
    - 北ヨーロッパ
    - 南アフリカ北部
    - 米国中南部
    - 東南アジア
    - スイス北部
    - スイス西部
    - アラブ首長国連邦北部
    - 英国南部
    - 英国西部
    - 米国中西部
    - 西ヨーロッパ
    - 米国西部
    - 米国西部 2

## <a name="data-completeness"></a>データの完全性
データ エクスポートは、大量のデータをエクスポート先に移動するために最適化されており、特定の再試行条件では、重複するレコードの一部を含めることができます。 エクスポート先へのエクスポート操作は、受信制限に達すると失敗する可能性があります。詳細については、「[データ エクスポート ルールを作成または更新する](#create-or-update-data-export-rule)」を参照してください。 エクスポートは最大 30 分間、再試行され続けます。エクスポート先がデータを受け取れない場合、データは、エクスポート先が使用可能になるまで破棄されます。

## <a name="cost"></a>コスト
現在、データ エクスポート機能に追加料金は発生しません。 データ エクスポートの価格は、後で発表され、課金が始まる前に通知されます。 通知期間後もデータ エクスポートを引き続き使用することを選択した場合は、該当する料金が適用されます。

## <a name="export-destinations"></a>エクスポート先

ワークスペース内にエクスポート ルールを作成する前に、データのエクスポート先を作成する必要があります。 この宛先は、ワークスペースと同じサブスクリプションにある必要はありません。 Azure Lighthouse を使用している場合は、別の Azure Active Directory テナントにある宛先にデータを送信することもできます。

### <a name="storage-account"></a>ストレージ アカウント

データのエクスポート ルールを構成するには、ワークスペースと宛先の両方に対する '書き込み' アクセス許可を持っている必要があります。 データへのアクセスをより適切に制御して、ストレージのインジェスト率の制限や調整に達することを防止できるように、他の非監視データが格納されている既存のストレージ アカウントは使用しないでください。 

データを不変ストレージに送信するには、[BLOB ストレージの不変ポリシーの設定および管理](../../storage/blobs/immutable-policy-configure-version-scope.md)に関するページの説明に従って、ストレージ アカウントの不変ポリシーを設定します。 この記事のすべての手順に従う必要があります。これには、保護された追加 BLOB の書き込みの有効化が含まれます。

ストレージ アカウントは、StorageV1 以降でかつ、ワークスペースと同じリージョンにある必要があります。 データを他のリージョン内の他のストレージ アカウントにレプリケートする必要がある場合は、[Azure Storage の冗長性オプション](../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) (GRS や GZRS など) のいずれかを使用できます。

データは、Azure Monitor に到達し、1 時間ごとの追加 BLOB に格納されると、ストレージ アカウントに送信されます。 ストレージ アカウント内のテーブルごとに、*am-* の後にそのテーブル名が続く名前を持つコンテナーが作成されます。 たとえば、テーブル *SecurityEvent* は、*am-SecurityEvent* という名前のコンテナーに送信されます。

> [!NOTE]
> イングレス レートの割り当てを適切に行い、スロットリング、エラー、待機時間のイベントを減らすには、個別のストレージ アカウントを使用することが推奨されます。

2021 年 10 月 15 日より、BLOB は、*WorkspaceResourceId=/subscriptions/subscription-id/resourcegroups/\<resource-group\>/providers/microsoft.operationalinsights/workspaces/\<workspace\>/y=\<four-digit numeric year\>/m=\<two-digit numeric month\>/d=\<two-digit numeric day\>/h=\<two-digit 24-hour clock hour\>/m=\<two-digit 60-minute clock minute\>/PT05M.json* というパス構造の 5 分フォルダーに格納されます。 追加 BLOB はストレージへの書き込みが 50K に制限されているため、追加の数が多い場合はエクスポートされる BLOB の数が増える可能性があります。 このような場合の BLOB の名前付けパターンは PT05M_#.json* になります。ここで、# は増分型の BLOB カウントです。

ストレージ アカウントのデータ形式は [JSON 行](../essentials/resource-logs-blob-format.md)です。 つまり、各レコードが改行で区切られ、外部レコードの配列や JSON レコード間のコンマはありません。 

[![ストレージのサンプル データ](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

### <a name="event-hub"></a>イベント ハブ

データのエクスポート ルールを構成するには、ワークスペースと宛先の両方に対する '書き込み' アクセス許可を持っている必要があります。 イベント ハブ名前空間の共有アクセス ポリシーによって、ストリーミング メカニズムのアクセス許可が定義されます。 イベント ハブへのストリーミングには、[管理]、[送信]、[リッスン] の各アクセス許可が必要です。 エクスポート ルールを更新するには、その Event Hubs 認可ルールに対する [ListKey] アクセス許可が必要です。

イベント ハブ名前空間は、ワークスペースと同じリージョンにある必要があります。

データは、Azure Monitor に到達すると、イベント ハブに送信されます。 イベント ハブは、エクスポートするデータ型ごとに作成され、*am-* の後にテーブルの名前が続く名前が付けられます。 たとえば、テーブル *SecurityEvent* は、*am-SecurityEvent* という名前のイベント ハブに送信されます。 エクスポートされたデータを特定のイベント ハブに到達させる場合や、47 文字の制限を超える名前の付いたテーブルがある場合は、独自のイベント ハブ名を指定して、定義されたテーブルのすべてのデータをそれにエクスポートすることができます。

> [!NOTE]
> - イベント ハブの 'Basic' レベルでは、サポートされるイベント サイズの[制限](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-vs-premium-vs-dedicated-tiers)が小さいため、ワークスペース内の一部のログがそれを超え、削除される可能性があります。 エクスポート先には 'Standard'、'Premium'、または 'Dedicated' の各レベルを使用してください。
> - エクスポートされるデータの量は時間の経過とともに増加し、イングレス レートを上げるためには結果的にスケーリングが必要となります。 **自動インフレ** 機能を使用して自動的にスケールアップし、スループット ユニットの数を増やすことで、使用量のニーズを満たします。 詳細については、「[Azure Event Hubs のスループット単位を自動的にスケールアップする](../../event-hubs/event-hubs-auto-inflate.md)」を参照してください。
> - イングレス レートの割り当てを適切に行い、スロットリング、エラー、待機時間のイベントを減らすには、個別のイベント ハブ名前空間を使用します。
> - データのエクスポートでは、仮想ネットワークが有効になっているとイベント ハブ リソースに到達できません。 Event Hubs リソースへのアクセスを許可するには、イベント ハブの設定 **[信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可しますか?]** を有効にする必要があります。

> [!IMPORTANT]
> ['Basic' および 'Standard' 名前空間レベルごとにサポートされるイベント ハブの数は 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers) です。 10 を超えるテーブルをエクスポートする場合は、複数のエクスポート ルール間でテーブルを別のイベント ハブ名前空間に分割するか、エクスポート ルールでイベントハブ名を指定して、すべてのテーブルをそのイベント ハブにエクスポートします。

## <a name="enable-data-export"></a>データ エクスポートを有効にする
Log Analytics のデータ エクスポートを有効にするには、次の手順を実行する必要があります。 それぞれの詳細については、以降のセクションを参照してください。

- リソースプロバイダーを登録する。
- 信頼された Microsoft サービスを許可する。
- エクスポートするテーブルとそのエクスポート先を定義するデータ エクスポート ルールを 1 つ以上作成する。

### <a name="register-resource-provider"></a>リソース プロバイダーの登録
Log Analytics のデータ エクスポートを有効にするには、次の Azure リソース プロバイダーを対象のサブスクリプションに登録する必要があります。 

- Microsoft.Insights

このリソース プロバイダーは、おそらくほとんどの Azure Monitor ユーザー用に既に登録されています。 確認するには、Azure portal で **[サブスクリプション]** に移動します。 対象のサブスクリプションを選択し、メニューの **[設定]** セクションで **[リソース プロバイダー]** をクリックします。 **Microsoft.Insights** を見つけます。 その状態が **[登録済み]** の場合は、既に登録されています。 そうでない場合は、 **[登録]** をクリックして登録します。

また、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」で説明されているように、リソース プロバイダーを登録するために使用可能な方法のいずれかを使用できます。 PowerShell を使用したサンプル コマンドを次に示します。

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>信頼された Microsoft サービスを許可する
選択したネットワークからのアクセスを許可するように対象のストレージ アカウントが構成した場場合は、そのアカウントへの書き込みを Azure Monitor に許可するための例外を追加する必要があります。 対象のストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** から、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択します。

[![ストレージ アカウントの [ファイアウォールと仮想ネットワーク]](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>データ エクスポート ルールを作成または更新する
データ エクスポート ルールは、データをエクスポートするテーブルとその宛先を定義します。 ワークスペースには有効な規則が 10 個ありますが、"無効" な状態の規則を追加できます。 エクスポート先は、ワークスペース内のすべてのエクスポート ルールごとに一意である必要があります。

データのエクスポート先には制限があるため、エクスポートの調整、失敗、待機時間を最小限に抑えるために監視する必要があります。 [ストレージ アカウントのスケーラビリティ](../../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts)と[イベント ハブの名前空間のクォータ](../../event-hubs/event-hubs-quotas.md)に関する記事を参照してください。

#### <a name="monitoring-storage-account"></a>ストレージ アカウントの監視

1. エクスポートに個別のストレージ アカウントを使用します
1. 次のメトリックでアラートを構成します。 

    | Scope | メトリック名前空間 | メトリック | 集計 | Threshold |
    |:---|:---|:---|:---|:---|
    | storage-name | Account | イングレス | SUM | アラートの評価期間あたり最大イングレスの 80%。 たとえば、米国西部の汎用 v2 の場合、上限は 60 Gbps です。 しきい値は、5 分間の評価期間あたり 14400 Gb です |
  
1. アラートの修復アクション
    - エクスポートに個別のストレージ アカウントを使用します
    - Azure Storage Standard アカウントでは、依頼により、さらに高いイングレス制限がサポートされます。 引き上げを依頼するには、[Azure サポート](https://azure.microsoft.com/support/faq/)にお問い合わせください。
    - 追加のストレージ アカウント間でテーブルを分割します

#### <a name="monitoring-event-hub"></a>監視イベント ハブ

1. 下の[メトリック](../../event-hubs/monitor-event-hubs-reference.md)にアラートを構成します。
  
    | Scope | メトリック名前空間 | メトリック | 集計 | Threshold |
    |:---|:---|:---|:---|:---|
    | namespaces-name | Event Hub の標準メトリック | 着信バイト数 | SUM | アラートの評価期間あたり最大イングレスの 80% たとえば、制限はユニットあたり 1 MB/秒 (TU または PU) で、使用ユニット数は 5 です。 しきい値は、5 分間の評価期間あたり 1200 MB です |
    | namespaces-name | Event Hub の標準メトリック | 受信要求 | Count | アラートの評価期間あたり最大イベントの 80%。 たとえば、上限はユニットあたり 1000/秒 (TU または PU) で、使用ユニット数は 5 です。 しきい値は、5 分間の評価期間あたり 1200000 です |
    | namespaces-name | Event Hub の標準メトリック | クォータ超過エラー数 | Count | 要求の 1% の間。 たとえば、5 分あたりの要求数は 600000 です。 しきい値は、5 分間の評価期間あたり 6000 です |

1. アラートの修復アクション
   - [自動インフレ](../../event-hubs/event-hubs-auto-inflate.md)機能を構成して自動的にスケールアップし、スループット ユニットの数を増やすことで、使用量のニーズを満たします。
   - 負荷に合わせてスループットユニットの増加を確認する
   - 他の名前空間間でテーブルを分割する
   - スループットを高めるために 'Premium' または 'Dedicated' レベルを使用します

エクスポート ルールには、ワークスペースにあるテーブルを含める必要があります。 ワークスペース内で使用できるテーブルの一覧を表示するには、このクエリを実行します。

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal の **[Log Analytics ワークスペース]** メニューで、**[設定]** セクションから **[データのエクスポート]** を選択し、中央のウィンドウの上部にある **[新しいエクスポート ルール]** をクリックします。

![エクスポートの作成](media/logs-data-export/export-create-1.png)

手順に従ったら、**[作成]** をクリックします。 

<img src="media/logs-data-export/export-create-2.png" alt="export rule configuration" title="エクスポート ルールの構成" width="80%"/>


# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してストレージ アカウントに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

CLI を使用してイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 テーブルごとに個別のイベント ハブが作成されます。

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

CLI を使用して特定のイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 すべてのテーブルは、指定されたイベント ハブ名にエクスポートされます。 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventhubs/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してデータ エクスポート ルールを作成するには、次の要求を使用します。 この要求では、ベアラー トークン承認とコンテンツ タイプ application/json を使用する必要があります。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

この要求の本文では、テーブルのエクスポート先を指定します。 ストレージ アカウントを対象とした REST 要求のサンプル本文を次に示します。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

イベント ハブを対象とした REST 要求のサンプル本文を次に示します。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

イベント ハブ名が指定されているイベント ハブを対象とした REST 要求のサンプル本文を次に示します。 この場合、エクスポートされたすべてのデータがこのイベント ハブに送信されます。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[テンプレート](#tab/json)

テンプレートを使用してストレージ アカウントに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

テンプレートを使用してイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 テーブルごとに個別のイベント ハブが作成されます。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

テンプレートを使用して特定のイベント ハブに対するデータ エクスポート ルールを作成するには、次のコマンドを使用します。 すべてのテーブルは、指定されたイベント ハブ名にエクスポートされます。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>データ エクスポート ルールの構成の表示

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal の **[Log Analytics ワークスペース]** メニューで、**[設定]** セクションから **[データのエクスポート]** を選択します。

![ルール ビューのエクスポート](media/logs-data-export/export-view-1.png)

構成ビューのルールをクリックします。

<img src="media/logs-data-export/export-view-2.png" alt="export rule settings" title= "ルールの設定のエクスポート" width="65%"/>


# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してデータ エクスポート ルールの構成を表示するには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してデータ エクスポート ルールの構成を表示するには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[テンプレート](#tab/json)

該当なし

---

## <a name="disable-an-export-rule"></a>エクスポート ルールを無効にする

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

テストの実行中など、データを一定期間保持する必要がないときにエクスポートを停止できるように、エクスポート ルールを無効にすることができます。 Azure portal の **[Log Analytics ワークスペース]** メニューで、**[設定]** セクションから **[データのエクスポート]** を選択し、状態トグルをクリックしてエクスポート ルールを無効または有効にします。

![エクスポート ルールの無効化](media/logs-data-export/export-disable.png)


# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

テストの実行中など、データを一定期間保持する必要がないときにエクスポートを停止できるように、エクスポート ルールを無効にすることができます。 CLI を使用してデータ エクスポート ルールを無効にするには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

テストの実行中など、データを一定期間保持する必要がないときにエクスポートを停止できるように、エクスポート ルールを無効にすることができます。 REST API を使用してデータ エクスポート ルールを無効にするには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[テンプレート](#tab/json)

テストの実行中など、データを一定期間保持する必要がないときにエクスポートを停止できるように、エクスポート ルールを無効にすることができます。 データ エクスポートを無効にするには、テンプレートに ```"enable": false``` を設定します。

---

## <a name="delete-an-export-rule"></a>エクスポート ルールを削除する

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal の **[Log Analytics ワークスペース]** メニューで、**[設定]** セクションから *[データのエクスポート]* を選択してから、ルールの右にある省略記号をクリックし、**[削除]** をクリックします。 

![エクスポート ルールの削除](media/logs-data-export/export-delete.png)


# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してデータ エクスポート ルールを削除するには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してデータ エクスポート ルールを削除するには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[テンプレート](#tab/json)

該当なし

---


## <a name="view-all-data-export-rules-in-a-workspace"></a>ワークスペース内のすべてのデータ エクスポート ルールを表示する

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal の **[Log Analytics ワークスペース]** メニューで、**[設定]** セクションから **[データのエクスポート]** を選択して、ワークスペースのすべてのエクスポート ルールを表示します。

![ルールのエクスポート](media/logs-data-export/export-view.png)


# <a name="powershell"></a>[PowerShell](#tab/powershell)

該当なし

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI を使用してワークスペース内のすべてのデータ エクスポート ルールを表示するには、次のコマンドを使用します。

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

REST API を使用してワークスペース内のすべてのデータ エクスポート ルールを表示するには、次の要求を使用します。 この要求では、ベアラー トークン承認を使用する必要があります。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[テンプレート](#tab/json)

該当なし

---


## <a name="unsupported-tables"></a>サポート対象外のテーブル
サポート対象外のテーブルがデータ エクスポート ルールに含まれている場合、構成は成功しますが、そのテーブルのデータはエクスポートされません。 そのテーブルが後でサポートされるようになると、その時点でテーブルのデータがエクスポートされます。

存在しないテーブルがデータ エクスポート ルールに含まれている場合、"Table \<tableName\> does not exist in the workspace (テーブル \<tableName\> がワークスペースに存在しません)" エラーで失敗します。


## <a name="supported-tables"></a>サポート対象のテーブル
サポート対象のテーブルは、現在、以下に記載されているものに限定されています。 制限事項が指定されている場合を除き、テーブルのすべてのデータがエクスポートされます。 この一覧は、追加されるテーブルが増えると更新されます。

| テーブル | 制限事項 |
|:---|:---|
| AACAudit |  |
| AACHttpRequest |  |
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADRiskyUsers |  |
| AADServicePrincipalSignInLogs |  |
| AADUserRiskEvents |  |
| ABSBotRequests |  |
| ACSAuthIncomingOperations |  |
| ACSBillingUsage |  |
| ACRConnectedClientList |  |
| ACRConnectedClientList |  |
| ACSCallDiagnostics |  |
| ACSCallSummary |  |
| ACSChatIncomingOperations |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFSSignInLogs |  |
| ADFTriggerRun |  |
| ADPAudit |  |
| ADPDiagnostics |  |
| ADPRequests |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| AEWAuditLogs |  |
| AgriFoodApplicationAuditLogs |  |
| AgriFoodApplicationAuditLogs |  |
| AgriFoodFarmManagementLogs |  |
| AgriFoodFarmManagementLogs |  |
| AgriFoodFarmOperationLogs |  |
| AgriFoodInsightLogs |  |
| AgriFoodJobProcessedLogs |  |
| AgriFoodModelInferenceLogs |  |
| AgriFoodProviderAuthLogs |  |
| AgriFoodSatelliteLogs |  |
| AgriFoodWeatherLogs |  |
| アラート: |  |
| AlertEvidence |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| ATCExpressRouteCircuitIpfix |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AWSGuardDuty |  |
| AWSVPCFlow |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CDBCassandraRequests |  |
| CDBControlPlaneRequests |  |
| CDBDataPlaneRequests |  |
| CDBGremlinRequests |  |
| CDBMongoRequests |  |
| CDBPartitionKeyRUConsumption |  |
| CDBPartitionKeyStatistics |  |
| CDBQueryRuntimeStatistics |  |
| CloudAppEvents |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | 部分的なサポート – データの一部は、エクスポートでサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerLogV2 |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DeviceNetworkInfo |  |
| DnsEvents |  |
| DnsInventory |  |
| DummyHydrationFact |  |
| Dynamics365Activity |  |
| EmailAttachmentInfo |  |
| EmailEvents |  |
| EmailPostDeliveryEvents |  |
| EmailUrlInfo |  |
| イベント | 部分的なサポート - Log Analytics エージェント (MMA) または Azure Monitor エージェント (AMA) から到着するデータは、エクスポートで完全にサポートされます。 Diagnostics Extension エージェントを介して到着するデータは、ストレージを使用して収集される一方で、このパスはエクスポートではサポートされていません。2 |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| HDInsightAmbariClusterAlerts |  |
| HDInsightAmbariSystemMetrics |  |
| HDInsightHadoopAndYarnLogs |  |
| HDInsightHadoopAndYarnMetrics |  |
| HDInsightHBaseLogs |  |
| HDInsightHBaseMetrics |  |
| HDInsightHiveAndLLAPLogs |  |
| HDInsightHiveAndLLAPMetrics |  |
| HDInsightHiveTezAppStats |  |
| HDInsightJupyterNotebookEvents |  |
| HDInsightKafkaMetrics |  |
| HDInsightOozieLogs |  |
| HDInsightRangerAuditLogs |  |
| HDInsightSecurityLogs |  |
| HDInsightSparkApplicationEvents |  |
| HDInsightSparkBlockManagerEvents |  |
| HDInsightSparkEnvironmentEvents |  |
| HDInsightSparkExecutorEvents |  |
| HDInsightSparkJobEvents |  |
| HDInsightSparkLogs |  |
| HDInsightSparkSQLExecutionEvents |  |
| HDInsightSparkStageEvents |  |
| HDInsightSparkStageTaskAccumulables |  |
| HDInsightSparkTaskEvents |  |
| Heartbeat |  |
| HuntingBookmark |  |
| InsightsMetrics | 部分的なサポート – データの一部は、エクスポートでサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MCCEventLogs |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftDataShareShareLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | 政府機関向けクラウドでの部分的なサポート – データの一部は、Webhook を介して O365 から LA に取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| Operation | 部分的なサポート – データの一部は、エクスポートでサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| Perf | 部分的なサポート – 現在、Windows のパフォーマンス データのみがサポートされています。 現在、Linux のパフォーマンス データはエクスポートに含まれません。 |
| PowerBIDatasetsWorkspace |  |
| HDInsightRangerAuditLogs |  |
| PurviewScanStatusLogs |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | 部分的なサポート - Log Analytics エージェント (MMA) または Azure Monitor エージェント (AMA) から到着するデータは、エクスポートで完全にサポートされます。 Diagnostics Extension エージェントを介して到着するデータは、ストレージを使用して収集される一方で、このパスはエクスポートではサポートされていません。 |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SentinelHealth |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SQLSecurityAuditEvents |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| syslog | 部分的なサポート - Log Analytics エージェント (MMA) または Azure Monitor エージェント (AMA) から到着するデータは、エクスポートで完全にサポートされます。 Diagnostics Extension エージェントを介して到着するデータは、ストレージを使用して収集される一方で、このパスはエクスポートではサポートされていません。 |
| ThreatIntelligenceIndicator |  |
| 更新 | 部分的なサポート – データの一部は、エクスポートでサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| UpdateRunProgress |  |
| UpdateSummary |  |
| 使用方法 |  |
| UserAccessAnalytics |  |
| UserPeerAnalytics |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | 部分的なサポート – データの一部は、エクスポートでサポートされていない内部サービスを介して取り込まれます。 現在、この部分はエクスポートに含まれません。 |
| WorkloadDiagnosticLogs |  |
| WVDAgentHealthStatus |  |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |

## <a name="next-steps"></a>次のステップ

- [Azure Data Explorer からエクスポートされたデータのクエリを実行する](../logs/azure-data-explorer-query-storage.md)。
