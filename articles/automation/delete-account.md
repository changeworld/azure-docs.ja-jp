---
title: Azure Automation アカウントを削除する
description: この記事では、さまざまな構成シナリオで Automation アカウントを削除する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105734918"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Azure Automation アカウントを削除する方法

IT またはビジネス プロセスを自動化するために Azure Automation アカウントを有効にした後や、Azure および Azure 以外のマシンの操作管理をサポートする他の機能 (Update Management など) を有効にした後に、Automation アカウントの使用の停止を決定する場合があります。 Azure Monitor Log Analytics ワークスペースとの統合に依存する機能を有効にした場合は、この操作を完了するために必要な手順が多くなります。

Automation アカウントを削除するには、サポートされているデプロイ モデルに基づいて、次のいずれかの方法を使用します。

* Automation アカウントが含まれているリソース グループを削除します。
* 次の場合には、Automation アカウントとリンクされた Azure Monitor Log Analytics ワークスペースが含まれているリソースグループを削除します。

    * アカウントとワークスペースは、Update Management、変更履歴とインベントリ、Start/Stop VMs during off-hours のサポート専用である。
    * アカウントはプロセスの自動化のプロセス専用であり、Runbook ジョブの状態とジョブ ストリームを送信するワークスペースと統合されている。

* Log Analytics ワークスペースを Automation アカウントからリンク解除して、Automation アカウントを削除します。
* リンクされたワークスペースから機能を削除し、ワークスペースからアカウントをリンク解除した後に、Automation アカウントを削除します。

この記事では、Azure portal、PowerShell、Azure CLI、または REST API を使用して Automation アカウントを完全に削除する方法について説明します。

## <a name="delete-the-dedicated-resource-group"></a>専用のリソース グループを削除する

Automation アカウントに加えて、Log Analytics ワークスペースも (そのアカウント専用の同じリソース グループに作成されていて、そのアカウントにリンクされている場合に) 削除するには、「[Azure Resource Manager のリソース グループとリソースの削除](../azure-resource-manager/management/delete-resource-group.md)」の記事に記載されている手順に従ってください。

## <a name="delete-a-standalone-automation-account"></a>スタンドアロン Automation アカウントを削除する

Automation アカウントが Log Analytics ワークスペースにリンクされていない場合は、次の手順を実行して削除します。

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [https://portal.azure.com](https://portal.azure.com) で Azure にサインインします。

2. Azure portal で、 **[Automation アカウント]** に移動します。

3. Automation アカウントを開き、メニューから **[削除]** を選択します。

情報が検証され、アカウントが削除されている間、メニューから **[通知]** を選択してその進行状況を追跡できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このコマンドでは、Automation アカウントを削除する際に、確認を求めるメッセージが表示されません。

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>ワークスペースにリンクされているスタンドアロン Automation アカウントを削除する

Automation アカウントがジョブ ストリームとジョブ ログを収集するための Log Analytics ワークスペースにリンクされている場合は、次の手順を実行してアカウントを削除します。

Automation アカウントから Log Analytics ワークスペースのリンクを解除するには、2 つのオプションがあります。 このプロセスは、Automation アカウントまたはリンクされたワークスペースから実行できます。

Automation アカウントからリンクを解除するには、次の手順を実行します。

1. Azure portal で、 **[Automation アカウント]** に移動します。

2. 自分の Automation アカウントを開いて、左側にある **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. **[ワークスペースのリンクを解除]** ページで **[ワークスペースのリンクを解除]** を選択して、プロンプトに応答します。

   ![[ワークスペースのリンクを解除] ページ](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics ワークスペースとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

ワークスペースからリンクを解除するには、次の手順を実行します。

1. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。

2. ワークスペースから、 **[関連リソース]** の **[Automation アカウント]** を選択します。

3. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択し、プロンプトに応答します。

Automation アカウントとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

Automation アカウントをワークスペースから正常にリンク解除したら、[スタンドアロン Automation アカウント](#delete-a-standalone-automation-account)に関するセクションの手順を実行してアカウントを削除します。

## <a name="delete-a-shared-capability-automation-account"></a>機能を共有する Automation アカウントを削除する

Update Management、変更履歴とインベントリ、Start/Stop VMs during off-hours をサポートしている Log Analytics ワークスペースにリンクされた Automation アカウントを削除するには、次の手順を実行します。

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>手順 1. リンクされたワークスペースからソリューションを削除する

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [https://portal.azure.com](https://portal.azure.com) で Azure にサインインします。

2. 自分の Automation アカウントに移動して、 **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. **[ワークスペースに移動]** を選択します。

4. **[全般]** の下にある **[ソリューション]** をクリックします。

5. [ソリューション] ページで、アカウントにデプロイされた機能に基づいて、次のいずれかを選択します。

    * Start/Stop VMs during off-hours については、**Start-Stop-VM[ワークスペース名]** を選択します。
    * Udate Management については、 **[Updates(workspace name)]\(更新 (ワークスペース名)\)** を選択します。
    * 変更履歴とインベントリについては、 **[ChangeTracking(ワークスペース名)]** を選択します。

6. **[ソリューション]** ページで、メニューから **[削除]** を選択します。 上記の一覧のうち複数の機能が Automation アカウントおよびリンクされたワークスペースにデプロイされている場合は、それぞれを選択して削除してから先へ進む必要があります。

7. 情報が検証され、機能が削除されている間、メニューから **[通知]** を選択してその進行状況を追跡できます。 削除プロセスの後、[ソリューション] ページに戻ります。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

インストールされているソリューションを Azure PowerShell で削除するには、[Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) コマンドレットを使用します。

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>手順 2. Automation アカウントからワークスペースのリンクを解除する

Automation アカウントから Log Analytics ワークスペースのリンクを解除するには、2 つのオプションがあります。 このプロセスは、Automation アカウントまたはリンクされたワークスペースから実行できます。

Automation アカウントからリンクを解除するには、次の手順を実行します。

1. Azure portal で、 **[Automation アカウント]** に移動します。

2. 自分の Automation アカウントを開いて、左側にある **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. **[ワークスペースのリンクを解除]** ページで **[ワークスペースのリンクを解除]** を選択して、プロンプトに応答します。

   ![[ワークスペースのリンクを解除] ページ](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics ワークスペースとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

ワークスペースからリンクを解除するには、次の手順を実行します。

1. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。

2. ワークスペースから、 **[関連リソース]** の **[Automation アカウント]** を選択します。

3. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択し、プロンプトに応答します。

Automation アカウントとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

### <a name="step-3-delete-automation-account"></a>手順 3. Automation アカウントを削除する

Automation アカウントをワークスペースから正常にリンク解除したら、[スタンドアロン Automation アカウント](#delete-a-standalone-automation-account)に関するセクションの手順を実行してアカウントを削除します。

## <a name="next-steps"></a>次のステップ

Azure portal からの Automation アカウントの作成については、「[スタンドアロン Azure Automation アカウントを作成する](automation-create-standalone-account.md)」を参照してください。 テンプレートを使用してアカウントを作成したい場合は、「[Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](quickstart-create-automation-account-template.md)」を参照してください。