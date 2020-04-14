---
title: Azure Automation で Runbook を管理する
description: この記事では、Azure Automation で Runbook を管理する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 29ac9239b8dc87b1ed12fc8333bf5201fe8fa204
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617137"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation で Runbook を管理する

Azure Automation に Runbook を追加するには、[新しいものを作成](#creating-a-runbook)するか、あるいはファイルまたは [Runbook ギャラリー](automation-runbook-gallery.md)から[既存のものをインポート](#importing-a-runbook)します。 この記事では、Runbook を作成またはファイルからインポートする方法について説明します。 コミュニティ Runbook およびモジュールへのアクセスに関するすべての詳細については、「[Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](automation-runbook-gallery.md)」を参照してください。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="creating-a-runbook"></a>Runbook の作成

Azure ポータルまたは Windows PowerShell のいずれかを使用して、Azure Automation で新しい Runbook を作成できます。 Runbook を作成した後は、「[Windows PowerShell ワークフローについて](automation-powershell-workflow.md)」および「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」の情報を使用して Runbook を編集できます。

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook を作成する

1. Azure ポータルで、Automation アカウントを開きます。
2. ハブから、 **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
3. **[Runbook の作成]** をクリックします。
4. Runbook の名前を入力し、その [[種類]](automation-runbook-types.md) を選択します。 Runbook 名は、先頭を英字にする必要があり、英字、数字、アンダースコア、およびダッシュを使用できます。
5. **[作成]** をクリックして Runbook を作成し、エディターを開きます。

### <a name="create-a-runbook-with-powershell"></a>PowerShell で Runbook を作成する

[New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) コマンドレットを使用すれば、空の [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) を作成できます。 `Type` パラメーターを使用して、`New-AzAutomationRunbook` に対して定義されている Runbook の種類の 1 つを指定します。

次の例は、新しい空の Runbook を作成する方法を示しています。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Runbook のインポート

PowerShell スクリプトまたは PowerShell ワークフロー ( **.ps1**)、エクスポートされたグラフィック Runbook ( **.graphrunbook**)、または Python2 スクリプト ( **.py**) をインポートすることで、Azure Automation で新しい Runbook を作成できます。  以下の点を考慮して、インポート中に作成される [Runbook の種類](automation-runbook-types.md) を指定する必要があります。

* ワークフローが含まれていない **.ps1** ファイルは、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) または [PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) のどちらにもインポートすることができます。 それを PowerShell ワークフロー Runbook にインポートすると、ワークフローに変換されます。 この場合、Runbook には、加えられた変更を説明するコメントが含められます。

* PowerShell ワークフローを含む **.ps1** ファイルは、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) にのみインポートできます。 ファイルに複数の PowerShell ワークフローが含まれている場合、インポートは失敗します。 各ワークフローを専用のファイルに保存し、それぞれを個別にインポートする必要があります。

* PowerShell ワークフローを含む **.ps1** ファイルは PowerShell スクリプト エンジンでは認識できないため、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) にインポートしないでください。

* **.graphrunbook** ファイルは、新しい[グラフィック Runbook](automation-runbook-types.md#graphical-runbooks) にのみインポートできます。 グラフィック Runbook は **.graphrunbook** ファイルからのみ作成できることに注意してださい。

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Azure portal でファイルから Runbook をインポートする

次の手順を使用して、スクリプト ファイルを Azure Automation にインポートできます。

> [!NOTE]
> このポータルを使用して PowerShell ワークフロー Runbook にインポートできるのは **.ps1** ファイルだけです。

1. Azure ポータルで、Automation アカウントを開きます。
2. ハブから、 **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
3. **[Runbook のインポート]** をクリックします。
4. **[Runbook ファイル]** をクリックし、インポートするファイルを選択します。
5. **[名前]** フィールドが有効になっている場合は、必要に応じて Runbook 名を変更することができます。 名前は文字で始める必要があり、文字、数字、アンダースコア、およびダッシュを含めることができます。
6. [Runbook の種類](automation-runbook-types.md) は自動的に選択されますが、適切な制限を考慮して変更することもできます。
7. **Create** をクリックしてください。 Automation アカウントの Runbook の一覧に新しい Runbook が表示されます。
8. Runbook を実行するには、先に [Runbook を発行する](#publishing-a-runbook) 必要があります。

> [!NOTE]
> グラフィック Runbook またはグラフィック PowerShell ワークフロー Runbook をインポートすると、その他の種類に変換できるようになります。 ただし、これらのグラフィック Runbook の 1 つは、テキスト形式の Runbook に変換することができません。

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Windows PowerShell を使用してスクリプト ファイルから Runbook をインポートする

PowerShell ワークフロー Runbook のドラフトとしてスクリプト ファイルをインポートするには、[Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) コマンドレットを使用します。 Runbook が既に存在する場合、コマンドレットで `Force` パラメーターを使用しないと、インポートは失敗します。

次の例では、スクリプト ファイルを Runbook にインポートする方法を示します。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="testing-a-runbook"></a>Runbook のテスト

Runbook のテスト時には [ドラフト バージョン](#publishing-a-runbook) が実行され、そのバージョンで行われるすべてのアクションが完了します。 ジョブ履歴は作成されませんが、[テスト出力] ウィンドウに[出力](automation-runbook-output-and-messages.md#output-stream)および[警告とエラー](automation-runbook-output-and-messages.md#message-streams)ストリームが表示されます。 [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) 変数が `Continue` に設定されている場合のみ、[詳細ストリーム](automation-runbook-output-and-messages.md#message-streams)に対するメッセージが [出力] ウィンドウに表示されます。

ドラフト バージョンの実行中でも、Runbook の実行は通常どおり行われて、環境内のリソースに対するすべてのアクションが実行されます。 このため、Runbook をテストするのは非運用環境のリソースのみにしてください。

[Runbook の各種類](automation-runbook-types.md)をテストする手順はいずれも同じです。 Azure portal のテキスト エディターとグラフィカル エディターではテスト方法に違いはありません。

1. [テキスト エディター](automation-edit-textual-runbook.md)または[グラフィカル エディター](automation-graphical-authoring-intro.md)のどちらかで、ドラフト バージョンの Runbook を開きます。
1. **[テスト]** ボタンをクリックして [テスト] ページを開きます。
1. Runbook にパラメーターがある場合は左側のウィンドウに表示され、そこでテストに使用する値を指定することができます。
1. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) に対してテストを実施する場合は、 **[実行設定]** を **[ハイブリッド worker]** に変更してターゲット グループの名前を選択します。  それ以外の場合は、既定の **[Azure]** をそのまま使用してクラウドでテストを実行します。
1. **[開始]** ボタンをクリックして、テストを開始します。
1. [出力] ウィンドウの下にあるボタンを使用すれば、テスト中に、[PowerShell ワークフロー](automation-runbook-types.md#powershell-workflow-runbooks)または[グラフィック](automation-runbook-types.md#graphical-runbooks) Runbook を停止したり中断したりできます。 Runbook は、中断される場合は中断前に現在のアクティビティを完了します。 Runbook を中断した後で、停止または再開できます。
1. [出力] ウィンドウで Runbook からの出力を確認します。

## <a name="publishing-a-runbook"></a>Runbook の発行

新しい Runbook を作成またはインポートしたときは、実行する前に発行する必要があります。 Azure Automation の各 Runbook には、ドラフト バージョンと発行バージョンがあります。 実行できるのは発行バージョンのみで、編集できるのはドラフト バージョンのみです。 ドラフト バージョンを変更しても発行バージョンに影響はありません。 ドラフト バージョンを使用できるようにする必要がある場合は、それを発行します。これにより、現在発行されているバージョンがドラフト バージョンで上書きされます。

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure portal で Runbook を発行する

1. Azure ポータルで Runbook を開きます。
2. **[編集]** をクリックします。
3. **[発行]** をクリックしてから、確認メッセージに対して **[はい]** クリックします。

### <a name="publish-a-runbook-using-powershell"></a>PowerShell を使用して Runbook を発行する

Windows PowerShell を使用して Runbook を発行するには、[Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) コマンドレットを使用します。 次の例では、サンプルの Runbook を発行する方法を示します。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Azure portal での Runbook のスケジュール設定

Runbook が発行済みであるときに、操作のスケジュールを設定できます。

1. Azure ポータルで Runbook を開きます。
2. **[リソース]** の下の **[スケジュール]** を選択します。
3. **[スケジュールの追加]** を選択します。
4. [Runbook のスケジュール] ウィンドウで、 **[スケジュールを Runbook にリンクします]** を選択します。
5. [スケジュール] ウィンドウで、 **[新しいスケジュールを作成します]** をクリックします。
6. [新しいスケジュール] ウィンドウで、名前、説明、およびその他のパラメーターを入力します。 
7. スケジュールを作成したら、それを強調表示し、 **[OK]** をクリックします。 これで、Runbook にリンクされるようになります。
8. メールボックスの電子メールで、Runbook の状態通知を探します。

## <a name="next-steps"></a>次のステップ

* Runbook と PowerShell モジュール ギャラリーの利点については、「[Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](automation-runbook-gallery.md)」を参照してください
* テキスト エディターを使用した PowerShell および PowerShell ワークフロー Runbook の編集の詳細については、「[Azure Automation でのテキスト形式の Runbook の編集](automation-edit-textual-runbook.md)」を参照してください。
* グラフィック Runbook 作成の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。
