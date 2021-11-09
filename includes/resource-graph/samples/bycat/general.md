---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: b22262682ebba72371d681a3de0f2429776699a3
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060181"
---
### <a name="count-azure-resources"></a>Azure リソースの数

このクエリは、あなたがアクセスしたサブスクリプションに存在する Azure リソースの数を返します。 また、選択するシェルに適切な Azure Resource Graph コンポーネントがインストールされ、正常に動作していることを検証するために使用する場合にも有効なクエリです。

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.cn</a>

---

### <a name="list-impacted-resources-when-transferring-an-azure-subscription"></a>Azure サブスクリプションの譲渡時に影響を受けるリソースの一覧

サブスクリプションを別の Azure Active Directory (Azure AD) ディレクトリに譲渡するときに影響を受ける、Azure リソースの一部を返します。 サブスクリプションの譲渡前に存在していたリソースの一部を再作成する必要があります。

```kusto
Resources
| where type in (
    'microsoft.managedidentity/userassignedidentities',
    'microsoft.keyvault/vaults',
    'microsoft.sql/servers/databases',
    'microsoft.datalakestore/accounts',
    'microsoft.containerservice/managedclusters')
    or identity has 'SystemAssigned'
    or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true)
| summarize count() by type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type in ( 'microsoft.managedidentity/userassignedidentities', 'microsoft.keyvault/vaults', 'microsoft.sql/servers/databases', 'microsoft.datalakestore/accounts', 'microsoft.containerservice/managedclusters') or identity has 'SystemAssigned' or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true) | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type in ( 'microsoft.managedidentity/userassignedidentities', 'microsoft.keyvault/vaults', 'microsoft.sql/servers/databases', 'microsoft.datalakestore/accounts', 'microsoft.containerservice/managedclusters') or identity has 'SystemAssigned' or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true) | summarize count() by type"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.cn</a>

---

### <a name="list-resources-sorted-by-name"></a>名前で並べ替えられたリソースの一覧表示

このクエリは、あらゆる種類のリソースを返しますが、返されるプロパティは **name**、**type**、**location** に限られます。 `order by` を使用することにより、これらのプロパティが **name** プロパティの昇順 (`asc`) で並べ替えらえます。

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="remove-columns-from-results"></a>結果から列を除外する

次のクエリは、`summarize` を使用してサブスクリプションごとにリソースをカウントし、`join` を使用して、_ResourceContainers_ テーブルに格納されているサブスクリプションの詳細と結合した後、`project-away` を使用して一部の列を除外しています。

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.cn</a>

---

### <a name="show-resource-types-and-api-versions"></a>リソースの種類と API バージョンを表示する

Resource Graph は、主にリソース プロバイダー API の最新の非プレビュー バージョンを使用して、更新中にリソース プロパティの `GET` を行います。 場合によっては、使用される API バージョンがオーバーライドされ、最新のプロパティまたは広く使用されているプロパティが結果に提供されます。 次のクエリは、リソースの種類ごとにプロパティを収集するために使用される API バージョンの詳細を示しています。

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a>

---

