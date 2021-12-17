---
title: Azure PowerShell でテンプレートをエクスポートする
description: Azure PowerShell を使用して、サブスクリプション内のリソースから Azure Resource Manager テンプレートをエクスポートします。
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 15a81b845811f870be783807cf680c724413bf6c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479161"
---
# <a name="use-azure-powershell-to-export-a-template"></a>Azure PowerShell を使用してテンプレートをエクスポートする

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

この記事では、**Azure PowerShell** を使用してテンプレートをエクスポートする方法を示します。 その他のオプションについては、以下を参照してください。

* [Azure CLI でテンプレートをエクスポートする](export-template-cli.md)
* [Azure portal でテンプレートをエクスポートする](export-template-portal.md)
* [REST API でリソース グループからエクスポートする](/rest/api/resources/resourcegroups/exporttemplate)および [REST API でデプロイ履歴からエクスポートする](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>リソース グループからテンプレートをエクスポートする

リソース グループを設定したら、リソース グループの Azure Resource Manager テンプレートをエクスポートできます。 

リソース グループ内のすべてのリソースをエクスポートするには、[Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) コマンドレットを使用して、リソース グループ名を指定します。

```azurepowershell-interactive
Export-AzResourceGroup -ResourceGroupName demoGroup
```

これでテンプレートはローカル ファイルとして保存されます。

リソース グループ内のすべてのリソースをエクスポートする代わりに、どのリソースをエクスポートするかを選択できます。

1 つのリソースをエクスポートするには、そのリソースの ID を渡します。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

複数のリソースをエクスポートするには、リソース ID を配列で渡します。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

テンプレートをエクスポートするときには、テンプレート内でパラメーターを使用するかどうかを指定できます。 既定では、リソース名用のパラメーターが含められますが、既定値はありません。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

テンプレートをエクスポートするときに `-SkipResourceNameParameterization` パラメーターを使用する場合、リソース名用のパラメーターはテンプレートに含められません。 代わりに、リソース名はリソースに対して直接、現在の値に設定されます。 この名前はデプロイ時にカスタマイズすることはできません。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

テンプレートをエクスポートするときに `-IncludeParameterDefaultValue` パラメーターを使用する場合、テンプレート パラメーターには、現在の値に設定される既定値が含められます。 その既定値を使用することも、異なる値を渡して既定値を上書きすることもできます。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

## <a name="save-template-from-deployment-history"></a>デプロイ履歴からのテンプレートの保存

デプロイ履歴のデプロイからテンプレートを保存できます。 取得するテンプレートは、デプロイに使用されたものとまったく同じです。 

リソース グループのデプロイからテンプレートを取得するには、[Save-AzResourceGroupDeploymentTemplate](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate) コマンドを使用します。 取得するデプロイの名前を指定します。 デプロイの名前を取得する方法については、「[Azure Resource Manager でのデプロイ履歴の表示](deployment-history.md)」を参照してください。

```azurepowershell-interactive
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName demoGroup -DeploymentName demoDeployment
```

テンプレートは、デプロイの名前を含むローカル ファイルとして保存されます。

他のレベルでデプロイされたテンプレートを取得するには、以下を使用します。

* [Save-AzDeploymentTemplate](/powershell/module/az.resources/save-azdeploymenttemplate) (サブスクリプションへのデプロイの場合)
* [Save-AzManagementGroupDeploymentTemplate](/powershell/module/az.resources/save-azmanagementgroupdeploymenttemplate) (管理グループへのデプロイの場合)
* [Save-AzTenantDeploymentTemplate](/powershell/module/az.resources/save-aztenantdeploymenttemplate) (テナントへのデプロイの場合)

## <a name="next-steps"></a>次のステップ

- [Azure CLI](export-template-cli.md)、[Azure portal](export-template-portal.md)、または [REST API](/rest/api/resources/resourcegroups/exporttemplate) を使用してテンプレートをエクスポートする方法を学びます。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./syntax.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](../index.yml)のページをご覧ください。