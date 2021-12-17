---
title: Azure Load Balancer の監視
description: ロード バランサーを監視する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 62885e889c64c40c051f838178f9a96d4ac4a8ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291182"
---
# <a name="monitoring-load-balancer"></a>ロード バランサーの監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 

この記事では、Load Balancer によって生成される監視データについて説明します。 Load Balancer では [Azure Monitor](../azure-monitor/overview.md) が使用されます。 Azure Monitor を使用するすべての Azure サービスにとって共通する機能に詳しくない場合は、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。

## <a name="load-balancer-insights"></a>ロード バランサーの分析情報

Azure の一部のサービスには、サービスを監視する開始点を提供する Azure portal の特別な事前構築済みの監視ダッシュボードがあります。 これらの特別なダッシュボードは、"分析情報" と呼ばれます。

Load Balancer の分析情報には次のものがあります。

* 機能依存関係ビュー
* メトリック ダッシュボード
* [概要] タブ
* フロントエンドとバックエンドの可用性に関するタブ
* データ スループットに関するタブ
* フローの分布
* 接続モニター
* メトリック定義

Load Balancer の分析情報の詳細については、「[Azure Load Balancer を監視および構成するための分析情報の使用](./load-balancer-insights.md)」を参照してください

## <a name="monitoring-data"></a>データの監視 

Load Balancer では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページを参照してください。 

Load Balancer によって作成されるメトリックとログ メトリックの詳細については、「[Load Balancer データの監視のリファレンス](monitor-load-balancer.md)」を参照してください。

Load Balancer では、以下を通じて追加の監視データが用意されています。

* [正常性プローブ](./load-balancer-custom-probe-overview.md)
* [リソースの正常性状態](./load-balancer-standard-diagnostics.md#resource-health-status)
* [REST API](./load-balancer-query-metrics-rest-api.md)

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

## <a name="creating-a-diagnostic-setting"></a>診断設定の作成

診断設定は、Azure portal、PowerShell、または Azure CLI を使用して作成できます。


一般的なガイダンスについては、[Azure でプラットフォーム ログとメトリックを収集するための診断設定の作成](../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。

診断設定を作成するときは、収集するログのカテゴリを指定します。 Load Balancer のカテゴリは **AllMetrics** です

### <a name="portal"></a>ポータル

1. [Azure ポータル](https://portal.azure.com)

2. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。

3. 検索結果で **[ロード バランサー]** を選択します。

4. お使いのロード バランサーを選択します。 この例では **myLoadBalancer** を使用します。

5. **myLoadBalancer** の **[監視]** セクションで、 **[診断設定]** を選択します。

6. **[診断設定]** で、 **[+ 診断設定を追加する]** を選択します。

7. **[診断設定]** に次の情報を入力するか、選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 診断設定の名前 | 診断設定の名前を入力します。 |
    | **カテゴリの詳細** |   |
    | メトリック | **[AllMetrics]** を選択します。 |

8. **[宛先の詳細]** を選択します。 宛先オプションのいくつかは次のとおりです。
    * **Log Analytics への送信**
        * **サブスクリプション** と **Log Analytics ワークスペース** を選択します。
    * **ストレージ アカウントへのアーカイブ**
        * **サブスクリプション** と **ストレージ アカウント** を選択します。
    * **イベント ハブへのストリーム**
        * **[サブスクリプション]** 、 **[イベント ハブの名前空間]** 、 **[イベント ハブ名 (オプション)]** 、 **[イベント ハブ ポリシー名]** を選択します

9. **[保存]** を選択します。

### <a name="powershell"></a>PowerShell

Azure PowerShell にサインインします。

```azurepowershell
Connect-AzAccount 
```

#### <a name="log-analytics-workspace"></a>Log Analytics ワークスペース

Log Analytics ワークスペースの診断ログを有効にするには、これらのコマンドを入力します。 角かっこで囲まれた値は、実際の値に置き換えます。

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the workspace in a variable. ##
$wspara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-log-analytics-workspace-name>
}
$ws = Get-AzOperationalInsightsWorkspace @wspara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -Enabled $true `
    -MetricCategory 'AllMetrics' `
    -WorkspaceId $ws.ResourceId
```

#### <a name="storage-account"></a>ストレージ アカウント

ストレージ アカウントの診断ログを有効にするには、これらのコマンドを入力します。 角かっこで囲まれた値は、実際の値に置き換えます。

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the storage account in a variable. ##
$storpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-storage-account-name>
}
$storage = Get-AzStorageAccount @storpara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -StorageAccountId $storage.id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```

#### <a name="event-hub"></a>イベント ハブ

イベント ハブの名前空間の診断ログを有効にするには、これらのコマンドを入力します。 角かっこで囲まれた値は、実際の値に置き換えます。

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the event hub in a variable. ##
$hubpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-event-hub-name>
}
$eventhub = Get-AzEventHubNamespace @hubpara

## Place the event hub authorization rule in a variable. ##    
$hubrule = @{
    ResourceGroupName = 'myResourceGroup'
    Namespace = 'myeventhub8675'
}
$eventhubrule = Get-AzEventHubAuthorizationRule @hubrule

## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.Id `
    -Name 'myDiagSetting-event'`
    -EventHubName $eventhub.Name `
    -EventHubAuthorizationRuleId $eventhubrule.Id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```
### <a name="azure-cli"></a>Azure CLI

Azure CLI にサインインします。

```azurecli
az login
```

#### <a name="log-analytics-workspace"></a>Log Analytics ワークスペース

Log Analytics ワークスペースの診断ログを有効にするには、これらのコマンドを入力します。 角かっこで囲まれた値は、実際の値に置き換えます。

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

wsid=$(az monitor log-analytics workspace show \
    --resource-group <your-resource-group> \
    --workspace-name <your-log-analytics-workspace-name> \
    --query id \
    --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --workspace $wsid
```

#### <a name="storage-account"></a>ストレージ アカウント

ストレージ アカウントの診断ログを有効にするには、これらのコマンドを入力します。 角かっこで囲まれた値は、実際の値に置き換えます。

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

storid=$(az storage account show \
        --name <your-storage-account-name> \
        --resource-group <your-resource-group> \
        --query id \
        --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --storage-account $storid
```

#### <a name="event-hub"></a>イベント ハブ

イベント ハブの名前空間の診断ログを有効にするには、これらのコマンドを入力します。 角かっこで囲まれた値は、実際の値に置き換えます。

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

az monitor diagnostic-settings create \
    --name myDiagSetting-event \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --event-hub-rule /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<your-event-hub-namespace>/authorizationrules/RootManageSharedAccessKey
```

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、Load Balancer のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 

Load Balancer のために収集されるプラットフォーム メトリックの一覧については、[Load Balancer データの監視のリファレンスのメトリック](monitor-load-balancer-reference.md#metrics)に関する記事を参照してください  

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

## <a name="analyzing-logs"></a>ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。  

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、サブスクリプションレベルのイベントの分析情報が得られるプラットフォーム ログの一種です。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  

Azure Monitor ログによって使用され、Log Analytics によってクエリ可能なテーブルの一覧については、「[Load Balancer データの監視のリファレンス](monitor-load-balancer-reference.md#azure-monitor-logs-tables)」を参照してください  

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!NOTE]
> 現在、Kusto クエリには、ロード バランサーのログからデータを取得できないという問題があります。


## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。

Load Balancer 上で動作するアプリケーションを作成または実行している場合、[Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) によって、アラートの種類が追加で用意される場合があります。


次の表に、Load Balancer の一般的および推奨される警告ルールを示します。

| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
| VM が使用できないため、負荷分散規則を使用できない | フロントエンド IP アドレスとフロントエンド ポート (既知および将来のすべての値) で分割されたデータ パスの可用性が 0 で、正常性プローブの状態が 0 の場合は、アラートを発生します | このアラートは、関連付けられているバックエンド プール内のすべての VM が構成された正常性プローブによってプローブ対象外にされているため、構成された負荷分散規則のデータ パスの可用性がトラフィックを処理できていないかどうかを判断します。 ロード バランサーの[トラブルシューティング ガイド](load-balancer-troubleshoot.md)を確認して、潜在的な根本原因を調査します。 |
| VM の可用性が大幅に低い | バックエンド IP とバックエンド ポートで分割された正常性プローブの状態が、プール サイズ全体のユーザー定義のプローブ対象率 (つまり、25% がプローブ対象) と等しい場合は、アラートを発生します | このアラートは、トラフィックを処理するために利用可能な VM が必要数に達していないかどうかを判断します |
| インターネット エンドポイントへの送信接続が失敗する | "接続状態" = "失敗" でフィルター処理された SNAT 接続数が 0 より大きい場合は、アラートを発生します | このアラートは、SNAT ポートが使い果たされ、VM が送信接続を開始できなかった場合に発生します。 |
| SNAT の枯渇に近づいている | 使用された SNAT ポートがユーザー定義の数を超えている場合、アラートを発生します | このアラートには、常に同じ数のポートが割り当てられる静的な送信の構成が必要です。 その後、割り当てられたポートのうち一定の割合が使用された場合に発生します。 |

## <a name="next-steps"></a>次のステップ

- Load Balancer によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、「[Load Balancer データの監視のリファレンス](monitor-load-balancer-reference.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。