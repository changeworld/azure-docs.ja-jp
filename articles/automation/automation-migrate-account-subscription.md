---
title: Automation アカウントとリソースを移行する | Microsoft Docs
description: この記事では、Azure Automation の Automation アカウントとそれに関連付けられているリソースをサブスクリプション間で移動する方法について説明します。
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/07/2016
ms.author: magoedte

---
# Automation アカウントとリソースを移行する
Azure ポータルで作成した Automation アカウントとそれに関連付けられているリソース (資産、Runbook、モジュールなど) をリソース グループ間またはサブスクリプション間で移行する操作は、Azure ポータルの[リソース移動](../resource-group-move-resources.md)機能を使用して簡単に実行できます。ただし、この操作を行う前に、[リソース移動前のチェックリスト](../resource-group-move-resources.md#Checklist-before-moving-resources)を確認した後、Automation に固有の次のリストもチェックする必要があります。

1. 移動先のサブスクリプション/リソース グループは、移動元と同じリージョン内に存在する必要があります。つまり、Automation アカウントは、リージョン間で移動することはできません。
2. リソース (Runbook やジョブなど) を移動する場合は、その操作の間、ソース グループとターゲット グループの両方がロックされます。これらのグループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。
3. 既存のサブスクリプションからリソースまたはサブスクリプション ID を参照する Runbook または変数は、移行が完了した後に更新する必要があります。

> [!NOTE]
> この機能は、クラシック オートメーション リソースの移動をサポートしていません。
> 
> 

## ポータルを使用して Automation アカウントを移動するには
1. 自分の Automation アカウントで、ブレードの上部にある **[移動]** をクリックします。<br>![[移動] オプション](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. **[リソースの移動]** ブレードに、Automation アカウントとリソース グループの両方に関連するリソースが表示されます。**[サブスクリプション]** ボックスの一覧と **[リソース グループ]** ボックスの一覧から選択するか、**[新しいリソース グループの作成]** オプションを選択して、表示されたフィールドに新しいグループ名を入力します。
3. *[リソースの移動後に新しいリソース ID を使用するにはツールとスクリプトの更新が必要であることを理解しました]* チェックボックスをオンにし、**[OK]** をクリックします。<br> ![[リソースの移動] ブレード](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>

この操作は、完了までに数分かかります。**[通知]** に、実行されている操作の状態が表示され、検証と移行の実行後に操作が完了します。

## PowerShell を使用して Automation アカウントを移動するには
既存の Automation リソースを別のリソース グループまたはサブスクリプションに移動するには、**Get-AzureRmResource** コマンドレットを使用して Automation アカウントを取得した後、**Move-AzureRmResource** コマンドレットを使用して移動を実行します。

最初の例では、Automation アカウントを新しいリソース グループに移動する方法を示します。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ``` 

上のコード例を実行すると、この操作を実行することの確認を求められます。**[はい]** をクリックしてスクリプトの続行を許可した後、移行実行中は通知を受信しません。

新しいサブスクリプションに移動する場合は、*DestinationSubscriptionId* パラメーターの値を含めます。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ``` 

前の例と同じように、移動することの確認を求められます。

## 次のステップ
* 新しいリソース グループまたはサブスクリプションへのリソースの移動については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」を参照してください。
* Azure Automation におけるロールベースのアクセス制御の詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
* サブスクリプションを管理するための PowerShell コマンドレットについては、「[Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。
* サブスクリプションを管理するためのポータル機能については、「[Azure ポータルを使用したリソース管理](../azure-portal/resource-group-portal.md)」を参照してください。

<!---HONumber=AcomDC_0713_2016-->