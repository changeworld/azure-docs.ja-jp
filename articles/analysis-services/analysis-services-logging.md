---
title: Azure Analysis Services の診断ログ | Microsoft Docs
description: Azure Analysis Services の診断ログの設定について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d19e45710aca3e1e18be6c4529da6474a97bc59f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449337"
---
# <a name="setup-diagnostic-logging"></a>診断ログのセットアップ

Analysis Services ソリューションの重要な部分は、サーバーのパフォーマンスを監視することです。 [Azure リソースの診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を使用すると、監視および [Azure Storage](https://azure.microsoft.com/services/storage/) にログを送信したり、それらを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にストリーミング配信したり、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) ([Azure](https://www.microsoft.com/cloud-platform/operations-management-suite) のサービス) にエクスポートしたりすることができます。 

![Storage、Event Hubs、または Log Analytics への診断ログ](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>ログに記録されるもの

**[エンジン]**、**[サービス]**、および **[メトリック]** のカテゴリを選択できます。

### <a name="engine"></a>エンジン

**[エンジン]** を選択すると、すべての [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) がログに記録されます。 個々のイベントを選択することはできません。 

|XEvent カテゴリ |イベント名  |
|---------|---------|
|セキュリティ監査    |   Audit Login      |
|セキュリティ監査    |   Audit Logout      |
|セキュリティ監査    |   Audit Server Starts And Stops      |
|進行状況レポート     |   Progress Report Begin      |
|進行状況レポート     |   Progress Report End      |
|進行状況レポート     |   Progress Report Current      |
|クエリ     |  Query Begin       |
|クエリ     |   Query End      |
|コマンド     |  Command Begin       |
|コマンド     |  Command End       |
|エラーと警告     |   エラー      |
|発見     |   Discover End      |
|通知     |    通知     |
|Session     |  Session Initialize       |
|ロック    |  Deadlock       |
|クエリの処理     |   VertiPaq SE Query Begin      |
|クエリの処理     |   VertiPaq SE Query End      |
|クエリの処理     |   VertiPaq SE Query Cache Match      |
|クエリの処理     |   Direct Query Begin      |
|クエリの処理     |  Direct Query End       |

### <a name="service"></a>サービス

|操作の名前  |発生するタイミング  |
|---------|---------|
|ResumeServer     |    サーバーを再開するとき     |
|SuspendServer    |   サーバーを一時停止するとき      |
|DeleteServer     |    サーバーの削除     |
|RestartServer    |     ユーザーが SSMS または PowerShell を経由してサーバーを再起動するとき    |
|GetServerLogFiles    |    ユーザーが PowerShell を通じてサーバー ログをエクスポートするとき     |
|ExportModel     |   ユーザーが [Visual Studio で開く] を使用してポータルでモデルをエクスポートするとき     |

### <a name="all-metrics"></a>すべてのメトリック

メトリックのカテゴリは、メトリックに表示されるのと同じ[サーバーのメトリック](analysis-services-monitor.md#server-metrics)をログ記録します。

## <a name="setup-diagnostics-logging"></a>診断ログのセットアップ

### <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) > サーバーで、左のナビゲーションから、**[診断ログ]** をクリックし、**[診断をオンにする]** をクリックします。

    ![Azure Portal で Azure Cosmos DB の診断ログを有効にする](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. **[診断設定]** で、次のオプションを指定します。 

    * **名前**。 作成するログの名前を入力します。

    * **[ストレージ アカウントへのアーカイブ]**。 このオプションを使用するには、接続先として既存のストレージ アカウントが必要です。 「[ストレージ アカウントを作成する](../storage/common/storage-create-storage-account.md)」を参照してください。 指示に従って、Resource Manager の汎用アカウントを作成し、ポータルのこのページに戻ってストレージ アカウントを選択します。 新しく作成されたストレージ アカウントがドロップダウン メニューに表示されるまでには、数分かかる場合があります。
    * **イベント ハブにストリーミングします**。 このオプションを使用するには、既存の Event Hubs 名前空間と接続先のイベント ハブが必要です。 詳細については、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」をご覧ください。 Portal でこのページに戻り、Event Hubs 名前空間とポリシー名を選択します。
    * **[Log Analytics への送信]**。 このオプションを使用するには、既存のワークスペースを使用するか、ポータルで[新しいワークスペースを作成する](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)手順に従って新しい Log Analytics ワークスペースを作成します。 Log Analytics でログを表示する方法については、「[Azure Cosmos DB 診断ログ](#view-in-loganalytics)」を参照してください。

    * **エンジン**。 xEvents をログ記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **サービス**。 サービス レベル イベントをログ記録するには、このオプションを選択します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。
    * **メトリック**。 [メトリック](analysis-services-monitor.md#server-metrics)に詳細データを保存するには、このオプションを使用します。 ストレージ アカウントにアーカイブする場合、診断ログのリテンション期間を選択できます。 リテンション期間が過ぎると、ログは自動的に削除されます。

3. **[Save]** をクリックします。

    "\<ワークスペース名> の診断を更新できませんでした。 サブスクリプション \<サブスクリプション ID> は microsoft.insights を使用するために登録されていません。" というエラーが表示される場合は、[Azure 診断のトラブルシューティング](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)に関する指示に従ってアカウントを登録してから、この手順を再試行してください。

    今後、診断ログを保存する方法を変更する場合は、このページに戻って設定を変更できます。

### <a name="powershell"></a>PowerShell

興味を引く基本的なコマンドを次に示します。 PowerShell を使用してストレージ アカウントにログ記録をセットアップする詳細な手順が必要な場合は、この記事で後述するチュートリアルを参照してください。

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

### <a name="rest-api"></a>REST API

[Azure Monitor REST API を使用して診断設定を変更する](https://msdn.microsoft.com/library/azure/dn931931.aspx)方法を学習します。 

### <a name="resource-manager-template"></a>Resource Manager テンプレート

[Resource Manager テンプレートを使用してリソースの作成時に診断設定を有効にする](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md)方法を学習します。 

## <a name="manage-your-logs"></a>ログを管理する

ログは通常、ログをセットアップしてから 2 時間以内に利用できるようになります。 ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログにアクセスできるユーザーを制限することでログのセキュリティを保護するには、標準的な Azure アクセス制御方法を使用します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。
* 古いログをストレージ アカウントから削除するためのリテンション期間を必ず設定してください。

## <a name="view-logs-in-log-analytics"></a>Log Analytics ログを表示する

メトリックとサーバー イベントは、side-by-side 分析のため、Log Analytics で xEvents と統合されます。 他の Azure サービスからイベントを受信するように Log Analytics を構成して、アーキテクチャ全体の診断ログ データの全体像を提供することもできます。

Log Analytics で診断データを表示するには、次の図のように左側のメニューまたは [管理] 領域から [ログ検索] ページを開きます。

![Azure Portal の [ログ検索] オプション](./media/analysis-services-logging/aas-logging-open-log-search.png)

これでデータ収集が有効になったので、**[ログ検索]** で **[収集されたすべてのデータ]** をクリックします。

**[型]** で、**[AzureDiagnostics]**、**[適用]** の順にクリックします。 AzureDiagnostics には、エンジンとサービスのイベントが含まれています。 Log Analytics クエリが即座に作成されることに注目してください。 EventClass\_s フィールドには、オンプレミスのログ記録の xEvents を使用している場合にはなじみのある、xEvent 名が含まれています。

[**EventClass\_s**] またはいずれかのイベント名をクリックすると、Log Analytics がクエリの作成を続行します。 後で再利用するため、クエリは必ず保存しておいてください。

Log Analytics を必ず表示してください。これは、収集されたデータに対するクエリ、ダッシュボード、アラートの強化された機能を Web サイトに提供します。

### <a name="queries"></a>クエリ

使用できるクエリは数百あります。 手始めとして、いくつかのクエリを次に示します。
新しい Log Analytics のクエリ言語の使用の詳細については、「[Log Analytics でのログ検索について](../log-analytics/log-analytics-log-search-new.md)」を参照してください。 

* Azure Analysis Services に送信されるクエリで、完了までに 5 分 (300,000 ミリ秒) 以上かかったクエリを返します。

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* スケール アウト レプリカを識別します。

    ```
    search * | summarize count() by ServerName_s
    ```
    ServerName\_s フィールド値にはその名前に付加されるレプリカ インスタンス番号があるため、スケールアウトを使用する際に、読み取り専用レプリカを特定できます。 リソース フィールドには、ユーザーに表示されるサーバー名に一致する Azure のリソース名が含まれています。 IsQueryScaleoutReadonlyInstance_s フィールドはレプリカの true と同じです。



> [!TIP]
> 共有したい優れた Log Analytics クエリはありますか? GitHub アカウントをお持ちであれば、この記事に追加できます。 このページの右上にある **[編集]** をクリックするだけです。


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>チュートリアル - PowerShell を使用してログ記録を有効にする
このクイック チュートリアルでは、Analysis Service サーバーと同じサブスクリプションとリソース グループでストレージ アカウントを作成します。 次に、Set-AzureRmDiagnosticSetting を使用して、診断ログを有効にし、出力を新しいストレージ アカウントに送信します。

### <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下のリソースが必要です。

* 既存の Azure Analysis Services サーバー。 サーバー リソースの作成方法については、[Azure Portal で Azure Analysis Services サーバーを作成する](analysis-services-create-server.md)方法に関するページ、または「[PowerShell を使って Azure Analysis Services サーバーを作成する](analysis-services-create-powershell.md)」を参照してください。

### <a name="aconnect-to-your-subscriptions"></a></a>サブスクリプションへの接続

Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

```powershell
Connect-AzureRmAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Azure Key Vault を作成するときに使用した特定の 1 つを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次を入力します。

```powershell
Get-AzureRmSubscription
```

ログを記録する Azure Analysis Services アカウントに関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 複数のサブスクリプションがアカウントに関連付けられている場合は、サブスクリプションを指定することが重要です。
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>ログ用に新しいストレージ アカウントを作成する

ログの既存のストレージ アカウントがサーバーと同じサブスクリプション内にある場合は、それを使用できます。 このチュートリアルでは、Analysis Services ログ専用の新しいストレージ アカウントを作成します。 便宜上、ストレージ アカウントの詳細を **sa** という変数に格納します。

また、お使いの Analysis Services サーバーを含むリソース グループと同じリソース グループを使用します。 `awsales_resgroup`、`awsaleslogs`、`West Central US` の値を独自の値に置き換えます。

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>ログのサーバー アカウントを特定する

アカウント名を **account** という変数に設定します。この ResourceName は、アカウントの名前です。

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>ログの有効化

ログ記録を有効にするために、AzureRmDiagnosticSetting コマンドレットを、新しいストレージ アカウントの変数、サーバー アカウント、およびカテゴリと組み合わせて使用します。 次のコマンドを実行し、**-Enabled** フラグを **$true** に設定します。

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

出力は次のようになります。

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

これを見れば、ログ記録がサーバーに対して有効になっていること、ストレージ アカウントに情報が保存されることを確認できます。

古いログが自動的に削除されるように、ログのアイテム保持ポリシーを設定することもできます。 たとえば、**-RetentionEnabled** フラグを **$true** に設定し、**-RetentionInDays** パラメーターを **90** に設定したアイテム保持ポリシーを設定します。 90 日を経過したログは自動的に削除されます。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>次の手順

[Azure リソースの診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)についてさらに詳しく学習します。

PowerShell ヘルプの「[Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting)」を参照してください。
