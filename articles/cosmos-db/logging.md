---
title: Azure Cosmos DB 診断ログ | Microsoft Docs
description: このチュートリアルを使用すると、Azure Cosmos DB のログ記録を簡単に始めることができます。
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mimig
ms.openlocfilehash: f647387b4e80c36339a456b8e9a2cfade7ac8102
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Azure Cosmos DB 診断ログ

1 つまたは複数の Azure Cosmos DB データベースを使用し始めた場合、データベースのアクセス方法と時間を監視したいと考えるのではないでしょうか。 この記事では、Azure プラットフォームで利用可能なすべてのログの概要を説明した後、監視目的で診断ログの記録を有効にして、ログの [Azure Storage](https://azure.microsoft.com/services/storage/) への送信、ログの [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) へのストリーミング、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) ([Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) の一部) へのエクスポートを行う方法について説明します。

## <a name="logs-available-in-azure"></a>Azure で利用可能なログ

Azure Cosmos DB アカウントの監視を始める前に、ログと監視に関するいくつかの点を明確にしておきます。 Azure プラットフォームのログには、さまざまな種類があります。 [Azure アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)、[Azure 診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)、[メトリック](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)、イベント、ハートビート監視、操作ログなどです。大量のログが存在します。 Azure Portal の[Azure Log Analytics](https://azure.microsoft.com/en-us/services/log-analytics/) で、ログの完全な一覧を確認できます。 

次の図は、利用可能な Azure ログの種類を示しています。

![さまざまな種類の Azure ログ](./media/logging/azurelogging.png)

上記の図の**コンピューティング リソース**とは、ユーザーがゲスト OS にアクセスできる Azure リソースを表します。 たとえば、Microsoft Azure Virtual Machines、Virtual Machine のスケール セット、Azure Container Service などが、コンピューティング リソースと見なされます。 コンピューティング リソースは、アクション ログ、診断ログとアプリケーション ログを生成します。 詳細については、[Azure Monitoring – コンピューティング リソース](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset)に関する記事を参照してください。

**非コンピューティング リソース**とは、ユーザーが基盤となる OS にアクセスできず、リソースに直接作業するリソースです。 たとえば、ネットワーク セキュリティ グループ、Azure Logic Apps などが挙げられます。**Cosmos DB** は、非コンピューティング リソースです。 非コンピューティング リソースのログは、アクティビティ ログで確認するか、ポータルの [診断ログ] オプションを有効にすることで表示できます。 詳細については、[Azure Monitoring – 非コンピューティング リソース](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else)に関する記事を参照してください。

アクティビティ ログは、ListKeys、DatabaseAccounts などの Cosmos DB のサブスクリプション レベルの操作を記録します。 診断ログは、より詳細なログ記録を提供し、DataPlaneRequests (Create, Read, Query.. ) と MongoRequests を記録できます。


この記事では、Azure アクティビティ、Azure 診断、およびメトリックに注目します。 この 3 つのログの違いは何でしょうか。 

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]

Azure アクティビティ ログは、Azure で発生したサブスクリプション レベルのイベントの分析に利用できるサブスクリプション ログです。 アクティビティ ログは、サブスクリプションのコントロール プレーン イベントを管理カテゴリで報告します。 アクティビティ ログを使用すると、サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 

アクティビティ ログは診断ログとは異なります。 アクティビティ ログは、外部から行われるリソースの操作に関するデータを提供します ("コントロール プレーン")。 Azure Cosmos DB のコンテキストでは、コントロール プレーンの操作の一部に、コレクションの作成、キーの一覧表示、キーの削除、データベースの一覧表示などが含まれます。診断ログは、リソースによって出力され、そのリソースの操作に関する情報を提供します ("データ プレーン")。 データ プレーンの診断ログの例には、delete、insert、readfeed 操作などがあります。

アクティビティ ログ (コントロール プレーン操作) は、実際は非常に大量になる可能性があり、呼び出し元の完全な電子メール アドレス、呼び出し元の IP アドレス、リソース名、操作名、TenantId などが含まれる可能性があります。アクティビティ ログには、データの複数の[カテゴリ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema)が含まれます。 これらのカテゴリのスキーマの詳細については、「[Azure アクティビティ ログのイベント スキーマ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema)」を参照してください。  ただし、診断ログでは、多くの場合 PII データが削除されるため、実際は限定的である可能性があります。 そのため、呼び出し元の IP アドレスが存在する可能性がありますが、最後のオクテットは削除されています。

### <a name="azure-metrics"></a>Azure メトリック

大半の Azure リソースによって出力される Azure テレメトリ データ (パフォーマンスカウンターとも呼ばれます)の最も重要な種類は、[Azure メトリック](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics)です。 メトリックを使用して、Azure Cosmos DB リソースのスループット、ストレージ、整合性、可用性、および 待機時間に関する情報を表示できます。 詳細については、「[Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)」を参照してください。

### <a name="azure-diagnostic-logs"></a>Azure 診断ログ

Azure 診断ログは、リソースによって出力されるログであり、そのリソースの操作に関する豊富なデータを提供します。 これらのログの内容は、リソースの種類によって異なります。 リソースレベルの診断ログは、ゲスト OS レベルの診断ログとも異なります。 ゲスト OS 診断ログは、仮想マシンの内部で実行されているエージェントや、他のサポートされるリソースの種類によって収集されるログです。 リソースレベルの診断ログの場合、エージェントは不要です。Azure プラットフォーム自体からリソース固有のデータをキャプチャします。一方、ゲスト OS レベルの診断ログの場合、仮想マシン上で実行されているオペレーティング システムとアプリケーションからデータをキャプチャします。

![Log Analytics を介した Storage、Event Hubs、Operations Management Suite への診断ログ記録](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Azure 診断ログによって記録されるもの

* すべての API で認証されたバックエンド要求 (TCP / REST) がすべて記録されます。たとえば、アクセス許可、システム エラー、または不正な要求の結果として失敗した要求などが記録されます。 ユーザーが開始した Graph、Cassandra、および Table API 要求のサポートは現在使用できません。
* データベース自体に対する操作。すべてのドキュメント、コンテナー、およびデータベースに対する CRUD 操作が含まれます。
* アカウント キーに対する操作。これらのキーの作成、変更、または削除が含まれます。
* 結果として 401 応答が発生する、認証されていない要求。 たとえば、ベアラー トークンを持たない要求、形式が正しくない要求、有効期限切れの要求、または無効なトークンを持つ要求です。

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Azure Portal でログを有効にする

診断ログを有効にするには、次のリソースが必要です。

* 既存の Azure Cosmos DB アカウント、データベース、およびコンテナー。 これらのリソースの作成手順については、[Azure Portal を使用したデータベース アカウントの作成](create-sql-api-dotnet.md#create-a-database-account)、[CLI サンプル](cli-samples.md)、または [PowerShell サンプル](powershell-samples.md)に関するページを参照してください。

Azure Portal で診断ログを有効にするには、以下を実行します。

1. [Azure Portal](https://portal.azure.com) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[診断ログ]** をクリックし、**[診断をオンにする]** をクリックします。

    ![Azure Portal で Azure Cosmos DB の診断ログを有効にする](./media/logging/turn-on-portal-logging.png)

2. **[診断設定]** ページで次の操作を行います。 

    * **名前**。 作成するログの名前を入力します。

    * **[ストレージ アカウントへのアーカイブ]**。 このオプションを使用するには、接続先として既存のストレージ アカウントが必要です。 Portal で新しいストレージ アカウントを作成するには、[ストレージ アカウントの作成に関するページ](../storage/common/storage-create-storage-account.md)を参照し、Resource Manager の汎用アカウントの作成手順を実行します。 Portal でこのページに戻り、ストレージ アカウントを選択します。 新しく作成されたストレージ アカウントがドロップダウン メニューに表示されるまでには、数分かかる場合があります。
    * **イベント ハブにストリーミングします**。 このオプションを使用するには、既存の Event Hubs 名前空間と接続先のイベント ハブが必要です。 Event Hubs 名前空間を作成するには、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」を参照してください。 Portal でこのページに戻り、Event Hubs 名前空間とポリシー名を選択します。
    * **[Log Analytics への送信]**。     このオプションを使用するには、既存のワークスペースを使用するか、ポータルで[新しいワークスペースを作成する](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)手順に従って新しい Log Analytics ワークスペースを作成します。 Log Analytics でログを表示する方法については、「[Azure Cosmos DB 診断ログ](#view-in-loganalytics)」を参照してください。
    * **DataPlaneRequests をログに記録します**。 SQL、Graph、MongoDB、Cassandra、および Table API アカウントについて、Azure Cosmos DB の基礎となる分散プラットフォームからのバックエンド要求をログに記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **[Log MongoRequests]\(MongoRequests をログに記録する\)**。 MongoDB API アカウントにサービスを提供するためにユーザーが Azure Cosmos DB のフロントエンドから開始した要求をログに記録するには、このオプションを選択します。  ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **[Metric Requests]\(メトリック要求\)**。 [Azure メトリックス](../monitoring-and-diagnostics/monitoring-supported-metrics.md)に詳細データを保存するには、このオプションを使用します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。

3. **[Save]** をクリックします。

    "\<ワークスペース名> の診断を更新できませんでした。 サブスクリプション \<サブスクリプション ID> は microsoft.insights を使用するために登録されていません。" というエラーが表示される場合は、[Azure 診断のトラブルシューティング](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)に関する指示に従ってアカウントを登録してから、この手順を再試行してください。

    今後、診断ログを保存する方法を変更する場合は、いつでもこのページに戻ってアカウントの診断ログの設定を変更できます。

## <a name="turn-on-logging-using-cli"></a>CLI を使用してログ記録を有効にする

Azure CLI を使用してメトリックと診断のロギングを有効にするには、次のコマンドを使用します。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` は、Azure Cosmos DB アカウントの名前です。 `storageId` は、ログの送信先のストレージ アカウント名です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` は、Azure Cosmos DB アカウントの名前です。 `serviceBusRuleId` は、次の形式の文字列です。

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

## <a name="turn-on-logging-using-powershell"></a>PowerShell を使用してログ記録を有効にする

PowerShell を使用して診断ログを有効にするには、Azure PowerShell バージョン 1.0.1 以降が必要です。

Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

Azure PowerShell をインストール済みで、バージョンがわからない場合は、PowerShell コンソールで「`(Get-Module azure -ListAvailable).Version`」と入力します。  

### <a id="connect"></a>サブスクリプションへの接続
Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

```powershell
Login-AzureRmAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Azure Key Vault を作成するときに使用した特定の 1 つを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次を入力します。

```powershell
Get-AzureRmSubscription
```

ログを記録する Azure Cosmos DB アカウントに関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 複数のサブスクリプションがアカウントに関連付けられている場合は、サブスクリプションを指定することが重要です。
>
>

Azure PowerShell の詳細については、「 [How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](/powershell/azure/overview)」をご覧ください。

### <a id="storage"></a>ログ用に新しいストレージ アカウントを作成する
ログに既存のストレージ アカウントを使用することもできますが、このチュートリアルでは、Azure Cosmos DB ログ専用の新しいストレージ アカウントを作成します。 便宜上、ストレージ アカウントの詳細を **sa** という変数に格納します。

さらに管理を簡単にするために、このチュートリアルでは、Azure Cosmos DB データベースと同じリソース グループを使用します。 ContosoResourceGroup、contosocosmosdblogs、"米国中北部" の値は、適宜、実際の値で置き換えてください。

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> 既存のストレージ アカウントを使用する場合、そのストレージ アカウントでは、目的の Azure Cosmos DB と同じサブスクリプションを使用する必要があります。また、クラシック デプロイメント モデルではなく、リソース マネージャー デプロイメント モデルを使用する必要があります。
>
>

### <a id="identify"></a>ログ用の Azure Cosmos DB アカウントを指定する
Azure Cosmos DB アカウント名を **account** という変数に設定します。この ResourceName は、Azure Cosmos DB アカウントの名前です。

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>ログの有効化
Azure Cosmos DB のログ記録を有効にするには、新しいストレージ アカウント、Azure Cosmos DB アカウント、およびログを有効にするカテゴリの変数を指定して、Set-AzureRmDiagnosticSetting コマンドレットを使用します。 次のコマンドを実行し、**-Enabled** フラグを **$true** に設定します。

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

コマンドの出力は次のようになります。

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

これを見れば、ログ記録がデータベースに対して有効になっていること、ストレージ アカウントに情報が保存されることを確認できます。

必要に応じて、ログのリテンション期間ポリシーを使用して、古いログが自動的に削除されるように設定することもできます。 たとえば、**-RetentionEnabled** フラグを **$true** に設定し、**-RetentionInDays** パラメーターを **90** に設定したリテンション期間ポリシーを設定すると、90 日より前のログが自動的に削除されます。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>ログへのアクセス
**DataPlaneRequests** カテゴリの Azure Cosmos DB ログは、指定したストレージ アカウントの **insights-logs-data-plane-requests** コンテナーに格納されます。 

最初に、コンテナー名の変数を作成します。 この変数は、チュートリアルの残りの部分で使用します。

```powershell
    $container = 'insights-logs-dataplanerequests'
```

このコンテナー内のすべての BLOB を一覧表示するには、次のように入力します。

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

出力は次のようになります。

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

この出力からわかるように、BLOB は次の命名規則に従います。`resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日付と時刻の値には UTC が使用されます。

同じストレージ アカウントを使用して複数のリソースのログを収集することができるので、必要な BLOB のみにアクセスしたり、ダウンロードしたりする場合には、BLOB 名の完全修飾リソース ID を使用すると便利です。 その前に、すべての BLOB をダウンロードする方法を説明します。

まず、フォルダーを作成して BLOB をダウンロードします。 例: 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

すべての BLOB の一覧を取得します。  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

'Get-AzureStorageBlobContent' を介してこの一覧をパイプして、BLOB を宛先フォルダーにダウンロードします。

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

この 2 番目のコマンドを実行すると、BLOB 名に含まれる **/** 区切り記号によって、宛先フォルダーの下にフォルダー構造全体が作成されます。 このフォルダー構造は、BLOB をファイルとしてダウンロードし、保存するために使用されます。

BLOB を選択的にダウンロードするには、ワイルドカードを使用します。 例: 

* 複数のデータベースを持っている場合に、CONTOSOCOSMOSDB3 という名前のデータベースのみについてログをダウンロードするには、次のようにします。

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* 複数のリソース グループを持っている場合、1 つのリソース グループのみについてログをダウンロードするには、次のように `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`を使用します。

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* 2017 年 7 月のすべてのログをダウンロードする場合は、次のように `-Blob '*/year=2017/m=07/*'`を使用します。

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

加えて次の作業を行います。

* データベース リソースの診断設定の状態のクエリを実行する場合: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* データベース アカウント リソースの **DataPlaneRequests** カテゴリのログ記録を無効にする場合: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


これらの各クエリで返される BLOB は、次のコードのように、JSON BLOB 形式のテキストに格納されます。 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

各 JSON BLOB のデータについては、「[Azure Cosmos DB ログを解釈する](#interpret)」を参照してください。

## <a name="managing-your-logs"></a>ログの管理

診断ログは、アカウントで Azure Cosmos DB 操作が実行されてから 2 時間後に使用できます。 ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログにアクセスできるユーザーを制限することでログのセキュリティを保護するには、標準的な Azure アクセス制御方法を使用します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。
* ストレージ アカウントにアーカイブされるデータ プレーン要求のリテンション期間は、**[Log DataPlaneRequests]\(DataPlaneRequests のログを記録する\)** をオンにしたときに構成されます。 この設定を変更する方法については、「[Azure Portal でログを有効にする](#turn-on-logging-in-the-azure-portal)」を参照してください。


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Log Analytics ログを表示する

ログ記録を有効にするときに **[Log Analytics への送信]** オプションを選択した場合、コレクションの診断ログは 2 時間以内に Log Analytics に転送されます。 つまり、ログ記録を有効にした直後に Log Analytics を見てもデータは何も表示されません。 2 時間待ってから、もう一度やり直してください。 

ログを確認する前に、Log Analytics ワークスペースが新しい Log Analytics クエリ言語を使用できるようにアップグレードされているかどうかを確認します。 確認するには、[Azure Portal](https://portal.azure.com) で左端の **[Log Analytics]** をクリックし、次の図のようにワークスペース名を選択します。 次の図のように **[OMS ワークスペース]** ページが表示されます。

![Azure Portal の Log Analytics](./media/logging/azure-portal.png)

**[OMS ワークスペース]** ページに次のメッセージが表示される場合、ワークスペースは新しい言語を使用できるようにアップグレードされていません。 新しいクエリ言語にアップグレードする方法の詳細については、「[新しいログ検索への Azure Log Analytics ワークスペースのアップグレード](../log-analytics/log-analytics-log-search-upgrade.md)」を参照してください。 

![Log Analytics のアップグレード通知](./media/logging/upgrade-notification.png)

Log Analytics で診断データを表示するには、次の図のように左側のメニューまたはページの [管理] 領域から [ログ検索] ページを開きます。

![Azure Portal の [ログ検索] オプション](./media/logging/log-analytics-open-log-search.png)

データ収集を有効にしたら、新しいクエリ言語を使用して、次のログ検索例を実行し、最新のログ `AzureDiagnostics | take 10` を確認します。

![10 個のログ検索を取得するサンプル](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>クエリ

**[ログ検索]** ボックスに入力して Azure Cosmos DB コンテナーの監視に利用できるその他のクエリを紹介します。 これらのクエリは[新しい言語](../log-analytics/log-analytics-log-search-upgrade.md)で使用できます。 

各ログ検索から返されるデータの意味については、「[Azure Cosmos DB ログを解釈する](#interpret)」を参照してください。

* 指定した期間の Azure Cosmos DB のすべての診断ログ。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* ログに記録された過去 10 個のイベント。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* 操作の種類でグループ分けされたすべての操作。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* リソースでグループ分けされたすべての操作。

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* リソースでグループ分けされたすべてのユーザー アクティビティ。 診断ログではなく、アクティビティ ログである点に注意してください。

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* 実行時間が 3 ミリ秒を超えている操作。

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 操作を実行しているエージェント。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* 実行時間が長い操作が実行された時刻。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

新しい Log Analytics 言語の使用方法の詳細については、「[Log Analytics でのログ検索について](../log-analytics/log-analytics-log-search-new.md)」を参照してください。 

## <a id="interpret"></a>ログを解釈する

Azure Storage と Log Analytics に格納されている診断データは、スキーマがよく似ています。 

次の表は、各ログ エントリの内容をまとめた一覧です。

| Azure Storage のフィールドまたはプロパティ | Log Analytics のプロパティ | [説明] |
| --- | --- | --- |
| time | TimeGenerated | 操作が発生した日時 (UTC)。 |
| ResourceId | リソース | ログが有効になっている Azure Cosmos DB アカウント。|
| カテゴリ | カテゴリ | Azure Cosmos DB ログの場合、使用できる値は DataPlaneRequests のみです。 |
| operationName | OperationName | 操作の名前。 この値は、Create、Update、Read、ReadFeed、Delete、Replace、Execute、SqlQuery、Query、JSQuery、Head、HeadFeed、または Upsert 操作のいずれかです。   |
| プロパティ | 該当なし | このフィールドの内容については、以下の行を参照してください。 |
| activityId | activityId_g | ログに記録された操作の一意の GUID。 |
| userAgent | userAgent_s | 要求を実行するクライアント ユーザー エージェントを示す文字列。 {ユーザー エージェント名}/{バージョン} という形式です。|
| resourceType | ResourceType | アクセスされたリソースの種類。 この値は、リソースの種類 Database、Collection、Document、Attachment、User、Permission、StoredProcedure、Trigger、UserDefinedFunction、または Offer のいずれかです。 |
| StatusCode |statusCode_s | 操作の応答状態。 |
| requestResourceId | ResourceId | 要求に関する resourceId は、実行された操作によって databaseRid、collectionRid、または documentRid の可能性があります。|
| clientIpAddress | clientIpAddress_s | クライアントの IP アドレス。 |
| requestCharge | requestCharge_s | 操作に使用された RU 数 |
| collectionRid | collectionId_s | コレクションの一意の ID。|
| duration | duration_s | 操作の期間 (ティック単位)。 |
| requestLength | requestLength_s | 要求の長さ (バイト単位)。 |
| responseLength | responseLength_s | 応答の長さ (バイト単位)。|
| resourceTokenUserRid | resourceTokenUserRid_s | 認証に[リソース トークン](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)が使用され、ユーザーのリソース ID を示す場合、この値は空ではありません。 |

## <a name="next-steps"></a>次の手順

- ロギングを有効にする方法だけでなく、各種 Azure サービスでサポートされるメトリックとログのカテゴリーについて理解を深めるには、「[Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」と「[Azure リソースからのログ データの収集と使用](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)」の両方を参照してください。
- Event Hubs については次の記事をご覧ください。
   - [Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- [Azure Storage からメトリックとログをダウンロードする方法](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)に関する記事を参照してください
- 「[Log Analytics でのログ検索について](../log-analytics/log-analytics-log-search-new.md)」を参照してください
