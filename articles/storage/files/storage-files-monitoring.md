---
title: Azure Files の監視 | Microsoft Docs
description: Azure Files のパフォーマンスと可用性を監視する方法について説明します。 Azure Files のデータを監視します。構成、およびメトリックとログ データの分析について説明します。
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 4b2f819edd875130c57d487536691b4588dcc71f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772670"
---
# <a name="monitoring-azure-files"></a>Azure Files の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Files によって生成される監視データと、Azure Monitor の機能を使用してこのデータに関するアラートを分析する方法について説明します。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 プレビューに登録するには、[こちらのページ](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)を参照してください。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、およびテーブルに対してログが有効になります。 この機能は、Azure Resource Manager デプロイ モデルを使用して作成されたすべてのストレージ アカウントで使用できます。 「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

## <a name="monitor-overview"></a>監視の概要

Azure portal で、各 Azure Files リソースの **[概要]** ページには、リソースの要求や時間ごとの課金など、リソースの使用状況の要約が表示されます。 この情報は役立ちますが、使用できる監視データはごくわずかです。 このデータの一部は自動的に収集され、リソースを作成するとすぐに、分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは
Azure Files は、Azure のフルスタック監視サービスである [Azure Monitor](../../azure-monitor/overview.md) を使用して監視データを作成します。 Azure Monitor には、Azure リソースと、他のクラウドおよびオンプレミスのリソースを監視するための完全な機能セットが用意されています。 

まず「[Azure Monitor を使用した Azure リソースの監視](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)」の記事にある次の事項の説明をお読みください。

- Azure Monitor とは
- 監視に関連するコスト
- Azure で収集される監視データ
- データ収集の構成
- 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事に基づき、Azure Files から収集された特定のデータについて説明します。 例では、Azure ツールを使用してデータ コレクションを構成し、このデータを分析する方法について説明します。

## <a name="monitoring-data"></a>データの監視

Azure Files では、他の Azure リソースと同じ種類の監視データが収集されます。これについては、[Azure リソースの監視データ](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)に関するページをご覧ください。 

Azure Files によって作成されるメトリックとログの詳細については、「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

Azure Monitor のメトリックとログでは、Azure Resource Manager ストレージ アカウントのみがサポートされています。 Azure Monitor では、従来のストレージ アカウントはサポートされていません。 従来のストレージ アカウントでメトリックまたはログを使用する場合、Azure Resource Manager ストレージ アカウントに移行する必要があります。 [Azure Resource Manager への移行](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)に関するページを参照してください。

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集されますが、診断設定を使用して他の場所にルーティングすることもできます。 リソース ログを収集するには、診断設定を作成する必要があります。 

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 プレビューに登録するには、[こちらのページ](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)を参照してください。 このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

Azure portal、Azure CLI、または PowerShell を使用して診断設定を作成する方法については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](../../azure-monitor/platform/diagnostic-settings.md)」をご覧ください。 

診断設定を作成する Azure Resource Manager テンプレートについては、「[Azure Storage の診断設定](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage)」をご覧ください。

診断設定を作成するときに、BLOB、キュー、テーブル、ファイルなど、ログを有効にするストレージの種類を選択します。 Azure Files の場合は、 **[File]\(ファイル\)** を選択します。 

Azure portal で診断設定を作成する場合は、一覧からリソースを選択できます。 PowerShell または Azure CLI を使用する場合は、Azure Files エンドポイントのリソース ID を使用する必要があります。 ストレージ アカウントの **[プロパティ]** ページを開くと、Azure portal 上でリソース ID を確認できます。

また、ログ収集の対象とする操作のカテゴリを次のいずれかから指定する必要もあります。 

| カテゴリ | 説明 |
|:---|:---|
| StorageRead | オブジェクトに対する読み取り操作。 |
| StorageWrite | オブジェクトに対する書き込み操作。 |
| StorageDelete | オブジェクトに対する削除操作。 |

ログに記録された SMB と REST の各操作の一覧を取得するには、[Storage によって記録される操作やステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

## <a name="analyzing-metrics"></a>メトリックの分析

メトリックス エクスプローラーを使用して、他の Azure サービスのメトリックと共に Azure Storage のメトリックを分析できます。 メトリックス エクスプローラーを開くには、 **[Azure Monitor]** メニューの **[メトリック]** を選択します。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/platform/metrics-getting-started.md)」をご覧ください。 

ディメンションをサポートするメトリックについては、目的のディメンション値でメトリックをフィルター処理できます。  Azure Storage でサポートされるディメンションの完全な一覧については、「[メトリックのディメンション](storage-files-monitoring-reference.md#metrics-dimensions)」をご覧ください。 Azure Files のメトリックは、こちらの名前空間にあります。 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

(Azure Files を含む) すべての Azure Monitor サポート メトリックの一覧については、「[Azure Monitor のサポートされるメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)」をご覧ください。

### <a name="accessing-metrics"></a>メトリックにアクセスする

> [!TIP]
> Azure CLI または .NET の例を表示するには、ここに示した対応するタブを選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>メトリック定義の一覧表示

ストレージ アカウントまたは Azure Files サービスのメトリック定義を一覧表示できます。 [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) コマンドレットを使用します。

この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体のリソース ID または Azure Files サービスのリソース ID に置き換えます。  これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>メトリック値を読み取る

ストレージ アカウントまたは Azure Files サービスのアカウント レベルのメトリック値を読み取ることができます。 [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) コマンドレットを使用します。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>アカウント レベルのメトリック定義を一覧表示する

ストレージ アカウントまたは Azure Files サービスのメトリック定義を一覧表示できます。 [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) コマンドを使用します。
 
この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体のリソース ID または Azure Files サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>アカウント レベルのメトリック値を読み取る

ストレージ アカウントまたは Azure Files サービスのメトリック値を読み取ることができます。 [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) コマンドを使用します。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor には、メトリックの定義と値を読み取るための [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) が用意されています。 [サンプル コード](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)では、さまざまなパラメーターで SDK を使用する方法を示します。 ストレージ メトリックスについては `0.18.0-preview` 以降のバージョンを使用する必要があります。
 
この例では、`<resource-ID>` プレースホルダーをストレージ アカウント全体または Azure Files サービスのリソース ID に置き換えます。 これらのリソース ID は、Azure portal 上のストレージ アカウントの **[プロパティ]** ページで確認できます。

`<subscription-ID>` 変数をご自身のサブスクリプションの ID に置き換えます。 `<tenant-ID>`、`<application-ID>`、および `<AccessKey>` の値を取得する方法のガイダンスについては、「[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)」をご覧ください。 

#### <a name="list-the-account-level-metric-definition"></a>アカウント レベルのメトリック定義を一覧表示する

次の例は、アカウント レベルでメトリック定義を一覧表示する方法を示しています。

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>アカウント レベルのメトリック値を読み取る

次の例は、アカウント レベルで `UsedCapacity` データを読み取る方法を示しています。

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>多次元メトリック値を読み取る

多次元メトリックの場合、特定のディメンション値に対するメトリック データを読み取る必要がある場合は、メタデータ フィルターを定義する必要があります。

次の例では、多次元をサポートするメトリックについてのメトリック データを読み取る方法を示します。

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-logs"></a>ログの分析

リソース ログには、ストレージ アカウント内の BLOB として、イベント データとして、またはログ分析クエリを使用してアクセスできます。

ログに記録された SMB と REST の各操作の一覧を取得するには、[Storage によって記録される操作やステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 プレビューに登録するには、[こちらのページ](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)を参照してください。 このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントのファイル エンドポイントにはアクティビティが存在するが、テーブル エンドポイントまたはキュー エンドポイントには存在しない場合、ファイル サービスに関連したログだけが作成されます。 Azure Storage ログには、ストレージ サービスに対する要求の成功と失敗についての詳細な情報が含まれています。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。

### <a name="log-authenticated-requests"></a>認証済み要求をログに記録する

 次のタイプの認証済み要求が記録されます。

- 成功した要求
- 失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー)
- Shared Access Signature (SAS) または OAuth を使用した要求 (失敗した要求と成功した要求を含む)
- 分析データ ( **$logs** コンテナーの従来のログ データと、 **$metric** テーブルのクラス メトリック データ) に対する要求

Azure Files サービスそのものによる要求 (ログの作成や削除など) は記録されません。 ログに記録された SMB と REST の各要求の完全な一覧については、[Storage によって記録される操作やステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

### <a name="log-anonymous-requests"></a>匿名要求をログに記録する

 次のタイプの匿名要求が記録されます。

- 成功した要求
- サーバー エラー
- クライアントとサーバーの両方のタイムアウト エラー
- エラー コード 304 (変更されていません) で失敗した GET 要求

その他の失敗した匿名要求は一切記録されません。 ログに記録された SMB と REST の各要求の完全な一覧については、[Storage によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)に関するページと「[Azure Files 監視データのリファレンス](storage-files-monitoring-reference.md)」を参照してください。

### <a name="accessing-logs-in-a-storage-account"></a>ストレージ アカウントのログにアクセスする

ログは、ターゲット ストレージ アカウントのコンテナーに格納された BLOB として表示されます。 データは、行区切りの JSON ペイロードとして収集され、1 つの BLOB 内に格納されます。 BLOB のこの名前は次の名前付け規則に従っています。

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

次に例を示します。

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>イベント ハブのログにアクセスする

イベント ハブに送信されたログはファイルとして保存されませんが、イベント ハブでログ情報が受信されたことを確認することはできます。 Azure portal でイベント ハブに移動し、**受信メッセージ**の数がゼロより大きいことを確認します。 

![監査ログ](media/storage-files-monitoring/event-hub-log.png)

イベント ハブに送信されるログ データのアクセスと読み取りには、セキュリティ情報とイベント管理のツールと監視ツールを使用します。 詳細については、[イベント ハブに送信された監視データを処理する方法](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)に関するページを参照してください。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Log Analytics ワークスペースのログにアクセスする

Azure Monitor ログ クエリを使用して、Log Analytics ワークスペースに送信されたログにアクセスできます。 データは**StorageFileLogs** テーブルに格納されます。 

詳細については、「[Azure Monitor で Log Analytics の使用を開始する](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)」を参照してください。

#### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

**[ログ検索]** バーに入力して Azure Files の監視に利用できるクエリを紹介します。 これらのクエリは[新しい言語](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)で使用できます。

> [!IMPORTANT]
> ストレージ アカウント リソース グループ メニューから **[ログ]** を選択すると、クエリのスコープが現在のリソース グループに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソース グループのデータのみが含まれます。 他のリソースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、**Azure Monitor** のメニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope/)」を参照してください。

これらのクエリを使用すると、Azure ファイル共有の監視に役立ちます。

- 先週の SMB エラーを表示する

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- 先週の SMB 操作の円グラフを作成する

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- 先週の REST エラーを表示する

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- 先週の REST 操作の円グラフを作成する

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Azure Files の列名と説明の一覧については、「[StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs)」を参照してください。

クエリを記述する方法の詳細については、「[チュートリアル:Log Analytics クエリの使用方法](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)」を参照してください。

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](/azure/azure-monitor/platform/alerts-metric-overview)、[ログ](/azure/azure-monitor/platform/alerts-unified-log)、[アクティビティ ログ](/azure/azure-monitor/platform/activity-log-alerts)に対して設定できます。 

次の表に、監視するシナリオの例とアラートに使用する適切なメトリックを示します。

| シナリオ | アラートに使用するメトリック |
|-|-|
| ファイル共有がスロットルされている。 | メトリック: トランザクション<br>ディメンション名:応答の種類 <br>ディメンション名:FileShare (Premium ファイル共有のみ) |
| ファイル共有のサイズが容量の 80% である。 | メトリック: File Capacity (ファイル容量)<br>ディメンション名:FileShare (Premium ファイル共有のみ) |
| ファイル共有のエグレスが 1 日に 500 GiB を超えた。 | メトリック: エグレス<br>ディメンション名:FileShare (Premium ファイル共有のみ) |

### <a name="how-to-create-alerts-for-azure-files"></a>Azure Files のアラートを作成する方法

1. **Azure portal** で目的の**ストレージ アカウント**に移動します。 

2. **[アラート]** をクリックし、次に **[+ 新しいアラート ルール]** をクリックします。

3. **[リソースの編集]** をクリックし、 **[ファイル リソースの種類]** を選択してから、 **[完了]** をクリックします。 

4. **[条件の選択]** をクリックし、アラートに関する次の情報を指定します。 

    - **メトリック**
    - **ディメンション名**
    - **アラート ロジック**

5. **[アクション グループの選択]** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、アクション グループ (電子メール、SMS など) をアラートに追加します。

6. **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** などの **[アラートの詳細]** を指定します。

7. **[アラート ルールの作成]** をクリックして、アラートを作成します。

> [!NOTE]  
> アラートを作成するときに雑音が多すぎる場合は、しきい値とアラート ロジックを調整します。

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>ファイル共有がスロットルされた場合にアラートを作成する方法

1. **Azure portal** で目的の**ストレージ アカウント**に移動します。
2. **[監視]** セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの**ファイル リソースの種類**を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が `contoso` である場合、`contoso/file` リソースを選択します。
4. **[条件の選択]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[トランザクション]** メトリックを選択します。
6. **[シグナルロジックの構成]** ブレードで、 **[ディメンション名]** ドロップダウンをクリックし、 **[応答の種類]** を選択します。
7. **[ディメンション値]** ドロップダウンをクリックして、 **[SuccessWithThrottling]** (SMB の場合) または **[ClientThrottlingError]** (REST の場合) を選択します。

   > [!NOTE]
   > [SuccessWithThrottling] または [ClientThrottlingError] のディメンション値が一覧にない場合は、リソースがスロットルされていないことを意味します。 ディメンション値を追加するには、 **[ディメンション値]** ドロップダウンの横の **[カスタム値を追加]** をクリックし、「**SuccessWithThrottling**」または「**ClientThrottlingError**」と入力し、 **[OK]** をクリックしてから、手順 7 を繰り返します。

8. **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。
9. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。

   > [!NOTE]
   > ファイル共有が標準のファイル共有の場合は、 **[現在と将来のすべての値]** を選択します。 ディメンション値のドロップダウンには、ファイル共有が一覧表示されません。これは、標準ファイル共有では、共有ごとのメトリックを使用できないためです。 ストレージ アカウント内のいずれかのファイル共有がスロットルされている場合は、標準ファイル共有のスロットリング アラートがトリガーされ、どのファイル共有がスロットルされたかはアラートによって識別されません。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

10. **アラート パラメーター** (しきい値、演算子、集計粒度、評価の頻度) を定義し、 **[完了]** をクリックします。

    > [!TIP]
    > 静的しきい値を使おうとしている場合は、ファイル共有が現在スロットルされているかどうかの妥当なしきい値を判断するのにメトリック グラフが役立つことがあります。 動的しきい値を使用している場合は、メトリック グラフに最新のデータに基づいて計算されたしきい値が表示されます。

11. **[アクション グループの選択]** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、**アクション グループ** (メール、SMS など) をアラートに追加します。
12. **[アラート ルール名]** 、**[説明]**、 **[重大度]** などの **[アラートの詳細]** を指定します。
13. **[アラート ルールの作成]** をクリックして、アラートを作成します。

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Azure ファイル共有のサイズが容量の80% である場合にアラートを作成する方法

1. **Azure portal** で目的の**ストレージ アカウント**に移動します。
2. **[監視]** セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの**ファイル リソースの種類**を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が `contoso` である場合、`contoso/file` リソースを選択します。
4. **[条件の選択]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[ファイル容量]** メトリックを選択します。
6. **[シグナルロジックの構成]** ブレードで、 **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。
7. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。

   > [!NOTE]
   > ファイル共有が標準のファイル共有の場合は、 **[現在と将来のすべての値]** を選択します。 ディメンション値のドロップダウンには、ファイル共有が一覧表示されません。これは、標準ファイル共有では、共有ごとのメトリックを使用できないためです。 Standard ファイル共有のアラートは、ストレージ アカウント内のすべてのファイル共有に基づいています。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

8. **[しきい値]** をバイト単位で入力します。 たとえば、ファイル共有サイズが 100 TiB で、ファイル共有のサイズが容量の 80% である場合にアラートを受け取るには、しきい値 (バイト単位) は 87960930222080 です。
9. 残りの**アラート パラメーター** (集計粒度、評価の頻度) を定義し、 **[完了]** をクリックします。
10. [アクション グループの選択] をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、アクション グループ (メール、SMS など) をアラートに追加します。
11. **[アラート ルール名]** 、**[説明]**、 **[重大度]** などの **[アラートの詳細]** を指定します。
12. **[アラート ルールの作成]** をクリックして、アラートを作成します。

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Azure ファイル共有のエグレスが 1 日に 500 GiB を超えた場合にアラートを作成する方法

1. **Azure portal** で目的の**ストレージ アカウント**に移動します。
2. [監視] セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの**ファイル リソースの種類**を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が contoso の場合は、contoso/ファイル リソースを選択します。
4. **[条件の選択]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[エグレス]** メトリックを選択します。
6. **[シグナルロジックの構成]** ブレードで、 **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。
7. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。

   > [!NOTE]
   > ファイル共有が標準のファイル共有の場合は、 **[現在と将来のすべての値]** を選択します。 ディメンション値のドロップダウンには、ファイル共有が一覧表示されません。これは、標準ファイル共有では、共有ごとのメトリックを使用できないためです。 Standard ファイル共有のアラートは、ストレージ アカウント内のすべてのファイル共有に基づいています。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

8. しきい値に「**536870912000**」バイトと入力します。 
9. **[集計粒度]** ドロップダウンをクリックし、 **[24 時間]** を選択します。
10. **[評価の頻度]** を選択し、 **[完了]** クリックします。
11. **[アクション グループの選択]** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、**アクション グループ** (メール、SMS など) をアラートに追加します。
12. **[アラート ルール名]** 、**[説明]**、 **[重大度]** などの **[アラートの詳細]** を指定します。
13. **[アラート ルールの作成]** をクリックして、アラートを作成します。

## <a name="next-steps"></a>次のステップ

- [Azure Files 監視データのリファレンス](storage-files-monitoring.md)
- [Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/insights/monitor-azure-resource.md)
- [Azure Storage メトリックの移行](../common/storage-metrics-migration.md)
- [Azure Files のデプロイの計画](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Azure Files のデプロイ方法](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Azure Files のトラブルシューティング - Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Azure Files のトラブルシューティング - Linux](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
