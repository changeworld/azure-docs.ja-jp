---
title: Azure CLI でテンプレートをエクスポートする
description: Azure CLI を使用して、サブスクリプション内のリソースから Azure Resource Manager テンプレートをエクスポートします。
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: b60c7ae3787b2b22b09eb844a9206b9530fb68df
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480878"
---
# <a name="use-azure-cli-to-export-a-template"></a>Azure CLI を使用してテンプレートをエクスポートする

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

この記事では、**Azure CLI** を使用してテンプレートをエクスポートする方法を示します。 その他のオプションについては、以下を参照してください。

* [Azure portal でテンプレートをエクスポートする](export-template-portal.md)
* [Azure PowerShell でテンプレートをエクスポートする](export-template-powershell.md)
* [リソース グループからの REST API エクスポート](/rest/api/resources/resourcegroups/exporttemplate)および[デプロイ履歴からの REST API エクスポート](/rest/api/resources/deployments/export-template)

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>リソース グループからテンプレートをエクスポートする

リソース グループを正常に設定したら、そのリソース グループの Azure Resource Manager テンプレートをエクスポートできます。

リソース グループ内のすべてのリソースをエクスポートするには、[az group export](/cli/azure/group#az_group_export) を使用して、リソース グループ名を指定します。

```azurecli-interactive
az group export --name demoGroup
```

このスクリプトでは、コンソールにテンプレートを表示します。 ファイルに保存するには、以下を使用します。

```azurecli-interactive
az group export --name demoGroup > exportedtemplate.json
```

リソース グループ内のすべてのリソースをエクスポートする代わりに、どのリソースをエクスポートするかを選択できます。

1 つのリソースをエクスポートするには、そのリソースの ID を渡します。

```azurecli-interactive
storageAccountID=$(az resource show --resource-group demoGroup --name demostg --resource-type Microsoft.Storage/storageAccounts --query id --output tsv)
az group export --resource-group demoGroup --resource-ids $storageAccountID
```

複数のリソースをエクスポートするには、スペースで区切られたリソース ID を渡します。 すべてのリソースをエクスポートするには、この引数を指定したり、"*" を指定したりしないでください。

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccountID1 $storageAccountID2
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

テンプレートをエクスポートするときに `--skip-resource-name-params` パラメーターを使用する場合、リソース名用のパラメーターはテンプレートに含められません。 代わりに、リソース名はリソースに対して直接、現在の値に設定されます。 この名前はデプロイ時にカスタマイズすることはできません。

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

テンプレートをエクスポートするときに `--include-parameter-default-value` パラメーターを使用する場合、テンプレート パラメーターには、現在の値に設定される既定値が含められます。 その既定値を使用することも、異なる値を渡して既定値を上書きすることもできます。

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

デプロイ履歴に含まれているデプロイからテンプレートを保存できます。 取得するテンプレートは、デプロイに使用されたものとまったく同じです。

リソース グループのデプロイからテンプレートを取得するには、[az deployment group export](/cli/azure/deployment/group#az_deployment_group_export) コマンドを使用します。 取得するデプロイの名前を指定します。 デプロイの名前を取得する方法については、「[Azure Resource Manager でのデプロイ履歴の表示](deployment-history.md)」を参照してください。

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment 
```

テンプレートがコンソールに表示されます。 ファイルを保存するには、以下を使用します。

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment > demoDeployment.json
```

他のレベルでデプロイされたテンプレートを取得するには、以下を使用します。

* [az deployment sub export](/cli/azure/deployment/sub#az_deployment_sub_export) (サブスクリプションへのデプロイの場合)
* [az deployment mg export](/cli/azure/deployment/mg#az_deployment_mg_export) (管理グループへのデプロイの場合)
* [az deployment tenant export](/cli/azure/deployment/tenant#az_deployment_tenant_export) (テナントへのデプロイの場合)


## <a name="next-steps"></a>次のステップ

- [Azure portal](export-template-portal.md)、[Azure PowerShell](export-template-powershell.md)、または [REST API](/rest/api/resources/resourcegroups/exporttemplate) を使用してテンプレートをエクスポートする方法を学びます。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./syntax.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](../index.yml)のページをご覧ください。