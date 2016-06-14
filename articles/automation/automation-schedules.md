<properties
   pageTitle="Azure Automation のスケジュール | Microsoft Azure"
   description="Automation のスケジュールは、Azure Automation の Runbook を自動的に開始するためのスケジュール設定に使用します。この記事では、スケジュールの作成方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="bwren" />

# Azure Automation のスケジュール

Automation のスケジュールは、Runbook を自動的に実行するためのスケジュール設定に使用します。Runbook を 1 回実行する単一の日付時刻が可能です。また、Runbook を複数回開始するように、時間単位、日単位、週単位、月単位の反復スケジュールも可能です。スケジュールは、Runbook からは通常はアクセスできません。

>[AZURE.NOTE]  スケジュールは、現時点では Azure Automation DSC 構成をサポートしていません。

## Windows PowerShell コマンドレット

Azure Automation から Windows PowerShell を使用してスケジュールを作成したり管理したりするには、以下の表に示したコマンドレットを使用します。これらは、[Azure PowerShell モジュール](../powershell-install-configure.md)の一部として出荷されます。

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

### Azure クラシック ポータルで新しいスケジュールを作成するには


1. Automation アカウントから、ウィンドウの上部にある **[資産]** をクリックします。
1. ウィンドウの下部にある **[設定の追加]** をクリックします。
1. **[スケジュールの追加]** をクリックします。
1. ウィザードを完了し、チェックボックスをクリックして新しいスケジュールを保存します。

### Azure ポータルで新しいスケジュールを作成するには

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

<!---HONumber=AcomDC_0601_2016-->