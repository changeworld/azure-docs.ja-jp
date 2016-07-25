<properties 
   pageTitle="Azure Automation の Runbook をスケジュール設定する"
   description="特定の時刻または定期的なスケジュールで自動的に Runbook を開始できるように Azure Automation でスケジュールを作成する方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="bwren" />

# Azure Automation の Runbook をスケジュール設定する

指定の時刻に開始するように Azure Automation の Runbook をスケジュール設定するには、Runbook を 1 つ以上のスケジュールにリンクします。スケジュールは、1 回だけ実行するようにも、複数回実行するようにも構成できます。Azure クラシック ポータルの Runbook では、時間または日単位で繰り返すスケジュールを指定でき、Azure ポータルの Runbook ではさらに週単位、月単位、特定の曜日や日にち、または月の特定の日のスケジュールも指定できます。1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。


## スケジュールを作成する

Runbook の新しいスケジュールは、Azure ポータル、クラシック ポータル、または Windows PowerShell で作成することができます。Azure クラシック ポータルまたは Azure ポータルを使用して Runbook をスケジュールにリンクするときに新しいスケジュールを作成するというオプションもあります。

>[AZURE.NOTE] スケジュールを Runbook に関連付けると、アカウントのモジュールの現在のバージョンが保存され、スケジュールにリンクされます。つまり、スケジュールを作成したときにアカウントのモジュールのバージョンが 1.0 だった場合は、そのモジュールをバージョン 2.0 に更新しても、スケジュールでは引き続きバージョン 1.0 が使用されます。更新後のバージョンを使用するには、新しいスケジュールを作成する必要があります。

### Azure クラシック ポータルで新しいスケジュールを作成するには

1. Azure クラシック ポータルで、[Automation] を選択し、次に Automation アカウントの名前を選択します。
1. **[資産]** タブを選択します。
1. ウィンドウの下部にある **[設定の追加]** をクリックします。
1. **[スケジュールの追加]** をクリックします。
1. 新しいスケジュールの**名前**と、必要に応じて**説明**を入力します。スケジュールは、**[1 回限り]**、**[時間単位]**、または **[日単位]** で実行できます。
1. **[開始時刻]** やその他のオプションを、選択したスケジュールの種類に応じて指定します。

### Azure ポータルで新しいスケジュールを作成するには

1. Azure ポータルで、Automation アカウントから **[資産]** タイルをクリックして、**[資産]** ブレードを開きます。
2. **[スケジュール]** タイルをクリックして、**[スケジュール]** ブレードを開きます。
3. ブレード上部の **[スケジュールの追加]** をクリックします。
4. **[新しいスケジュール]** ブレードで、新しいスケジュールの**名前**と、必要に応じて**説明**を入力します。
5. スケジュールを 1 回だけ実行するか、繰り返し実行するかを、**[1 回]** または **[繰り返し]** から選択します。**[1 回]** を選択した場合は、**[開始時刻]** を指定し、**[作成]** をクリックします。**[繰り返し]** を選択した場合は、**[開始時刻]** を指定し、Runbook を繰り返す頻度を **[時間]**、**[日]**、**[週]**、または **[月]** から選択します。ドロップダウン リストで **[週]** または **[月]** を選択した場合は、ブレードに **[Recurrence option (繰り返しのオプション)]** が表示されます。選択すると **[Recurrence option (繰り返しのオプション)]** ブレードが表示され、**[週]** を選択した場合は曜日を選択できます。**[月]** を選択した場合は、**[平日]** を選択するか、カレンダーで月の特定の日を選択できます。最後に、月の最終日に実行するかどうかを選択し、**[OK]** をクリックします。

### Windows PowerShell で新しいスケジュールを作成するには

クラシック Runbook 用に Azure Automation の新しいスケジュールを作成するには、[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) コマンドレットを使用します。Azure ポータルの Runbook 用には、[New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) コマンドレットを使用します。スケジュールの開始時刻と実行の頻度を指定する必要があります。

次のサンプル コマンドは、2015 年 1 月 20 日から毎日午後 3 時 30 分に実行される新しいスケジュールを Azure サービス管理コマンドレットで作成する方法を示しています。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

次のサンプル コマンドは、毎月 15 日と 30 日に実行されるスケジュールを Azure Resource Manager コマンドレットで作成する方法を示しています。

    $automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
    

## スケジュールを Runbook にリンクする

1 つの Runbook を複数のスケジュールにリンクし、1 つのスケジュールを複数の Runbook にリンクすることができます。Runbook にパラメーターがある場合は、その値を指定できます。必須のパラメーターについては必ず値を指定してください。オプションのパラメーターについては値の指定は任意です。このスケジュールで Runbook が開始されるたびに、これらの値が使用されます。同じ Runbook を別のスケジュールに関連付けて、異なるパラメーター値を指定することができます。


### Azure クラシック ポータルで Runbook にスケジュールをリンクするには

1. Azure クラシック ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
2. **[Runbook]** タブを選択します。
3. Runbook の名前をクリックして、スケジュールを設定します。
4. **[スケジュール]** タブをクリックします。
5. 現在、Runbook がスケジュールにリンクされていない場合、**[新しいスケジュールへのリンク]** または **[既存のスケジュールへのリンク]** というオプションが表示されます。現在、Runbook がスケジュールにリンクされている場合、ウィンドウの下部にある **[リンク]** をクリックし、これらのオプションにアクセスします。
6. Runbook がパラメーターを持っている場合は、その値を入力するように求められます。

### Azure ポータルで Runbook にスケジュールをリンクするには

1. Azure ポータルで、Automation アカウントから **[Runbook]** タイルをクリックして、**[Runbook]** ブレードを開きます。
2. Runbook の名前をクリックして、スケジュールを設定します。
3. 現在、Runbook がスケジュールにリンクされていない場合は、新しいスケジュールの作成、または既存のスケジュールへのリンクというオプションが表示されます。
4. Runbook にパラメーターがある場合は、オプション **[実行設定を変更する (既定: Azure)]** を選択すると **[パラメーター]** ブレードが表示されるので、必要に応じて情報を入力することができます。

### Windows PowerShell で Runbook にスケジュールをリンクするには

クラシック Runbook にスケジュールをリンクするには [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) を使用します。Azure ポータルの Runbook の場合は、[Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) コマンドレットを使用します。Parameters パラメーターを使用して、Runbook のパラメーターに値を指定できます。パラメーター値を指定する方法の詳細については、「[Azure Automation での Runbook を開始する](automation-starting-a-runbook.md)」をご覧ください。

次のサンプル コマンドは、パラメーターを使用して Azure サービス管理コマンドレットでスケジュールをリンクする方法を示しています。

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

次のサンプル コマンドは、パラメーターを使用して Azure Resource Manager コマンドレットでスケジュールを Runbook にリンクする方法を示しています。

    $automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## スケジュールを無効にする

スケジュールを無効にすると、そのスケジュールにリンクされた Runbook はいずれもそのスケジュールに基づいて実行できなくなります。スケジュールは手動で無効にしたり、頻度が指定されているスケジュールの有効期限を、作成時に設定したりすることができます。有効期限の時刻に達すると、スケジュールが無効になります。

### Azure クラシック ポータルからスケジュールを無効にするには

Azure クラシック ポータルで、スケジュール用の [スケジュールの詳細] ページからスケジュールを無効にすることができます。

1. Azure クラシック ポータルで、[Automation] を選択し、次に Automation アカウントの名前をクリックします。
1. Select the Assets tab.
1. スケジュールの名前をクリックして、その詳細ページを開きます。
2. **[有効]** を **[いいえ]** に変更します。

### Azure ポータルからスケジュールを無効にするには

1. Azure ポータルで、Automation アカウントから **[資産]** タイルをクリックして、**[資産]** ブレードを開きます。
2. **[スケジュール]** タイルをクリックして、**[スケジュール]** ブレードを開きます。
2. スケジュールの名前をクリックして、その詳細ブレードを開きます。
3. **[有効]** を **[いいえ]** に変更します。

### Windows PowerShell でスケジュールを無効にするには

クラシック Runbook 用の既存のスケジュールのプロパティを変更するには、[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) コマンドレットを使用します。Azure ポータルの Runbook 用には、[Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) コマンドレットを使用します。スケジュールを無効にするには、**IsEnabled** パラメーターに **false** を指定します。

次のサンプル コマンドは、Azure サービス管理コマンドレットでスケジュールを無効にする方法を示しています。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

次のサンプル コマンドは、Azure Resource Manager コマンドレットで Runbook のスケジュールを無効にする方法を示しています。

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
	Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## 次のステップ

- スケジュールの操作に関する詳細については、「[Azure Automation のスケジュール資産](http://msdn.microsoft.com/library/azure/dn940016.aspx)」を参照してください。
- Azure Automation の Runbook の使用を開始するには、「[Azure Automation での Runbook の開始](automation-starting-a-runbook.md)」を参照してください。

<!---HONumber=AcomDC_0713_2016-->