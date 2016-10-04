<properties
    pageTitle="リソース グループの削除の自動化 | Microsoft Azure"
    description="サブスクリプション内のすべてのリソース グループを削除する Runbook が含まれた、PowerShell ワークフロー バージョンの Azure Automation のシナリオ。"
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
	/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>

# Azure Automation のシナリオ - リソース グループの削除の自動化

多くのユーザーは、複数のリソース グループを作成します。その中には、運用アプリケーションの管理専用に使用されるものもあれば、開発、テスト、ステージング環境に使用されるものもあります。これらのリソースのデプロイを自動化することと、ワンクリックでリソース グループを削除する機能は、まったくの別物です。その実現のために Automation を使用することは、このような一般的な管理タスクを効率化するうえで最適なユース ケースであり、良い機会です。これはまた、MSDN や Microsoft Partner Network Cloud Essentials プログラムなどのメンバー プランを通じて使用制限のある Azure サブスクリプションを使っている場合に役立ちます。

このシナリオは PowerShell Runbook に基づいており、指定した 1 つ以上のリソース グループをサブスクリプションから削除するために使用されます。Runbook では、実際に実行する前のテストがサポートされており、既定で有効になっています。そのため、この手順を完了する準備が整う前に、誤ってリソース グループを削除してしまうことはありません。

## シナリオの取得

このシナリオは、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript)からダウンロードできる PowerShell Runbook で構成されています。また、この Runbook は Azure Portal の [Runbook ギャラリー](automation-runbook-gallery.md)から直接インポートすることもできます。<br><br>

Runbook | Description|
----------|------------|
Remove-ResourceGroup | 1 つ以上の Azure リソース グループとそのリソースをサブスクリプションから削除します。  
<br>この Runbook では、次の入力パラメーターを定義します。

パラメーター | Description|
----------|------------|
NameFilter (必須) | 名前フィルターを指定して、削除するリソース グループを限定できます。コンマ区切りのリストを使用すれば、複数の値を渡すことができます。<br>フィルターでは大文字と小文字が区別されず、文字列を含むすべてのリソース グループが一致します。|
PreviewMode (省略可能) | Runbook を実行し、削除対象のリソース グループを単に表示します。<br>Runbook に渡された 1 つ以上のリソース グループを誤って削除しないように、既定値は **true** になっています。  

## このシナリオのインストールと構成

### 前提条件

この Runbook は、[Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)を使用して認証します。

### Runbook をインストールして発行する

Runbook をダウンロードしたら、[Runbook のインポートの手順](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation)を使用してインポートすることができます。Runbook が Automation アカウントに正常にインポートされたら、それを発行します。


## Runbook の使用

以下の手順では、この Runbook の実行について説明していきます。これらの手順は、Runbook のしくみを理解するのに役立ちます。この例では、Runbook のテストのみを行い、リソース グループは実際には削除しません。

1. Azure ポータルで Automation アカウントを開き、**[Runbook]** タイルをクリックします。
2. **Remove-ResourceGroup** Runbook を選択し、**[開始]** をクリックします。
3. Runbook を開始すると、**[Runbook の開始]** ブレードが開きます。ここで、次のパラメーターの値を構成できます。サブスクリプション内にある、テストしたい 1 つ以上のリソース グループの名前を入力します。誤って削除しても悪影響のないものを選んでください。<br>![Remove-ResouceGroup Parameters](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] 選択したリソース グループが削除されないように、**[Previewmode]** オプションが **true** に設定されていることを確認します。この Runbook では、Runbook を実行中の Automation アカウントが含まれているリソース グループは削除されないことに**注意してください**。

4. すべてのパラメーター値を構成したら、**[OK]** をクリックします。Runbook が実行されるよう、キューに配置されます。

Azure Portal で **Remove-ResourceGroup** Runbook ジョブの詳細を表示するには、Runbook の **[ジョブ]** タイルを選択します。ジョブの概要として、入力パラメーターと出力ストリーム、さらにジョブに関する全般情報が表示されます。例外が発生した場合は、その情報も表示されます。<br>![Remove-ResourceGroup Runbook Job Status](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png)

**[ジョブの概要]** には、出力、警告、およびエラー ストリームからのメッセージが表示されます。Runbook 実行の詳細な結果を表示するには、**[出力]** タイルを選択します。<br>![Remove-ResourceGroup Runbook Output Results](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## 次のステップ

- 独自の Runbook の作成を開始するには、「[Azure Automation での Runbook の作成またはインポート](automation-creating-importing-runbook.md)」を参照してください。
- PowerShell ワークフロー Runbook の使用を開始するには、「[最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。

<!---HONumber=AcomDC_0928_2016-->