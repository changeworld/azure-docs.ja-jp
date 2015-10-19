<properties 
   pageTitle="Azure Automation の Runbook をスケジュール設定する"
   description="特定の時刻または定期的なスケジュールで自動的に Runbook を開始できるように Azure Automation でスケジュールを作成する方法について説明します。"
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
   ms.date="10/01/2015"
   ms.author="bwren" />

# Azure Automation の Runbook をスケジュール設定する

指定の時刻に開始するように Azure Automation の Runbook をスケジュール設定するには、Runbook を 1 つ以上のスケジュールにリンクします。A schedule can be configured to either run one time or recurring every specified number of hours or days.1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。

## スケジュールを作成する

Azure 管理ポータルまたは Windows PowerShell で新しいスケジュールを作成することができます。Azure 管理ポータルを使用して Runbook をスケジュールにリンクするときに新しいスケジュールを作成するというオプションもあります。

### Azure 管理ポータルで新しいスケジュールを作成するには

1. Azure 管理ポータルで、[Automation] を選択し、次に Automation アカウントの名前をクリックします。
1. **[資産]** タブを選択します。
1. ウィンドウの下部にある **[設定の追加]** をクリックします。
1. **[スケジュールの追加]** をクリックします。
1. 新しいスケジュールの **[名前]** と、**[説明]** (省略可能) を入力します。
1. スケジュールの実行頻度を **[1 回限り]**、**[1 時間ごと]**、**[毎日]** の中から選択します。
1. **[開始時刻]** やその他のオプションを、選択したスケジュールの種類に応じて指定します。

### Windows PowerShell で新しいスケジュールを作成するには

[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) コマンドレットを使用して、Azure Automation に新しいスケジュールを作成できます。スケジュールの開始時刻と、スケジュールの実行頻度 (1 回限り、1 時間ごと、または毎日) を指定する必要があります。

次のサンプル コマンドは、2015 年 1 月 20 日から毎日午後 3 時 30 分に実行される新しいスケジュールを作成する方法を示しています。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## スケジュールを Runbook にリンクする

1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。If a runbook has parameters, then you can provide values for them.You must provide values for any mandatory parameters and may provide values for any optional parameters.These values will be used each time the runbook is started by this schedule.同じ Runbook を別のスケジュールに関連付けて、異なるパラメーター値を指定することができます。

### Azure 管理ポータルで Runbook にスケジュールをリンクするには

1. Azure 管理ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
1. **[Runbook]** タブを選択します。
1. Runbook の名前をクリックして、スケジュールを設定します。
1. **[スケジュール]** タブをクリックします。
2. 現在、Runbook がスケジュールにリンクされていない場合、**[新しいスケジュールへのリンク]** または **[既存のスケジュールへのリンク]** というオプションが表示されます。現在、Runbook がスケジュールにリンクされている場合、ウィンドウの下部にある **[リンク]** をクリックし、これらのオプションにアクセスします。
1. Runbook がパラメーターを持っている場合は、その値を入力するように求められます。  

### Windows PowerShell で Runbook にスケジュールをリンクするには

[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) を使用して Runbook にスケジュールをリンクすることができます。Parameters パラメーターを使用して、Runbook のパラメーターに値を指定できます。パラメーター値を指定する方法の詳細については、「[Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)」をご覧ください。

次のサンプル コマンドは、パラメーターを使用して Runbook にスケジュールをリンクする方法を示しています。

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## スケジュールを無効にする

スケジュールを無効にすると、そのスケジュールにリンクされた Runbook はいずれもそのスケジュールに基づいて実行できなくなります。You can manually disable a schedule or set an expiration time for Hourly and Daily schedules when you create them.有効期限の時刻に達すると、スケジュールが無効になります。

### Azure 管理ポータルでスケジュールを無効にするには

Azure 管理ポータルで、スケジュール用の [スケジュールの詳細] ページからスケジュールを無効にすることができます。

1. In the Azure Management Portal, select Automation and then then click the name of an automation account.
1. Select the Assets tab.
1. スケジュールの名前をクリックして、その詳細ページを開きます。
2. **[有効]** を **[いいえ]** に変更します。

### Windows PowerShell でスケジュールを無効にするには

[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) コマンドレットを使用して、既存のスケジュールのプロパティを変更できます。スケジュールを無効にするには、**IsEnabled** パラメーターに **false** を指定します。

次のサンプル コマンドは、スケジュールを無効にする方法を示しています。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## 関連記事:

- [Azure Automation のスケジュール資産](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Azure Automation での Runbook を開始する](automation-starting-a-runbook.md) 

<!---HONumber=Oct15_HO2-->