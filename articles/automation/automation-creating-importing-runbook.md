<properties
	pageTitle="Azure Automation での Runbook の作成またはインポート"
	description="この記事では、Azure Automation で新しい Runbook を作成する方法、またはファイルから Runbook をインポートする方法について説明します。"
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
	ms.date="02/29/2016"
	ms.author="magoedte;bwren" />

# Azure Automation での Runbook の作成またはインポート

[新しい Runbook を作成する](#creating-a-new-runbook)ことによって、または既存の Runbook をファイルまたは [Runbook ギャラリー](automation-runbook-gallery.md)からインポートすることによって、Azure Automation に Runbook を追加できます。この記事では、Runbook を作成またはファイルからインポートする方法について説明します。コミュニティ Runbook およびモジュールへのアクセスに関する詳細については、「[Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](automation-runbook-gallery.md)」を参照してください。

## 新しい Runbook の作成

Azure ポータルまたは Windows PowerShell のいずれかを使用して、Azure Automation で新しい Runbook を作成できます。Runbook を作成した後は、「[Windows PowerShell ワークフローについて](automation-powershell-workflow.md)」および「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」の情報を使用して Runbook を編集できます。

### Azure クラシック ポータルで新しい Azure Automation Runbook を作成するには

Azure ポータルで使用できるのは [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) だけです。

1. Azure クラシック ポータルで、**[新規]**、**[App Services]**、**[Automation]**、**[Runbook]**、**[簡易作成]** の順にクリックします。
2. 必要な情報を入力し、**[作成]** をクリックします。Runbook 名は、先頭を英字にする必要があり、英字、数字、アンダースコア、およびダッシュを使用できます。
3. ここで Runbook を編集する場合は、**[Runbook の編集]** をクリックします。それ以外の場合は、**[OK]** をクリックします。
4. 新しい Runbook が **[Runbook]** タブに表示されます。


### Azure ポータルで新しい Azure Automation Runbook を作成するには

1. Azure ポータルで、Automation アカウントを開きます。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **[Runbook の追加]** ボタンをクリックし、次に **[新しい Runbook の作成]** をクリックします。
2. Runbook の **[名前]** を入力し、[[種類]](automation-runbook-types.md) を選択します。Runbook 名は、先頭を英字にする必要があり、英字、数字、アンダースコア、およびダッシュを使用できます。
3. **[作成]** をクリックして Runbook を作成し、エディターを開きます。


### Windows PowerShell で新しい Azure Automation Runbook を作成するには

[New-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690272.aspx) コマンドレットを使用して、空の [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) を作成できます。**Name** パラメーターを指定して後で編集できる空の Runbook を作成することも、**Path** パラメーターを指定してスクリプト ファイルをインポートすることもできます。

次に示すサンプル コマンドでは、新しい空の Runbook を作成しています。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## ファイルから Azure Automation への Runbook のインポート

PowerShell スクリプトまたは PowerShell ワークフロー (拡張子 .ps1) をインポートして、またはエクスポートされたグラフィカル Runbook (.graphrunbook) をインポートして、Azure Automation で新しい Runbook を作成できます。以下の点を考慮して、インポートから作成される [Runbook の種類](automation-runbook-types.md)を指定する必要があります。

- .graphrunbook ファイルは新しい[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) にのみインポートでき、グラフィカル Runbook は .graphrunbook ファイルからのみ作成できます。
- PowerShell ワークフローを含む .ps1 ファイルは、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) にのみインポートできます。ファイルに複数の PowerShell ワークフローが含まれている場合、インポートは失敗します。各ワークフローを専用のファイルに保存し、それぞれを個別にインポートする必要があります。
- ワークフローが含まれていない .ps1 ファイルは、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) または [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) のどちらにもインポートできます。PowerShell ワークフロー Runbook にインポートされた場合は、ワークフローに変換されて、行われた変更を示すコメントが Runbook に追加されます。

### Azure クラシック ポータルでファイルから Runbook をインポートするには
次の手順を使用して、スクリプト ファイルを Azure Automation にインポートできます。このポータルを使用して PowerShell ワークフロー Runbook にインポートできるのは .ps1 ファイルだけであることに注意してください。他の種類の場合は、Azure ポータルを使用する必要があります。

1. Microsoft Azure 管理ポータルで、**[Automation]** を選択し、Automation アカウントを選択します。
2. **[インポート]** をクリックします。
3. **[ファイルの参照]** をクリックし、インポートするスクリプト ファイルを選択します。
4. ここで Runbook を編集する場合は、**[Runbook の編集]** をクリックします。それ以外の場合は、[OK] をクリックします。
5. 新しい Runbook が、Automation アカウントの **[Runbook]** タブに表示されます。
6. Runbook を実行するには、先に [Runbook を発行する](#publishing-a-runbook)必要があります。


### Azure ポータルでファイルから Runbook をインポートするには
次の手順を使用して、スクリプト ファイルを Azure Automation にインポートできます。このポータルを使用して PowerShell ワークフロー Runbook にインポートできるのは .ps1 ファイルだけであることに注意してください。

1. Azure ポータルで、Automation アカウントを開きます。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **[Runbook の追加]** ボタンをクリックし、次に **[インポート]** をクリックします。
4. **[Runbook ファイル]** をクリックし、インポートするファイルを選択します。
2. **[名前]** フィールドが有効になっている場合は、名前を変更できます。Runbook 名は、先頭を英字にする必要があり、英字、数字、アンダースコア、およびダッシュを使用できます。
3. 上記の制限を考慮して、[Runbook の種類](automation-runbook-types.md)を選択します。
3. 新しい Runbook が、Automation アカウントの Runbook の一覧に表示されます。
4. Runbook を実行するには、先に [Runbook を発行する](#publishing-a-runbook)必要があります。

### Windows PowerShell でスクリプト ファイルから Runbook をインポートするには

[Set-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690267.aspx) コマンドレットを使用して、スクリプト ファイルを既存の Runbook のドラフト バージョンにインポートできます。スクリプト ファイルには、Windows PowerShell ワークフローが 1 つだけ含まれている必要があります。Runbook にドラフト バージョンが既にある場合、Overwrite パラメーターを使用しないと、インポートは失敗します。Runbook をインポートした後は、[Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) で発行できます。

次のサンプル コマンドでは、既存の Runbook にスクリプト ファイルをインポートして発行する方法を示します。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition –AutomationAccountName $automationAccountName –Name $runbookName –Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName


## Runbook の発行

新しい Runbook を作成またはインポートしたときは、実行する前に発行する必要があります。Azure Automation の各 Runbook には、ドラフト バージョンと発行バージョンがあります。実行できるのは発行バージョンのみで、編集できるのはドラフト バージョンのみです。ドラフト バージョンを変更しても発行バージョンに影響はありません。ドラフト バージョンを使用できるようにする必要がある場合は、それを発行します。これにより、発行バージョンがドラフト バージョンで上書きされます。

## Azure クラシック ポータルを使用して Runbook を発行するには

1. Azure クラシック ポータルで Runbook を開きます。
1. 画面上部の **[作成者]** をクリックします。
1. 画面下部の **[発行]** をクリックし、確認メッセージに **[はい]** をクリックします。

## Azure ポータルを使用して Runbook を発行するには

1. Azure ポータルで Runbook を開きます。
1. **[編集]** をクリックします。
1. **[発行]** ボタンをクリックし、確認メッセージに **[はい]** をクリックします。


## Windows PowerShell を使用して Runbook を発行するには

Windows PowerShell の [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) コマンドレットを使用して、Runbook を発行できます。次のサンプル コマンドでは、サンプルの Runbook を発行する方法を示します。

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"

	Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName



## 関連記事:

- [Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](automation-runbook-gallery.md)
- [Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md)
- [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)

<!---HONumber=AcomDC_0302_2016-->