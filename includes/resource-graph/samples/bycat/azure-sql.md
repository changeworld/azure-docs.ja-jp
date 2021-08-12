---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 07/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8f44ee8ef31f8fd649edfa0aa332dbaf16ee4de1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114457269"
---
### <a name="list-sql-databases-and-their-elastic-pools"></a>SQL データベースとそのエラスティック プールを一覧表示する

次のクエリでは、**leftouter**`join` を使用して、SQL Database リソースと (存在する場合) それに関連するエラスティック プールを結合します。

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
    on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a>

---

