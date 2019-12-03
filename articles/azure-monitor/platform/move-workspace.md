---
title: Azure Monitor で Log Analytics ワークスペースを移動する | Microsoft Docs
description: Log Analytics ワークスペースを別のサブスクリプションまたはリソース グループに移動する方法について説明します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093237"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Log Analytics ワークスペースを別のサブスクリプションまたはリソース グループに移動する

この記事では、Log Analytics ワークスペースを同じリージョン内の別のリソース グループまたはサブスクリプションに移動する手順について説明します。 Azure portal、PowerShell、Azure CLI、または REST API を使用した Azure リソースの移動の詳細についは、 「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/resource-group-move-resources.md)」を参照してください。 

> [!IMPORTANT]
> ワークスペースを別のリージョンに移動することはできません。

## <a name="verify-active-directory-tenant"></a>Active Directory テナントを確認する
ワークスペースの移動元と移動先のサブスクリプションは、同じ Azure Active Directory テナント内に存在している必要があります。 Azure PowerShell を使用して、両方のサブスクリプションのテナント ID が同じであることを確認します。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>ソリューションを削除する
ワークスペースにインストールされているマネージド ソリューションは、Log Analytics ワークスペースの移動操作によって移動されます。 ただし、ワークスペースから Automation アカウントへのリンクは削除しなければならないため、そのリンクに依存するソリューションは削除する必要があります。

削除する必要があるソリューションには、次のものがあります。 

- 更新管理
- 変更の追跡
- 勤務時間外に VM を起動/停止する


### <a name="azure-portal"></a>Azure ポータル
Azure portal を使用してソリューションを削除するには、次の手順を実行してください。

1. ソリューションがインストールされているリソース グループのメニューを開きます。
2. 削除するソリューションを選択します。
3. **[リソースの削除]** をクリックし、 **[削除]** をクリックして、削除されるリソースを確認します。

![ソリューションを削除する](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

PowerShell を使用してソリューションを削除するには、次の例に示すように、[Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) コマンドレットを使用します。

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>アラート ルール を削除する
**[Start/Stop VMs]** (VM の開始/停止) ソリューションの場合、ソリューションによって作成されたアラート ルールを削除する必要もあります。 これらのルールを削除するには、Azure portal で次の手順を使用します。

1. **[監視]** メニューを開き、 **[アラート]** を選択します。
2. **[アラート ルールの管理]** をクリックします。
3. 次の 3 つのアラート ルールを選択し、 **[削除]** をクリックします。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![規則の削除](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Automation アカウントのリンクを解除する
Azure portal を使用してワークスペースから Automation アカウントのリンクを解除するには、次の手順に従います。

1. **[Automation アカウント]** メニューを開き、削除するアカウントを選択します。
2. メニューの **[関連リソース]** セクションで、 **[Linked workspace]\(リンクされたワークスペース\)** を選択します。 
3. **[ワークスペースのリンクを解除]** をクリックして、Automation アカウントからワークスペースのリンクを解除します。

    ![ワークスペースのリンクの解除](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>ワークスペースを移動する

### <a name="azure-portal"></a>Azure ポータル
Azure portal を使用してワークスペースを移動するには、次の手順を実行してください。

1. **[Log Analytics ワークスペース]** メニューを開き、ワークスペースを選択します。
2. **[概要]** ページで、 **[リソース グループ]** または **[サブスクリプション]** の横の **[変更]** をクリックします。
3. 新しいページが開き、ワークスペースに関連するリソースの一覧が表示されます。 ワークスペースと同じ宛先サブスクリプションとリソース グループに移動するリソースを選択します。 
4. 移動先の **[サブスクリプション]** と **[リソース グループ]** を選択します。 同じサブスクリプション内の別のリソース グループにワークスペースを移動する場合は、 **[サブスクリプション]** オプションは表示されません。
5. **[OK]** をクリックして、ワークスペースと選択したリソースを移動します。

    ![ポータル](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
PowerShell を使用してワークスペースを移動するには、次の例のように [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) を使用します。

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> 移動操作後、削除されたソリューションと Automation アカウントのリンクを再構成して、ワークスペースを前の状態に戻す必要があります。


## <a name="next-steps"></a>次の手順
- 移動をサポートしているリソースのリストは、「[Move operation support for resources](../../azure-resource-manager/move-support-resources.md)」(リソースの移動操作のサポート) を参照してください。
