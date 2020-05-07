---
title: Azure Automation アカウントを別のサブスクリプションに移動する
description: この記事では、Automation アカウントを別のサブスクリプションに移動する方法について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681887"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Automation アカウントを別のサブスクリプションに移動する

Azure Automation を使用すると、一部のリソースを新しいリソース グループまたはサブスクリプションに移動することができます。 Azure portal、PowerShell、Azure CLI、または REST API を使用して、リソースを移動できます。 方法の詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

Azure Automation アカウントは、移動できるリソースの 1 つです。 この記事では、Automation アカウントを別のリソースまたはサブスクリプションに移動する方法について説明します。 Automation アカウントを移動する手順の概要は次のとおりです。

1. ソリューションを削除します。
2. ワークスペースのリンクを解除します。
3. Automation アカウントを移動します。
4. 実行アカウントを削除して再作成します。
5. ソリューションを再度有効にします。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="remove-solutions"></a>ソリューションを削除する

Automation アカウントからワークスペースのリンクを解除するには、次のソリューションをワークスペースから削除する必要があります。

- 変更履歴とインベントリ
- 更新管理
- 勤務時間外の VM の起動/停止

1. Azure portal で目的のリソース グループを探します。
2. 各ソリューションを見つけて、[リソースの削除] ページで **[削除]** をクリックします。

    ![Azure portal からソリューションを削除する](../media/move-account/delete-solutions.png)

    必要に応じて、[Remove-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) コマンドレットを使用してソリューションを削除できます。

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>Start/Stop VMs during off hours ソリューションのアラート ルールを削除します。

Start/Stop VMs during off hours ソリューションの場合、ソリューションによって作成されたアラート ルールも削除する必要があります。

1. Azure portal で、リソース グループに移動し、 **[監視]**  >  **[アラート]**  >  **[アラート ルールの管理]** を選択します。

![[アラート ルールの管理] の選択を示した [アラート] ページ](../media/move-account/alert-rules.png)

2. [ルール] ページに、そのリソース グループに構成されているアラートの一覧が表示されます。 ソリューションは、次のルールを作成します。

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. ルールを一度に 1 つずつ選択し、 **[削除]** をクリックして削除します。

    ![選択したルールの削除の確認を要求する [ルール] ページ](../media/move-account/delete-rules.png)

    > [!NOTE]
    > [ルール] ページにアラート ルールが表示されない場合は、それらを無効にしている可能性があるため、 **[状態]** フィールドを [無効] に変更して、無効化されているアラートを表示します。

4. アラート ルールが削除されたら、Start/Stop VMs during off hours ソリューションの通知のために作成したアクション グループを削除する必要があります。 Azure portal で、 **[監視]**  >  **[アラート]**  >  **[アクション グループの管理]** の順に選択します。

5. **[StartStop_VM_Notification]** を選択します。 

6. [アクション グループ] ページで、 **[削除]** を選択します。

    ![[アクション グループ] ページ](../media/move-account/delete-action-group.png)

    必要に応じて、[Remove-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) コマンドレットを使用してアクション グループを削除できます。

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>ワークスペースのリンクを解除する

次に、ワークスペースのリンクを解除することができます。

1. Azure portal で、 **[Automation アカウント]**  >  **[関連リソース]**  >  **[Linked workspace]** (リンクされたワークスペース) の順に選択します。 

2. **[ワークスペースのリンクを解除]** を選択して、Automation アカウントからワークスペースのリンクを解除します。

    ![Automation アカウントからワークスペースのリンクを解除する](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation アカウントを移動する

次に、Automation アカウントとその Runbook を移動できます。 

1. Azure portal で、Automation アカウントのリソース グループを参照します。 **[移動]**  >  **[別のサブスクリプションに移動する]** の順に選択します。

    ![[リソース グループ] ページ、別のサブスクリプションに移動します](../media/move-account/move-resources.png)

2. 移動するリソース グループ内のリソースを選択します。 Automation アカウント、Runbook、および Log Analytics ワークスペースのリソースを必ず含めてください。

## <a name="recreate-run-as-accounts"></a>実行アカウントを再作成する

[[実行アカウント]](../manage-runas-account.md) は、Azure リソースで認証するために、Azure Active Directory にサービス プリンシパルを作成します。 サブスクリプションを変更すると、Automation アカウントは既存の実行アカウントを使用しなくなります。 実行アカウントを再作成するには、次にようにします。

1. 新しいサブスクリプションの Automation アカウントに移動し、 **[アカウント設定]** の下で **[実行アカウント]** を選択します。 実行アカウントが現在、不完全と表示されていることがわかります。

    ![実行アカウントが不完全です](../media/move-account/run-as-accounts.png)

2. [プロパティ] ページの **[削除]** ボタンを使用して、実行アカウントを一度に 1 つずつ削除します。 

    > [!NOTE]
    > 実行アカウントを作成または表示するアクセス許可がない場合は、次のメッセージが表示されます。`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 実行アカウントを構成するために必要なアクセス許可については、「[実行アカウントを構成するために必要なアクセス許可](../manage-runas-account.md#permissions)」を参照してください。

3. 実行アカウントを削除したら、 **[Azure 実行アカウント]** の下で **[作成]** を選択します。 

4. [Azure 実行アカウントの追加] ページで **[作成]** を選択して、実行アカウントとサービス プリンシパルを作成します。 

5. Azure クラシック実行アカウントで上記の手順を繰り返します。

## <a name="enable-solutions"></a>ソリューションの有効化

実行アカウントを再作成したら、移動の前に削除したソリューションを再び有効にする必要があります。 

1. 変更履歴とインベントリ ソリューションを有効にするには、Automation アカウントで変更履歴とインベントリを選択します。 移動した Log Analytics ワークスペースを選択し、 **[有効化]** を選択します。

2. Update Management ソリューションの手順 1 を繰り返します。

    ![移動した Automation アカウントでソリューションを再度有効にする](../media/move-account/reenable-solutions.png)

3. 既存の Log Analytics ワークスペースを接続したときに、ソリューションがオンボードされるマシンが表示されます。 Start/Stop VMs during off hours ソリューションをオンにするには、ソリューションを再デプロイする必要があります。 **[関連リソース]** の下で、 **[Start/Stop VMs]\(VM の開始/停止)**  >  **[Learn more about and enable the solution]\(ソリューションの詳細と有効化)**  >  **[作成]** の順に選択してデプロイを開始します。

4. [ソリューションの追加] ページで、Log Analytics ワークスペースと Automation アカウントを選択します。

    ![[ソリューションの追加] メニュー](../media/move-account/add-solution-vm.png)

5. 「[Azure Automation の Start/Stop VMs during off-hours ソリューション](../automation-solution-vm-management.md)」の説明にしたがってソリューションを構成します。

## <a name="verify-the-move"></a>移動を確認する

移動が完了したら、次の一覧の機能が有効になっていることを確認します。 

|機能|テスト|トラブルシューティング|
|---|---|---|
|Runbooks|Runbook が正常に実行し、Azure リソースに接続できる。|[Runbook のトラブルシューティング](../troubleshoot/runbooks.md)
|ソース管理|ソース管理リポジトリで手動同期を実行できる。|[ソース管理の統合](../source-control-integration.md)|
|変更履歴とインベントリ|マシンの現在のインベントリ データが表示されることを確認する。|[Change Tracking とインベントリのトラブルシューティング](../troubleshoot/change-tracking.md)|
|更新管理|マシンが表示されており、正常な状態であることを確認する。</br>テスト ソフトウェア更新プログラムのデプロイを実行する。|[Update Management の問題をトラブルシューティングする](../troubleshoot/update-management.md)|
|共有リソース|[資格情報](../shared-resources/credentials.md)や[変数](../shared-resources/variables.md)などのすべての共有リソースが表示されていることを確認する。|

## <a name="next-steps"></a>次のステップ

Azure でのリソースの移動の詳細については、[Azure でのリソースの移動](../../azure-resource-manager/management/move-support-resources.md)に関するページを参照してください。
