---
title: "Azure Automation の Hybrid Runbook Worker の削除 | Microsoft Docs"
description: "この記事では、お使いのローカル データ センターまたはクラウド 環境内のコンピューターで Runbook を実行できるようにする Azure Automation Hybrid Runbook Worker のデプロイ管理について説明します。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 602b868073da6bd1f64099c0f344c9b492abaff0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Azure Automation の Hybrid Runbook Worker の削除

Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 この記事では、オンプレミス コンピューターからハイブリッド worker を削除する手順について説明します。

## <a name="removing-hybrid-runbook-worker"></a>Hybrid Runbook Worker の削除

要件に応じて、グループから 1 つ以上の Hybrid Runbook Worker を削除したり、グループを削除することができます。  オンプレミス コンピューターから Hybrid Runbook Worker を削除するには、次の手順を実行します。

1. Azure Portal で、Automation アカウントに移動します。  
2. **[設定]** ブレードから、**[キー]** を選択し、フィールドの **[URL]** と **[プライマリ アクセス キー]** の値をメモします。  この情報は、次の手順に必要です。
3. 管理者モードで PowerShell セッションを開き、次のコマンド - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>` を実行します。  削除処理の詳細なログを取得するには、 **-Verbose** スイッチを使用します。

> [!NOTE]
> これによってコンピューターから Microsoft Monitoring Agent が削除されることはありません。Hybrid Runbook Worker ロールの機能と構成のみが削除されます。  

## <a name="remove-hybrid-worker-groups"></a>Hybrid Worker グループを削除する

グループを削除するには、まず、先ほどの手順を使用して、グループのメンバーであるすべてのコンピューターから Hybrid Runbook Worker を削除する必要があります。その後、次の手順を実行してグループを削除します。  

1. Azure ポータルで Automation アカウントを開きます。
1. **[プロセス オートメーション]** で **[ハイブリッド Worker グループ]** を選択します。 削除するグループを選択します。  特定のグループを選択すると、**[ハイブリッド Worker グループ]** プロパティ ブレードが表示されます。<br> ![Hybrid Runbook Worker グループ ブレード](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
2. 選択したグループのプロパティ ブレードで、**[削除]** をクリックします。  このアクションの確認を求めるメッセージが表示されるので、続行する場合は **[はい]** をクリックします。<br> ![グループ削除の確認ダイアログ ボックス](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。 

## <a name="next-steps"></a>次の手順

* 「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
* Windows Hybrid Runbook Worker をインストールする方法については、[Azure Automation の Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) に関する記事をご覧ください。
* Linux Hybrid Runbook Worker をインストールする方法については、[Azure Automation の Linux Hybrid Runbook Worker](automation-linux-hrw-install.md) に関する記事をご覧ください。