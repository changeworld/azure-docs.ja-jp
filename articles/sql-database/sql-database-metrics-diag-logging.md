---
title: Azure SQL Database のメトリックと診断のロギング | Microsoft Docs
description: Azure SQL Database リソースを構成して、リソースの使用状況、接続性、およびクエリ実行の統計情報を保存する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 8f66c95202e0ccdef86f9630f7a98c20023a8955
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087748"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database のメトリックと診断のロギング 

Azure SQL Database、エラスティック プール、Managed Instance、および Managed Instance のデータベースは、パフォーマンス監視を容易にするためのメトリックと診断ログを出力できます。 リソース使用率、ワーカーとセッション、および接続性を次の Azure リソースのいずれかにストリーミングするようにデータベースを構成することができます。

* **Azure SQL Analytics**: レポート機能、アラート機能、および移行機能を備えた、統合済みの Azure データベース インテリジェント パフォーマンス監視ソリューションとして使用されます。
* **Azure Event Hubs**: SQL Database のテレメトリを、カスタム監視ソリューションまたはホット パイプラインと統合します。
* **Azure Storage**: 大量のテレメトリを低価格でアーカイブするために使用されます。

    ![アーキテクチャ](./media/sql-database-metrics-diag-logging/architecture.png)

各種の Azure サービスでサポートされているメトリックとログ カテゴリを理解するには、次の資料を参考にすることを検討してください。

* [Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

## <a name="enable-logging-of-diagnostics-telemetry"></a>診断テレメトリのログを有効にする

このドキュメントの最初のセクションを使用し、データベースの診断テレメトリを有効にします。ドキュメントの 2 つ目のパートを使用し、エラスティック プールまたは Managed Instance の診断テレメトリを有効にします。 このドキュメントの後続のセクションを使用し、ストリーミングされたデータベース診断テレメトリを表示するための監視ツールとして Azure SQL Analytics を構成します。

> [!NOTE]
> データベースに対して診断テレメトリを有効にしている以外に、エラスティック プールまたは Managed Instance を使用している場合、それらのリソースに対しても診断テレメトリを有効にすることが推奨されます。 データベース コンテナーのロールにおけるエラスティック プールと Managed Instance には、個々のデータベース診断テレメトリとは別の、独自の診断テレメトリが与えられるためです。 
>

次のいずれかの方法を使用してメトリックと診断テレメトリのロギングを有効にして管理できます。

- Azure ポータル
- PowerShell
- Azure CLI
- Azure Monitor REST API 
- Azure Resource Manager テンプレート

メトリックと診断ログを有効にする場合は、選択されたデータが収集される Azure リソースの宛先を指定する必要があります。 次のオプションを使用できます。

- Azure の SQL 分析
- Azure Event Hubs
- Azure Storage

新しい Azure リソースをプロビジョニングするか、既存のリソースを選択できます。 リソースを選択したら、診断設定オプションを使用して、収集するデータを指定する必要があります。

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Azure SQL Database または Managed Instance のデータベースのログ記録を有効にする

SQL Database と Managed Instance のデータベース上のメトリックや診断ログは、既定では有効になりません。

次の診断テレメトリは、Azure SQL Database および Managed Instance のデータベースのコレクションに使用できます。

| データベースの監視テレメトリ | Azure SQL Database のサポート | Managed Instance のデータベースのサポート |
| :------------------- | ------------------- | ------------------- |
| [すべてのメトリック](sql-database-metrics-diag-logging.md#all-metrics): DTU/CPU の割合、DTU/CPU の上限、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合が含まれます。 | [はい] | いいえ  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): CPU 使用率やクエリ実行時間の統計など、クエリのランタイム統計に関する情報が含まれます。 | [はい] | [はい] |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): CPU、ログ、ロック状態など、クエリが何を待機したかを示すクエリ待機統計に関する情報が含まれます。 | [はい] | [はい] |
| [Errors](sql-database-metrics-diag-logging.md#errors-dataset): このデータベースで発生した SQL エラーに関する情報が含まれます。 | [はい] | いいえ  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): データベースが各種の待機に費やした時間に関する情報が含まれます。 | [はい] | いいえ  |
| [Timeouts](sql-database-metrics-diag-logging.md#time-outs-dataset): データベースで発生したタイムアウトに関する情報が含まれます。 | [はい] | いいえ  |
| [Blocks](sql-database-metrics-diag-logging.md#blockings-dataset): データベースで発生したブロック イベントに関する情報が含まれます。 | [はい] | いいえ  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): パフォーマンスに対する Intelligent Insights が含まれます。 [Intelligent Insights](sql-database-intelligent-insights.md) の詳細。 | [はい] | [はい] |

### <a name="azure-portal"></a>Azure ポータル

Azure SQL Database および Managed Instance のデータベースの診断テレメトリの Azure Storage、イベント ハブ、または Log Analytics の宛先へのストリーミングは、Azure Portal の各データベースの [診断設定] メニューで構成されます。

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Azure SQL Database の診断テレメトリのストリーミングを構成する

   ![SQL Database アイコン](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

**Azure SQL Database** の診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Azure SQL Database リソースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。
- 診断テレメトリをストリーミングするための最大 3 つの並列接続を作成できます。 複数のリソースへの診断データの複数の並列ストリーミングを構成するには、**[+Add diagnostic setting] (+ 診断設定の追加)** を選択して追加の設定を作成します。

   ![SQL Database の診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

4. 設定の名前を入力します (自分の参照のため)。
5. データベースからの診断データのストリーミング先のリソースを **[Archive to storage account] (ストレージ アカウントへのアーカイブ)**、**[イベント ハブへのストリーミング]**、または **[Log Analytics への送信]** から選択します。
6. 標準の監視エクスペリエンスを得るには、データベース診断ログ テレメトリ **[SQLInsights]**、**[AutomaticTuning]**、**[QueryStoreRuntimeStatistics]**、**[QueryStoreWaitStatistics]**、**[Errors]**、**[DatabaseWaitStatistics]**、**[Timeouts]**、**[Blocks]**、**[Deadlocks]** のチェックボックスをオンにします。 このテレメトリはイベントに基づいており、標準の監視エクスペリエンスを提供します。
7. 高度な監視エクスペリエンスを得るには、**[AllMetrics]** のチェックボックスをオンにします。 これは、上で説明されているように、データベース診断テレメトリの 1 分に基づくテレメトリです。 
8. [設定] メニューの **[保存]**

   ![SQL Database の診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> 監査ログは、データベースの [診断設定] から有効にすることはできません。 監査ログのストリーミングを有効にするには、「[データベースに対する監査を設定する](sql-database-auditing.md#subheading-2)」と「[SQL Audit logs in Azure Log Analytics and Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/)」 (Azure Log Analytics と Azure Event Hubs の SQL 監査) を参照してください。
>

> [!TIP]
> 監視する Azure SQL Database ごとに上の手順を繰り返します。 
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Managed Instance のデータベースの診断テレメトリのストリーミングを構成する

   ![Managed Instance のデータベースのアイコン](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

**Managed Instance のデータベース**の診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Managed Instance のデータベースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。
- 診断テレメトリをストリーミングするための最大 3 つの並列接続を作成できます。 複数のリソースへの診断データの複数の並列ストリーミングを構成するには、**[+Add diagnostic setting] (+ 診断設定の追加)** を選択して追加の設定を作成します。

   ![Managed Instance データベースに対して診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. 設定の名前を入力します (自分の参照のため)。
5. データベースからの診断データのストリーミング先のリソースを **[Archive to storage account] (ストレージ アカウントへのアーカイブ)**、**[イベント ハブへのストリーミング]**、または **[Log Analytics への送信]** から選択します。
6. データベース診断テレメトリ **[SQLInsights]**、**[QueryStoreRuntimeStatistics]**、**[QueryStoreWaitStatistics]**、および **[Errors]** のチェックボックスをオンにします。
7. [設定] メニューの **[保存]**

   ![Managed Instance データベースに対して診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> 監視する Managed Instance のデータベースごとに上の手順を繰り返します。
>

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>エラスティック プールまたは Managed Instance のロギングを有効にする

データベース コンテナーとしてのエラスティック プールと Managed Instance には、データベースとは別の、独自の診断テレメトリがあります。 この診断テレメトリは既定では有効になっていません。 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>エラスティック プールの診断テレメトリのストリーミングを構成する

   ![エラスティック プール アイコン](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

次の診断テレメトリは、エラスティック プール リソースのコレクションに使用できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **エラスティック プール** | [すべてのメトリック](sql-database-metrics-diag-logging.md#all-metrics)には、eDTU/CPU の割合、eDTU/CPU の制限、物理データ読み取りの割合、ログ書き込みの割合、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、ストレージの制限、および XTP ストレージの割合が含まれています。 |

**エラスティック プール リソース**の診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Azure Portal でエラスティック プール リソースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。

   ![エラスティック プールに対して診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. 設定の名前を入力します (自分の参照のため)。
5. エラスティック プールからの診断データのストリーミング先のリソースを **[Archive to storage account] (ストレージ アカウントへのアーカイブ)**、**[イベント ハブへのストリーミング]**、または **[Log Analytics への送信]** から選択します。
6. Log Analytics が選択されている場合は、**[構成]** を選択し、**[+Create New Workspace] (+ 新しいワークスペースの作成)** を選択することによって新しいワークスペースを作成するか、または既存のワークスペースを選択します。
7. エラスティック プールの診断テレメトリ **[AllMetrics]** のチェックボックスをオンにします。
8. **[保存]**

   ![エラスティック プールに対して診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> 監視するエラスティック プールごとに上の手順を繰り返します。
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Managed Instance の診断テレメトリのストリーミングを構成する

   ![Managed Instance アイコン](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

次の診断テレメトリは、Managed Instance リソースのコレクションに使用できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **Managed Instance** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) には、仮想コアの個数、平均の CPU の割合、IO 要求、読み取り/書き込みバイト数、予約済みストレージ領域、使用済みストレージ領域が含まれています。 |

**Managed Instance リソース**の診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Azure Portal で Managed Instance リソースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。

   ![Managed Instance に対して診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. 設定の名前を入力します (自分の参照のため)。
5. エラスティック プールからの診断データのストリーミング先のリソースを **[Archive to storage account] (ストレージ アカウントへのアーカイブ)**、**[イベント ハブへのストリーミング]**、または **[Log Analytics への送信]** から選択します。
6. Log Analytics が選択されている場合は、既存のワークスペースを作成または使用します。
7. Managed Instance の診断テレメトリ **[ResourceUsageStats]** のチェックボックスをオンにします。
8. **[保存]**

   ![Managed Instance に対して診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> 監視する Managed Instance ごとに上の手順を繰り返します。
>

### <a name="powershell"></a>PowerShell

PowerShell を使用してメトリックと診断のロギングを有効にするには、次のコマンドを使用します。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus ルール ID は、次の形式の文字列です。

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="to-configure-multiple-azure-resources"></a>複数の Azure リソースの構成方法

複数のサブスクリプションをサポートするためには、「[Enable Azure resource metrics logging using PowerShell (PowerShell を使用して Azure リソース メトリックのログ記録を有効にする)](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)」の PowerShell スクリプトを使用します。

スクリプト (Enable-AzureRMDiagnostics.ps1) の実行時にパラメーターとしてワークスペース リソース ID &lt;$WSID&gt; を入力すると、複数のリソースからの診断データをワークスペースに送信できます。 診断データの送信先となるワークスペースの ID &lt;$WSID&gt; を取得するには、&lt;subID&gt; とサブスクリプション ID、&lt;RG_NAME&gt; とリソース グループ名を置き換え、&lt;WS_NAME&gt; に次のスクリプトのワークスペース名を入力します。

- 複数の Azure サブスクリプションを構成するには、次のコマンドを使用します。

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してメトリックと診断のロギングを有効にするには、次のコマンドを使用します。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   ストレージ アカウント ID は、ログの送信先となるストレージ アカウントのリソース ID です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Service Bus ルール ID は、この形式の文字列です。

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="rest-api"></a>REST API

[Azure Monitor REST API を使用して診断設定を変更する](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)方法を参照してください。 

### <a name="resource-manager-template"></a>Resource Manager テンプレート

[Resource Manager テンプレートを使用してリソースの作成時に診断設定を有効にする](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)方法を参照してください。 

## <a name="stream-into-azure-sql-analytics"></a>Azure SQL Analytics へのストリーム 

Azure SQL Analytics は、大規模かつ複数のサブスクリプションにまたがる Azure SQL データベースやエラスティック プール、マネージド インスタンスのパフォーマンスを、1 つの窓から監視するためのクラウド監視ソリューションです。 これを使用すると、パフォーマンスのトラブルシューティングのために、組み込みのインテリジェンスを使用して Azure SQL Database の重要なパフォーマンス メトリックを収集し、視覚化できます。

![Azure SQL Analytics の概要](../log-analytics/media/log-analytics-azure-sql/azure-sql-sol-overview.png)

SQL Database のメトリックと診断ログは、ポータルの診断設定ブレードに組み込まれている **[Send to Log Analytics]\(Log Analytics に送信\)** オプションを使用して Azure SQL Analytics にストリームできます。 Log Analytics は、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して診断設定をすることでも有効にできます。

### <a name="installation-overview"></a>インストールの概要

Azure SQL Analytics を使用すると、SQL Database フリートを簡単に監視できます。 次の 3 つの手順が必要です。

1. Azure Marketplace から Azure SQL Analytics ソリューションを作成する
2. ソリューションで監視ワークスペースを作成する
3. 作成したワークスペースに診断テレメトリをストリームするようにデータベースを構成する

データベース診断テレメトリの構成に加え、エラスティック プールまたは Managed Instances を使用している場合、それらのリソースからも診断テレメトリをストリームするように構成します。

### <a name="create-azure-sql-analytics-resource"></a>Azure SQL Analytics リソースを作成する

1. Azure Marketplace で Azure SQL Analytics を探し、それを選択します。

   ![ポータルで Azure SQL Analytics を検索する](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)
   
2. ソリューションの概要画面で **[作成]** を選択します。

3. Azure SQL Analytics フォームに必要な追加情報 (ワークスペースの名前、サブスクリプション、リソース グループ、場所、価格レベル) を入力します。
 
   ![ポータルで Azure SQL Analytics を構成する](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. **[OK]** を選択して確定し、**[作成]** を選択して完成とします。

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>メトリックと診断ログを記録するようデータベースを構成する

データベースがメトリックを記録する場所を構成する最も簡単な方法は、前述のように Azure portal を使用する方法です。 ポータルで、Azure SQL Database リソースに移動し、**[診断設定]** を選択します。

エラスティック プールまたは Managed Instances を使用している場合、作成したワークスペースに独自の診断テレメトリをストリームするために、それらのリソースで診断設定を構成する必要もあります。

### <a name="use-the-sql-analytics-solution"></a>Azure SQL Analytics ソリューションを使用する

Azure SQL Analytics は階層型のダッシュボードで、Azure SQL Database リソースの階層を移動できます。 SQL Analytics ソリューションの使用方法については、「[Monitor SQL Database by using the SQL Analytics solution](../log-analytics/log-analytics-azure-sql.md)」(SQL Analytics ソリューションを使用して SQL データベースを監視する) を参照してください。

## <a name="stream-into-event-hubs"></a>Event Hubs へのストリーム

Azure SQL Database のメトリックと診断ログは、ポータルに組み込まれている [**Stream to an event hub]\(Event Hubs に送信\)** オプションを使用して Log Analytics にストリームできます。 Service Bus ルール ID は、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して診断設定をすることでも有効にできます。 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Event Hubs におけるメトリックと診断ログの活用方法
選択したデータが Event Hubs にストリーミングされると、高度な監視シナリオを有効にできます。 Event Hubs は、イベント パイプラインの玄関口として機能します。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs は、イベントのストリームの運用と、イベントの消費を分離します。 これにより、イベントのコンシューマーは独自のスケジュールでイベントにアクセスできます。 Event Hubs の詳細については、以下を参照してください。

- [Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

ストリーミング機能を使用する場合、次のような方法があります。

* **サービスの正常性を表示するには、Power BI にホット パス データをストリーミングします**。 Event Hubs、Stream Analytics および Power BI を使用することで、メトリクスと診断データを Azure サービスの近リアルタイム洞察に簡単に転換できます。 Event Hubs の設定、Stream Analytics を使用したデータ処理、および PowerBI を出力として使用する方法の概要については、「[Stream Analytics と Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)」をご覧ください。

* **サード パーティ製のロギングおよびテレメトリ ストリームにログをストリームします**。 Event Hubs ストリーミングを使用することで、さまざまなサードパーティのモニタリングおよびログ解析ソリューションにマトリクスと診断ログを送信できます。 

* **カスタム テレメトリおよびロギング プラットフォームの構築**。 カスタマイズされたテレメトリ プラットフォームをすでに構築している場合、または構築を検討している場合は、高い拡張性の公開サブスクライブを特長とする Event Hubs を使用することで診断ログを柔軟に取り込むことができます。 [グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法に関する Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## <a name="stream-into-storage"></a>ストレージへのストリーム

Azure SQL Database のメトリックと診断ログは、ポータルに組み込まれている **[Archive to a storage account] \(ストレージ アカウントへアーカイブ\)** オプションを使用してストレージに保存できます。 ストレージは、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API の診断設定からも有効にできます。

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>ストレージ アカウントにおけるメトリックおよび診断ログのスキーマ

メトリックおよび診断ログの収集を設定した後、データの先頭行が取得されたときに、選択したストレージ アカウントにストレージ コンテナーが作成されます。 これらのBLOB の構造は次のとおりです。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
または、次のようによりシンプルな構造になります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、すべてのメトリックの BLOB 名は次のようになります。

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

エラスティック プールからデータを記録する場合、次のように BLOB 名が少し変わります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Azure Storage からメトリックとログをダウンロードする

[メトリックと診断ログをストレージからダウンロードする](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)を参照してください。

## <a name="data-retention-policy-and-pricing"></a>データ リテンション期間ポリシーと価格

Event Hubs またはストレージ アカウントを選択した場合は、保持ポリシーを指定できます。 このポリシーは、選択した期間よりも古いデータを削除します。 Log Analytics を指定した場合、リテンション期間ポリシーは選択した価格レベルに依存します。 毎月に割り当てられている無料のデータ インジェスト単位数を超えた診断テレメトリの消費が適用されます。 提供される無料のデータ インジェスト単位数により、毎月いくつかのデータベースの無料の監視が可能になります。 アイドル状態のデータベースに比べて、ワークロードが重いほど、またアクティブなデータベースが多いほど、取り込まれるデータは多くなることに注意してください。 詳細については、「[Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。 

Azure SQL Analytics を使用している場合は、Azure SQL Analytics のナビゲーション メニューで [OMS ワークスペース] を選択してから [使用量と推定コスト] を選択することによって、ソリューションでのデータ インジェストの消費を容易に監視できます。

## <a name="metrics-and-logs-available"></a>利用可能なメトリックとログ

収集された監視テレメトリは、[SQL Analytics 言語](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)による独自の**カスタム分析**と**アプリケーション開発**に利用できます。 以下は、収集したデータ、メトリック、ログの構造を一覧にしたものです。

## <a name="all-metrics"></a>すべてのメトリック

### <a name="all-metrics-for-elastic-pools"></a>エラスティック プールのすべてのメトリック

|**リソース**|**メトリック**|
|---|---|
|エラスティック プール|eDTU の割合、使用中の eDTU、eDTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、ストレージの上限、XTP ストレージの割合 |

### <a name="all-metrics-for-azure-sql-database"></a>Azure SQL Database のすべてのメトリック

|**リソース**|**メトリック**|
|---|---|
|Azure SQL Database|DTU の割合、使用中の DTU、DTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合、デッドロック |

## <a name="logs"></a>ログ

### <a name="logs-for-managed-instance"></a>Managed Instance のログ

### <a name="resource-usage-stats"></a>リソースの使用状況統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に: ResourceUsageStats|
|リソース|リソースの名前。|
|ResourceType|リソースの種類の名前。 常に: MANAGEDINSTANCES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|Managed Instance の名前。|
|resourceId|リソース URI。|
|SKU_s|Managed Instance の製品 SKU|
|virtual_core_count_s|使用可能な仮想コアの数|
|avg_cpu_percent_s|平均 CPU の割合|
|reserved_storage_mb_s|Managed Instance 上の予約済みストレージ容量|
|storage_space_used_mb_s|Managed Instance 上の使用済みストレージ|
|io_requests_s|IOPS 数|
|io_bytes_read_s|読み取り IOPS バイト|
|io_bytes_written_s|書き込み IOPS バイト|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Azure SQL Database と Managed Instance データベースのログ

### <a name="query-store-runtime-statistics"></a>クエリ ストアのランタイム統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に QueryStoreRuntimeStatistics|
|OperationName|操作の名前。 常に QueryStoreRuntimeStatisticsEvent|
|リソース|リソースの名前。|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。|
|resourceId|リソース URI。|
|query_hash_s|クエリ ハッシュ。|
|query_plan_hash_s|クエリ プラン ハッシュ。|
|statement_sql_handle_s|ステートメント sql ハンドル。|
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset。|
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset。|
|logical_io_writes_d|論理 IO 書き込みの合計数。|
|max_logical_io_writes_d|実行ごとの論理 IO 書き込みの最大数。|
|physical_io_reads_d|物理 IO 読み取りの合計数。|
|max_physical_io_reads_d|実行ごとの論理 IO 読み取りの最大数。|
|logical_io_reads_d|論理 IO 読み取りの合計数。|
|max_logical_io_reads_d|実行ごとの論理 IO 読み取りの最大数。|
|execution_type_d|実行の種類。|
|count_executions_d|クエリの実行回数。|
|cpu_time_d|クエリで使用された合計 CPU 時間 (マイクロ秒単位)。|
|max_cpu_time_d|1 回の実行による最大 CPU 時間コンシューマー (マイクロ秒単位)。|
|dop_d|並列処理の次数の合計。|
|max_dop_d|1 回の実行で使用された並列処理の最大次数。|
|rowcount_d|返された行数の合計。|
|max_rowcount_d|1 回の実行で返された行の最大数。|
|query_max_used_memory_d|サポート技術情報で使用されたメモリの合計量 (KB 単位)。|
|max_query_max_used_memory_d|1 回の実行で使用されたメモリの最大量 (KB 単位)。|
|duration_d|合計実行時間 (マイクロ秒単位)。|
|max_duration_d|1 回の実行の最大実行時間。|
|num_physical_io_reads_d|物理読み取りの合計数。|
|max_num_physical_io_reads_d|実行ごとの物理読み取りの最大数。|
|log_bytes_used_d|使用されたログの合計量 (バイト数)。|
|max_log_bytes_used_d|実行ごとに使用されたログの最大量 (バイト数)。|
|query_id_d|クエリ ストアでのクエリの ID。|
|plan_id_d|クエリ ストアでのプランの ID。|

[クエリ ストアのランタイム統計データ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)の詳細。

### <a name="query-store-wait-statistics"></a>クエリ ストアの待機統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に QueryStoreWaitStatistics|
|OperationName|操作の名前。 常に QueryStoreWaitStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。|
|resourceId|リソース URI。|
|wait_category_s|待機のカテゴリ。|
|is_parameterizable_s|クエリがパラメーター化可能かどうか。|
|statement_type_s|ステートメントの種類。|
|statement_key_hash_s|ステートメント キー ハッシュ。|
|exec_type_d|実行の種類。|
|total_query_wait_time_ms_d|特定の待機カテゴリでのクエリの合計待機時間。|
|max_query_wait_time_ms_d|特定の待機カテゴリの個々の実行でのクエリの最大待機時間。|
|query_param_type_d|0|
|query_hash_s|クエリ ストア内のクエリ ハッシュ。|
|query_plan_hash_s|クエリ ストア内のクエリ プラン ハッシュ。|
|statement_sql_handle_s|クエリ ストア内のステートメント ハンドル。|
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset。|
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset。|
|count_executions_d|クエリの実行回数。|
|query_id_d|クエリ ストアでのクエリの ID。|
|plan_id_d|クエリ ストアでのプランの ID。|

[クエリ ストアの待機統計データ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)の詳細。

### <a name="errors-dataset"></a>エラー データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に Errors|
|OperationName|操作の名前。 常に ErrorEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。|
|resourceId|リソース URI。|
|Message|プレーンテキストのエラー メッセージ。|
|user_defined_b|エラーがユーザー定義ビットかどうか。|
|error_number_d|エラー コード。|
|severity|エラーの重大度。|
|state_d|エラーの状態。|
|query_hash_s|使用可能な場合は、失敗したクエリのクエリ ハッシュ。|
|query_plan_hash_s|使用可能な場合は、失敗したクエリのクエリ プラン ハッシュ。|

[Azure SQL Server エラー メッセージ](https://msdn.microsoft.com/library/cc645603.aspx)の詳細。

### <a name="database-wait-statistics-dataset"></a>データベースの待機統計データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に DatabaseWaitStatistics|
|OperationName|操作の名前。 常に DatabaseWaitStatisticsEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。|
|resourceId|リソース URI。|
|wait_type_s|待機の種類の名前。|
|start_utc_date_t [UTC]|測定期間の開始時刻。|
|end_utc_date_t [UTC]|測定期間の終了時刻。|
|delta_max_wait_time_ms_d|実行ごとの最大待機時間|
|delta_signal_wait_time_ms_d|シグナルの合計待機時間。|
|delta_wait_time_ms_d|期間内の合計待機時間。|
|delta_waiting_tasks_count_d|待機中のタスク数。|

[データベース待機統計](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)の詳細。

### <a name="time-outs-dataset"></a>タイムアウトのデータセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に Timeouts|
|OperationName|操作の名前。 常に TimeoutEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。|
|resourceId|リソース URI。|
|error_state_d|エラー状態コード。|
|query_hash_s|クエリ ハッシュ (使用可能な場合)。|
|query_plan_hash_s|クエリ プラン ハッシュ (使用可能な場合)。|

### <a name="blockings-dataset"></a>ブロックしているデータセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に Blocks|
|OperationName|操作の名前。 常に BlockEvent|
|リソース|リソースの名前|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。|
|resourceId|リソース URI。|
|lock_mode_s|クエリで使用されるロック モード。|
|resource_owner_type_s|ロックの所有者。|
|blocked_process_filtered_s|ブロックされているプロセスのレポート XML。|
|duration_d|ロック期間 (ミリ秒)。|

### <a name="deadlocks-dataset"></a>デッドロック データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC] |ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に Deadlocks|
|OperationName|操作の名前。 常に DeadlockEvent|
|リソース|リソースの名前。|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。 |
|resourceId|リソース URI。|
|deadlock_xml_s|デッドロック レポート XML。|

### <a name="automatic-tuning-dataset"></a>自動チューニング データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID。|
|SourceSystem|常に Azure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ。|
|type|常に AzureDiagnostics|
|ResourceProvider|リソース プロバイダーの名前。 常に MICROSOFT.SQL|
|Category|カテゴリの名前。 常に AutomaticTuning|
|リソース|リソースの名前。|
|ResourceType|リソースの種類の名前。 常に SERVERS/DATABASES|
|SubscriptionId|データベースが属するサブスクリプション GUID。|
|ResourceGroup|データベースが属するリソース グループの名前。|
|LogicalServerName_s|データベースが属するサーバーの名前。|
|LogicalDatabaseName_s|データベースの名前。|
|ElasticPoolName_s|データベースが属するエラスティック プールの名前 (存在する場合)。|
|DatabaseName_s|データベースの名前。|
|resourceId|リソース URI。|
|RecommendationHash_s|自動チューニング推奨設定の一意のハッシュ。|
|OptionName_s|自動チューニング オプション。|
|Schema_s|データベース スキーマ。|
|Table_s|影響を受けるテーブル。|
|IndexName_s|インデックス名。|
|IndexColumns_s|列名。|
|IncludedColumns_s|含まれる列。|
|EstimatedImpact_s|自動チューニング推奨設定 JSON の推定される影響。|
|Event_s|自動チューニング イベントの種類。|
|Timestamp_t|最終更新時のタイムスタンプ。|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights データセット
[Intelligent Insights ログ形式](sql-database-intelligent-insights-use-diagnostics-log.md)の詳細。

## <a name="next-steps"></a>次の手順

ログ記録を有効にする方法や、各種の Azure サービスでサポートされているメトリックとログのカテゴリについては、次の資料を参照してください。

 * [Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Event Hubs の詳細については、次の資料を参照してください。

* [Azure Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

ストレージの詳細については[メトリックと診断ログをストレージからダウンロードする](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)を参照してください。
