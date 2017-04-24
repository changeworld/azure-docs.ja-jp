---
title: "Azure Automation アカウントの構成を検証する | Microsoft Docs"
description: "この記事では、Automation アカウントの構成が正しく設定されていることを確認する方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Azure Automation 実行アカウントの認証をテストする
Automation アカウントが正常に作成されたら、新しく作成または更新された Automation 実行アカウントを使用して Azure Resource Manager または Azure クラシック デプロイメントで正常に認証できることを確認する簡単なテストを実行できます。    

## <a name="automation-run-as-authentication"></a>Automation 実行アカウントの認証

1. 先ほど作成した Automation アカウントを Azure Portal で開きます。  
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **AzureAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。  Runbook を開始することを確認するプロンプトが表示されます。
4. [Runbook ジョブ](automation-runbook-execution.md) が作成されると、[ジョブ] ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。  
5. 最初のジョブの状態は " *キュー登録済み* " であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。 その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  
6. Runbook ジョブが完了すると、状態は **[完了]** と表示されます。<br> ![Security Principal Runbook Test](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Runbook の詳細な結果を表示するには、 **[出力]** タイルをクリックします。
8. **[出力]** ブレードに、正常に認証され、リソース グループ内で使用可能なすべてのリソースの一覧が返されたことが示されます。
9. **[出力]** ブレードを閉じて、**[ジョブの概要]** ブレードに戻ります。
10. **[ジョブの概要]** と、対応する **[AzureAutomationTutorialScript]** Runbook ブレードを閉じます。

## <a name="classic-run-as-authentication"></a>クラシック実行認証
クラシック デプロイメント モデルのリソースを管理する場合は、次の手順を実行して、新しいクラシック実行アカウントを使用して正しく認証できることを確認します。     

1. 先ほど作成した Automation アカウントを Azure Portal で開きます。  
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **AzureClassicAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。  Runbook を開始することを確認するプロンプトが表示されます。
4. [Runbook ジョブ](automation-runbook-execution.md) が作成されると、[ジョブ] ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。  
5. 最初のジョブの状態は " *キュー登録済み* " であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。 その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  
6. Runbook ジョブが完了すると、状態は **[完了]** と表示されます。<br><br> ![Security Principal Runbook Test](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Runbook の詳細な結果を表示するには、 **[出力]** タイルをクリックします。
8. **[出力]** ブレードに、正常に認証され、サブスクリプション内のすべてのクラシック VM の一覧が返されたことが示されます。
9. **[出力]** ブレードを閉じて、**[ジョブの概要]** ブレードに戻ります。
10. **[ジョブの概要]** と、対応する **[AzureClassicAutomationTutorialScript]** Runbook ブレードを閉じます。

## <a name="next-steps"></a>次のステップ
* PowerShell Runbook の使用を開始するには、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」を参照してください。
* グラフィカル作成の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。

