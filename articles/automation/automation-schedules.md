<properties 
   pageTitle="Azure Automation のスケジュール"
   description="Automation のスケジュールは、Azure Automation の Runbook を自動的に開始するためのスケジュール設定に使用します。この記事では、スケジュールの作成方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/21/2015"
   ms.author="bwren" />

# Azure Automation のスケジュール

Automation のスケジュールは、Runbook を自動的に実行するためのスケジュール設定に使用します。スケジュール設定できるのは、対象 Runbook を 1 度実行するための 1 つの日時です。または、Runbook を複数回開始するための定期的なスケジュールを設定することもできます。スケジュールは、Runbook からは通常はアクセスできません。

## Windows PowerShell コマンドレット

以下の表にあるコマンドレットを使用すると、Windows PowerShell で使用される変数を Azure Automation で作成して管理できます。これらは、[Azure PowerShell モジュール](../powershell-install-configure.md)の一部として出荷されます。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|スケジュールを取得します。|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|新しいスケジュールを作成します。|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|スケジュールを削除します。|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|既存のスケジュールのプロパティを設定します。|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|スケジュール済みの Runbook を取得します。|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Runbook をスケジュールに関連付けます。|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Runbook とスケジュールの関連付けを解除します。|

## 新しいスケジュールを作成する

### Azure ポータルで新しいスケジュールを作成するには


1. Automation アカウントから、ウィンドウ上部にある **[資産]** をクリックします。
1. ウィンドウの下部にある **[設定の追加]** をクリックします。
1. **[スケジュールの追加]** をクリックします。
1. ウィザードを完了し、チェック ボックスをクリックして新しい変数を保存します。

### Azure プレビュー ポータルで新しいスケジュールを作成するには

1. Automation アカウントから、**[資産]** 部分をクリックして **[資産]** ブレードを開きます。
1. **[スケジュール]** 部分をクリックして、**[スケジュール]** ブレードを開きます。
1. ブレード上部の **[スケジュールの追加]** をクリックします。
1. フォームを完成させ、**[作成]** をクリックして新しいスケジュールを保存します。

### Windows PowerShell で新しいスケジュールを作成するには

[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) コマンドレットは新しいスケジュールを作成し、既存のスケジュールの値を設定します。次の Windows PowerShell サンプル コマンドは My Daily Schedule という名前の新しいスケジュールを作成します。このスケジュールは、明日の正午に開始して、1 年間毎日実行されます。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)
	
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## 関連項目
- [Azure Automation の Runbook をスケジュール設定する](automation-scheduling-a-runbook.md)

<!---HONumber=58--> 