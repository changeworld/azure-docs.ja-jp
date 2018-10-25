---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5914789675edba0d56e6899728fc2c3c7768374a
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312472"
---
リソース グループに 2 つのタグを追加するには、[Set-AzureRmResourceGroup](/powershell/module/azurerm.resources/set-azurermresourcegroup) コマンドを使用します。

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

3 つ目のタグを追加するとします。 リソースまたはリソース グループにタグを適用するたびに、そのリソースまたはリソース グループの既存のタグが上書きされます。 既存のタグを失うことなく新しいタグを追加するには、既存のタグを取得し、新しいタグを追加して、タグのコレクションを再適用する必要があります。

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzureRmResourceGroup -Name myResourceGroup).Tags
$tags.Add("Project", "Documentation")

# Reapply the updated set of tags 
Set-AzureRmResourceGroup -Tag $tags -Name myResourceGroup
```

リソースは、リソース グループからタグを継承しません。 現在、リソース グループには 3 つのタグがありますが、リソースにはタグがありません。 リソースにある重複しない既存のタグを保持して、リソース グループのすべてのタグをリソースに適用するには、次のスクリプトを使用します。

```azurepowershell-interactive
# Get the resource group
$group = Get-AzureRmResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        
        # If the resource has existing tags, add new ones
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }

            # Reapply the updated tags to the resource 
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

また、既存のタグを保持せずに、リソース グループのタグをリソースに適用することもできまます。

```azurepowershell-interactive
# Get the resource group
$g = Get-AzureRmResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

1 つのタグに複数の値を結合するには、JSON 文字列を使用します。

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

既存のタグを失うことなく複数の値を持つ新しいタグを追加するには、既存のタグを取得し、JSON 文字列を使用して新しいタグを追加して、タグのコレクションを再適用する必要があります。

```azurepowershell-interactive
# Get existing tags and add a new tag
$ResourceGroup = Get-AzureRmResourceGroup -Name myResourceGroup
$Tags = $ResourceGroup.Tags
$Tags.Add("CostCenter", "{`"Dept`":`"IT`",`"Environment`":`"Test`"}")

# Reapply the updated set of tags
$ResourceGroup | Set-AzureRmResourceGroup -Tag $Tags
```

すべてのタグを削除するには、空のハッシュ テーブルを渡します。

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ }
```
