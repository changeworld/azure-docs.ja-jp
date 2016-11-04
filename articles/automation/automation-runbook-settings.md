---
title: Runbook の設定
description: Azure Automation の Runbook の構成設定について説明し、Azure 管理ポータルと Windows PowerShell のそれぞれで設定を変更する方法について取り上げます。
services: automation
documentationcenter: ''
author: bwren
manager: stevenka
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: bwren

---
# Runbook の設定
Azure Automation 内の各 Runbook には、それぞれを識別し、ログ記録の動作を変更するのに役立つ設定が複数あります。これらの各設定を以下に示し、その後にそれらの変更方法手順を記します。

## 設定
### 名前と説明
Runbook の作成後に、名前を変更することはできません。説明は省略可能で、最大 512 文字まで可能です。

### タグ
タグを使用すると、個別の単語や語句を割り当て、Runbook を識別するのに役立ちます。たとえば、Runbook を [Runbook ギャラリー](https://msdn.microsoft.com/library/dn781422.aspx)に送信する場合、対象の Runbook が一覧表示されるカテゴリを特定するタグを指定します。コンマで区切ると 1 つの Runbook に複数のタグを指定できます。

### ログの記録
既定では、詳細レコードと進行状況レコードはジョブ履歴に書き込まれません。設定を変更し、特定の Runbook でこうしたレコードをログに記録できます。これらのレコードの詳細については、「[Runbook の出力とメッセージ](https://msdn.microsoft.com/library/dn879148.aspx)」をご覧ください。

## Runbook の設定変更
### Azure 管理ポータルで Runbook の設定を変更する
Runbook の設定変更は、Azure 管理ポータルで対象 Runbook の **[構成]** ページから実行できます。

1. Azure 管理ポータルで、**[Automation]** を選択し、次に Automation アカウントの名前をクリックします。
2. **[Runbook]** タブを選択します。
3. Runbook の名前をクリックします。
4. **[構成]** タブを選択します。

### Windows PowerShell で Runbook の設定を変更する
[Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) コマンドレットを使用して、Runbook の設定を変更できます。複数のタグを指定する場合は、配列を指定するか、コンマで区切った値を使用して 1 つの文字列をタグ パラメーターに指定できます。[Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx) を使用して、現在のタグを取得できます。

以下のサンプル コマンドは、ある Runbook のプロパティ設定方法を示しています。このサンプルでは、既存のタグに 3 つのタグを追加し、詳細レコードをログに記録するように指定します。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## 関連記事:
* [Runbook の出力とメッセージ](automation-runbook-output-and-messages.md) 
* [Runbook の作成またはインポート](https://msdn.microsoft.com/library/dn643637.aspx) 

<!---HONumber=AcomDC_0211_2016-->