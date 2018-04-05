---
title: クイック スタート - PowerShell を使用してポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する | Microsoft Docs
description: このクイック スタートでは、PowerShell を使用して Azure Policy の割り当てを作成し、準拠していないリソースを特定します。
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 3/14/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 45c5ccd0f891a5592eee7400de108c5097f75286
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>クイック スタート: Azure RM PowerShell モジュールを使用してポリシーの割り当てを作成し、準拠していないリソースを特定する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 このクイックスタートでは、ポリシーの割り当てを作成して、管理ディスクを使用していない仮想マシンを特定します。 完了すると、ポリシーの割り当てに "*準拠していない*" 仮想マシンが特定されます。

AzureRM PowerShell モジュールは、コマンド ラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、AzureRM を使用してポリシーの割り当てを作成する方法について説明します。 このポリシーは、準拠していないリソースを Azure 環境内から特定するものです。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

- 開始する前に、最新バージョンの PowerShell がインストールされていることを確認してください。 詳細については、[Azure PowerShell の概要](/powershell/azureps-cmdlets-docs)に関するページを参照してください。
- AzureRM PowerShell モジュールを最新バージョンに更新します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイック スタートでは、ポリシー割り当てを作成し、"*管理ディスクのない仮想マシンを監査*" 定義を割り当てます。 このポリシー定義で、ポリシー定義に設定されている条件に準拠していないリソースを特定します。

ポリシーの割り当てを新たに作成するには、次のコマンドを実行します。

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"

$definition = Get-AzureRmPolicyDefinition -Name "Audit Virtual Machines without Managed Disks"

New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}

```

上記のコマンドでは次の情報を使用します。

- **Name** - ポリシーの割り当てに使用する表示名。 このケースでは、"*Audit Virtual Machines without Managed Disks Assignment*" を使用します。
- **Definition** - 割り当ての作成に使用するポリシー定義。 ここでは、"*Audit Virtual Machines without Managed Disks*" というポリシー定義です
- **Scope** - スコープによって、ポリシーの割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。 &lt;scope&gt; は、実際のリソース グループの名前に置き換えてください。
- **Sku** - このコマンドでは、Standard レベルのポリシーの割り当てが作成されます。 Standard レベルでは、大規模な管理、コンプライアンス評価、修復が利用できます。 価格レベルの詳細については、「[Azure Policy の価格](https://azure.microsoft.com/pricing/details/azure-policy)」を参照してください。


以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

以下、作成したポリシーの割り当てに準拠していないリソースを特定する方法について説明します。 次のコマンドを実行します。

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

ポリシー割り当て ID の詳細については、「[Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment)」を参照してください。

続けて、次のコマンドを実行して、非準拠リソースの ID を取得します。該当するリソース ID が JSON ファイルに出力されます。

```powershell
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```
次のような結果が返されます。


```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}
```

この結果は、普段 Azure Portal ビューの **[準拠していないリソース]** に一覧表示される内容に相当します。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

以降、この一連のガイドは、このクイックスタートに基づいて執筆しています。 続けて残りのチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 続行する予定がない場合は、次のコマンドを実行して、作成した割り当てを削除してください。

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

ポリシーの割り当てについてさらに詳しく学び、**今後**作成されるリソースが準拠していることを確認するには、次に進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./create-manage-policy.md)
