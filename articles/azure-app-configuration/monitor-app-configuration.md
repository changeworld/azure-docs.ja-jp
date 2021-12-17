---
title: Azure App Configuration の監視
description: App Configuration を監視する方法について説明します
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: 7edddaac52c569bf29c1be7e173ce966adb0c17b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969752"
---
# <a name="monitoring-app-configuration"></a>App Configuration を監視する
Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 

この記事では、App Configuration によって生成される監視データについて説明します。 App Configuration では [Azure Monitor](../azure-monitor/overview.md) が使用されます。 Azure Monitor を使用するすべての Azure サービスにとって共通する機能に詳しくない場合は、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure portal の [監視の概要] ページ
Azure portal の **[概要]** ページには、リソース使用状況の概要 (要求の合計数、調整された要求の数、構成ストアごとの要求時間など) が表示されます。 この情報は便利ですが、表示される監視データはごくわずかです。 この監視データの一部は自動的に収集され、リソースを作成するとすぐに分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

> [!div class="mx-imgBorder"]
> ![[概要] ページでの監視](./media/monitoring-overview-page.png)

## <a name="monitoring-data"></a>データの監視 
App Configuration では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 App Configuration によって作成されるメトリックとログ メトリックの詳細については、「[App Configuration データ監視のリファレンス](./monitor-app-configuration-reference.md)」を参照してください。

## <a name="collection-and-routing"></a>収集とルーティング
プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。 たとえば、Azure Monitor で構成ストアのログとメトリックをほぼリアルタイムで表示するには、Log Analytics ワークスペースでリソース ログを収集します。 まだない場合は、[Log Analytics ワークスペース](../azure-monitor/logs/quick-create-workspace.md)を作成し、次の手順に従って診断設定を作成して有効にします。 

 #### <a name="portal"></a>[ポータル](#tab/portal)

1. Azure portal にサインインします。

1. App Configuration ストアに移動します。

1. **[監視]** セクションで **[診断設定]** を選択し、 **[+ 診断設定の追加]** を選択します。 
    > [!div class="mx-imgBorder"]
    > ![診断設定を追加します](./media/diagnostic-settings-add.png)

1. **[診断設定]** ページで、設定の名前を入力し、 **[HttpRequest]** を選択して、ログの送信先を選択します。 これらを Log Analytics ワークスペースに送信するには、 **[Log Analytics ワークスペースに送信する]** を選択します。
 
    > [!div class="mx-imgBorder"]
    > ![診断設定の詳細](./media/monitoring-diagnostic-settings-details.png)

1. **サブスクリプション** の名前と **Log Analytics ワークスペース** を入力します。 
1. **[保存]** を選択して、[診断設定] ページに新しい診断設定が表示されることを確認します。 
    

 ### <a name="azure-cli"></a>[Azure CLI](#tab/cli)
    
1. Azure Cloud Shell を開きます。または、Azure CLI をローカルにインストールした場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

1. 自分の ID が複数のサブスクリプションに関連付けられている場合は、ログを有効にするストレージ アカウントのサブスクリプションをアクティブなサブスクリプションに設定します。

    ```Azure CLI
    az account set --subscription <your-subscription-id>
    ```

1. az monitor [diagnostic-settings create コマンド](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create&preserve-view=true)を使用してログを有効にします。

    ```Azure CLI
    az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <app-configuration-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
    ```

 ### <a name="powershell"></a>[PowerShell](#tab/PowerShell)
    
1. Windows PowerShell コマンド ウィンドウを開き、Connect-AzAccount コマンドを使用して Azure サブスクリプションにサインインします。 その後、画面の指示に従います。

    ```PowerShell
    Connect-AzAccount
    ```

1. アクティブなサブスクリプションを、ログを有効にする App Configuration アカウントのサブスクリプションに設定します。

    ```PowerShell
    Set-AzContext -SubscriptionId <subscription-id>
    ```
    
1. Log Analytics ワークスペースのログを有効にするには、[Set-AzDiagnosticSetting PowerShell](/previous-versions/azure/mt631625(v=azure.100)?redirectedfrom=MSDN) コマンドレットを使用します。 

    ```PowerShell
    Set-AzDiagnosticSetting -ResourceId <app-configuration-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true
    ```
1. 診断設定が正しく設定されていてログ カテゴリが有効になっていることを確認します。 

    ```PowerShell
    Get-AzureRmDiagnosticSetting -ResourceId <app-configuration-resource-id> 
    ```
---
Azure portal、CLI、または PowerShell を使用した診断設定の作成の詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。 

診断設定を作成するときは、収集するログのカテゴリを指定します。 App Configuration のログのカテゴリの詳細については、[App Configuration の監視データのリファレンス](./monitor-app-configuration-reference.md#resourcelogs)に関する記事を参照してください。

## <a name="analyzing-metrics"></a>メトリックの分析

**Azure Monitor** のメニューから **[メトリック]** を開き、メトリックス エクスプローラーを使用して、App Configuration のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 App Configuration では、次のメトリックが収集されます。 

* Http 着信要求数 
* Http 着信要求の時間 
* 調整された Http 要求数 (Http 状態コード 429 の応答)

ポータルで、 **[メトリック]** セクションに移動し、分析する **[メトリック名前空間]** と **[メトリック]** を選択します。 次のスクリーンショットは、構成ストアの **[Http 着信要求数]** を選択したときのメトリック ビューを示しています。

> [!div class="mx-imgBorder"]
> ![アプリ構成メトリックの使用方法](./media/monitoring-analyze-metrics.png)

App Configuration で収集されるプラットフォーム メトリックの一覧については、[App Configuration データ参照メトリックの監視](./monitor-app-configuration-reference.md#metrics)に関する記事を参照してください。 参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧も確認できます。

## <a name="analyzing-logs"></a>ログの分析
Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)に関する記事をご覧ください。 

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、Azure のプラットフォーム ログであり、サブスクリプション レベルのイベントの分析情報が提供されます。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  
App Configuration で収集されるリソース ログの種類の一覧については、「[App Configuration データ監視のリファレンス](./monitor-app-configuration-reference.md#resourcelogs)」を参照してください。 Azure Monitor ログによって使用され、Log Analytics によってクエリ可能なテーブルの一覧については、「[App Configuration データ監視のリファレンス](./monitor-app-configuration-reference.md#azuremonitorlogstables)」を参照してください  

>[!IMPORTANT]
> App Configuration メニューで **[ログ]** を選択すると、Log Analytics が開き、クエリ スコープが現在のアプリ構成リソースに設定されています。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 


他の構成のデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

ポータルで、 **[ログ]** セクションに移動し、クエリ エディターに移動します。 左側の **[テーブル]** タブの下で **AACHttpRequest** を選択して、構成ストアのログを表示します。 Kusto クエリをエディターに入力すると、結果が下に表示されます。  

> [!div class="mx-imgBorder"]
> ![kusto クエリをログに記入する](./media/monitoring-writing-queries.png)

次に、App Configuration リソースの監視に使用できるサンプル クエリを示します。 



* 過去 3 日間のすべての HTTP 要求が一覧表示されます 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
    ```

* 過去 3 日間のすべての調整された要求 (要求が多すぎるために Http 状態コード 429 が返された) が一覧表示されます 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | where StatusCode == "429"
    ```

* 過去 3 日間に送信された要求の数が IP アドレス別に一覧表示されます 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount= count() by ClientIPAddress
        | order by requestCount desc 
    ```

* 過去 3 日間に受信した状態コードの種類を示す円グラフを作成します
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount=count() by StatusCode
        | order by requestCount desc 
        | render piechart 
    ```

* 過去 14 日間に送信された要求の日別の数が一覧表示されます
    ```Kusto
    AACHttpRequest
        | where TimeGenerated > ago(14d)
        | extend Day = startofday(TimeGenerated)
        | summarize requestcount=count() by Day
        | order by Day desc  
    ```

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。
次の表に、App Configuration の一般的および推奨される警告ルールを示します。

| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
|Http 要求のレート制限 | 状態コード = 429  | 構成ストアは[時間毎要求クォータ](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration)を超えました。 標準ストアにアップグレードするか、[ベストプラクティス](./howto-best-practices.md#reduce-requests-made-to-app-configuration)に従って使用状況を最適化してください。 |



## <a name="next-steps"></a>次のステップ

* App Configuration によって作成されるメトリック、ログ、その他の重要な値のリファレンスについては、「[App Configuration データ監視のリファレンス](./monitor-app-configuration-reference.md)」を参照してください。

* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。