<properties
	pageTitle="Azure Insights: Azure Insights の PowerShell クイック スタート サンプル | Microsoft Azure"
	description="Azure Insights の PowerShell クイック スタート サンプル コマンドを使用すると、Azure Insights の監視機能にすばやくアクセスできます。"
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Azure Insights の PowerShell クイック スタート サンプル

この記事では、Azure Insights の監視機能にすばやくアクセスできる PowerShell のサンプル コマンドを紹介します。Azure Insights では、AutoScale Cloud Services、Virtual Machines、Web Apps を使用できます。また、アラート通知の送信や、構成済みのテレメトリ データの値に基づく Web URL の呼び出しも可能です。

## PowerShell のセットアップ
コンピューターで実行するために PowerShell をセットアップします (まだセットアップしていない場合)。詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」をご覧ください。

## この記事の例

この記事の各例は、Azure Insights コマンドレットの使用方法を示しています。「[Azure Insights Cmdlets (Azure Insights コマンドレット)](https://msdn.microsoft.com/library/mt282452.aspx)」で、Azure Insights (監視) コマンドレットのすべてのリストを確認することもできます。


## サインインとサブスクリプションの使用

まず、Azure サブスクリプションにログインします。

```
Login-AzureRmAccount
```

そのためには、サインインする必要があります。サインインすると、アカウント、テナント ID、既定のサブスクリプション ID が表示されます。すべての Azure コマンドレットは、既定のサブスクリプションのコンテキストで動作します。アクセスできるサブスクリプションのリストを表示するには、次のコマンドを使用します。

```
Get-AzureRmSubscription
```

作業コンテキストを別のサブスクリプションに変更するには、次のコマンドを使用します。

```
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## サブスクリプションの監査ログの取得
`Get-AzureRmLog` コマンドレットを使用します。一般的な例を以下に示します。

指定した日時のログ エントリを取得します。

```
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

範囲内の日時のログ エントリを取得します。

```
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定のリソース グループのログ エントリを取得します。

```
Get-AzureRmLog -ResourceGroup 'myrg1'
```

範囲内の日時の特定リソース プロバイダーのログ エントリを取得します。

```
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

特定の呼び出し元が含まれたすべてのログ エントリを取得します。

```
Get-AzureRmLog -Caller 'myname@company.com'
```

次のコマンドは、監査ログから最後の 1000 件のイベントを取得します。

```
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` は、他にも多くのパラメーターをサポートしています。詳細については、`Get-AzureRmLog` のリファレンスをご覧ください。

>[AZURE.NOTE] `Get-AzureRmLog` は、15 日間の履歴のみを提供します。**-MaxEvents** パラメーターを使用すると、15 日間を超えて最後の N 件のイベントを照会できます。15 日より前のイベントにアクセスするには、REST API または SDK (SDK を使用した C# のサンプル) を使用します。**StartTime** を指定しない場合、既定値は **EndTime** から 1 時間引いた値になります。**EndTime** を指定しない場合、既定値は現在の時刻です。時刻はすべて UTC 形式です。

## アラートの履歴の取得
すべてのアラート イベントを表示するには、次の例を使用して Azure Resource Manager (ARM) のログを照会します。

```
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

特定のアラート ルールの履歴を表示するには、そのアラート ルールのリソース ID を渡して `Get-AzureRmAlertHistory` コマンドレットを使用します。

```
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` コマンドレットは、さまざまなパラメーターをサポートしています。詳細については、「[Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)」をご覧ください。


## アラート ルールに関する情報の取得
以下のコマンドは、いずれも "montest" という名前のリソース グループに影響を及ぼします。

アラート ルールのすべてのプロパティを表示します。

```
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

リソース グループに対するすべてのアラートを取得します。

```
Get-AzureRmAlertRule -ResourceGroup montest
```

ターゲット リソースに設定されたすべてのアラート ルールを取得します。たとえば、VM に設定されたすべてのアラート ルールを取得します。

```
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` は、他のパラメーターもサポートしています。詳細については、「[Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx)」をご覧ください。

## アラート ルールの作成
`Add-AlertRule` コマンドレットを使用して、アラート ルールを作成、更新、または無効化できます。

電子メール プロパティと Webhook プロパティは、それぞれ `New-AzureRmAlertRuleEmail` と `New-AzureRmAlertRuleWebhook` を使用して作成できます。アラート ルール コマンドレットでは、これらをアラート ルールの **Actions** プロパティにアクションとして割り当てます。

次のセクションには、さまざまなパラメーターを指定してアラート ルールを作成する方法を示すサンプルが記載されています。

### メトリックのアラート ルール
メトリックを使用するアラートを作成する際に使用されるパラメーターと値を次の表に示します。


|パラメーター|値|
|---|---|
|名前|	simpletestdiskwrite|
|このアラート ルールの場所|	米国東部|
|ResourceGroup|	montest|
|TargetResourceId|	/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|作成されたアラートの MetricName|	\\PhysicalDisk(\_Total)\\Disk Writes/sec。正確なメトリック名を取得する方法については、後述の `Get-MetricDefinitions` コマンドレットをご覧ください。| |演算子| GreaterThan| |しきい値 (このメトリックの場合、数/秒)| 1| |WindowSize (hh:mm:ss 形式)| 00:05:00| |アグリゲーター (メトリックの統計。この例では Average を使用)| Average| |カスタム電子メール (文字列配列)|'foo@example.com','bar@example.com'| |所有者、共同作成者、閲覧者への電子メールの送信| -SendToServiceOwners|

Email アクションを作成する

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Webhook アクションを作成する

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

クラシック VM の CPU 使用率メトリックのアラート ルールを作成する

```
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

アラート ルールを取得する

```
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Add アラート コマンドレットは、指定されたプロパティのアラート ルールが既に存在する場合に、そのルールの更新も実行します。アラート ルールを無効にするには、**-DisableRule** パラメーターを含めます。

### 監査ログ イベントのアラート

>[AZURE.NOTE] この機能はプレビュー段階にあります。

このシナリオでは、リソース グループ *abhingrgtest123* において、ユーザーのサブスクリプションで Web サイトが正常に開始されたときに電子メールを送信します。

電子メール ルールを設定する

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Webhook ルールを設定する

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

イベントのルールを作成する

```
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

アラート ルールを取得する

```
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

`Add-AlertRule` コマンドレットでは、他にもさまざまなパラメーターを使用できます。詳細については、「[Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx)」をご覧ください。

## アラートで使用可能なメトリックのリストの取得
`Get-AzureRmMetricDefinition` コマンドレットを使用して、特定のリソースのすべてのメトリックのリストを表示できます。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

次の例では、メトリックの名前 (Name) と単位 (Unit) を含むテーブルを生成します。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

`Get-AzureRmMetricDefinition` で使用できるオプションの完全な一覧については、「[Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx)」をご覧ください。

各種 Azure サービスで使用可能なメトリックとその単位の一覧については、「[(Web ページのタイトル)](http://link)」をご覧ください。

## 自動スケール設定の作成と管理
Web Apps、VM、Cloud Services、VM Scale Set などのリソースは、そのリソース用に構成された自動スケール設定を 1 つだけ使用できます。ただし、各自動スケール設定では複数のプロファイルを使用できます。たとえば、パフォーマンス ベースのスケール プロファイルを使用し、2 つ目のプロファイルとしてスケジュール ベースのプロファイルを使用できます。各プロファイルには、複数のルールを構成できます。自動スケールの詳細については、「[アプリケーションの自動スケールを実行する方法](../cloud-services/cloud-services-how-to-scale.md)」をご覧ください。

ここでは、次の手順を使用します。

1. ルールを作成します。
2. 前の手順で作成したルールをプロファイルにマッピングしてプロファイルを作成します。
3. 省略可能: Webhook プロパティと電子メール プロパティを構成して、自動スケールの通知を作成します。
4. これまでの手順で作成したプロファイルと通知をマッピングし、ターゲット リソースでの名前を指定して自動スケール設定を作成します。

以下の例は、CPU 使用率メトリックを使用して、Windows オペレーティング システム ベースの VM スケール セットの自動スケール設定を作成する方法を示しています。

まず、インスタンス数を増やしてスケールアウトするルールを作成します。

```
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```		

次に、インスタンス数を減らしてスケールインするルールを作成します。

```
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

ルールのプロファイルを作成します。

```
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Webhook プロパティを作成します。

```
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

前に作成した電子メールと Webhook を含めて、自動スケール設定の通知プロパティを作成します。

```
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

最後に、自動スケール設定を作成して、上記で作成したプロファイルを追加します。

```
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

自動スケール設定の管理の詳細については、「[Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)」をご覧ください。

## 自動スケールの履歴
次の例は、最近の自動スケール イベントとアラート イベントを表示する方法を示しています。監査ログの検索を使用して、自動スケールの履歴を表示します。

```
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

`Get-AzureRmAutoScaleHistory` コマンドレットを使用して、自動スケールの履歴を取得できます。

```
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

詳細については、「[Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)」をご覧ください。

### 自動スケール設定の詳細の表示
`Get-Autoscalesetting` コマンドレットを使用して、自動スケール設定の詳細を取得できます。

次の例では、リソース グループ "myrg1" のすべての自動スケール設定の詳細を表示します。

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

次の例では、リソース グループ "myrg1" の自動スケール設定の詳細を表示します。具体的には、"MyScaleVMSSSetting" という名前の自動スケール設定の詳細を表示します。

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### 自動スケール設定の削除
`Remove-Autoscalesetting` コマンドレットを使用して、自動スケール設定を削除できます。

```
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## 監査ログのログ プロファイルの管理

*ログ プロファイル*を作成し、監査ログのデータをストレージ アカウントにエクスポートできます。また、ストレージ アカウントのデータ リテンション期間を構成できます。必要に応じて、データをイベント ハブにストリーミングすることもできます。この機能は現在プレビュー段階にあり、サブスクリプションごとに作成できるログ プロファイルは 1 つに限られます。現在のサブスクリプションで以下のコマンドレットを使用して、ログ プロファイルを作成し、管理できます。また、特定のサブスクリプションを選択することもできます。PowerShell では現在のサブスクリプションが既定で使用されますが、`Set-AzureRmContext` を使用してサブスクリプションをいつでも変更できます。そのサブスクリプション内の任意のストレージ アカウントまたはイベント ハブにデータをルーティングするように監査ログを構成できます。データは、JSON 形式で BLOB ファイルとして書き込まれます。

### ログ プロファイルの取得
既存のログ プロファイルを取得するには、`Get-AzureRmLogProfile` コマンドレットを使用します。

### データ リテンション期間を指定しないログ プロファイルの追加

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### ログ プロファイルの削除

```
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### データ リテンション期間を指定したログ プロファイルの追加

データを保持する日数を正の整数で指定して、**RetentionInDays** プロパティを指定できます。

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### リテンション期間とイベント ハブを指定したログ プロファイルの追加
データは、ストレージ アカウントにルーティングするだけでなく、イベント ハブにストリーミングすることもできます。このプレビュー リリースでは、ストレージ アカウント構成は必須ですが、イベント ハブ構成は省略可能です。

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## 診断ログの構成
Azure Network Security Groups、Software Load Balancers、Key Vault、Azure Search Services、Logic Apps など、多数の Azure サービスで追加のログとテレメトリが提供されます。Azure ストレージ アカウントにデータを保存するようにこれらのサービスを構成できます。この操作はリソース レベルでのみ実行できます。ストレージ アカウントは、診断設定が構成されているターゲット リソースと同じリージョンに存在する必要があります。

### 診断設定の取得

```
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

診断設定の無効化

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

リテンション期間を指定せずに診断設定を有効にする

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

リテンション期間を指定して診断設定を有効にする

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

特定のログ カテゴリのリテンション期間を指定して診断設定を有効にする

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

<!---HONumber=AcomDC_0330_2016------>