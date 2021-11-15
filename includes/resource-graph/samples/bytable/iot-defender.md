---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 2feaeb47285a7c0cf548a64fcd0090397dcb4a62
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058110"
---
### <a name="get-all-new-alerts-from-the-past-30-days"></a>過去 30 日間の新しいアラートをすべて取得する

このクエリにより、過去 30 日間におけるユーザーのすべての新しいアラートの一覧が得られます。

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/alerts'
| where todatetime(properties.startTimeUtc) > ago(30d) and properties.status == 'New'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/alerts' | where todatetime(properties.startTimeUtc) > ago(30d) and properties.status == 'New'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/alerts' | where todatetime(properties.startTimeUtc) > ago(30d) and properties.status == 'New'"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2falerts%27%0a%7c%20where%20todatetime(properties.startTimeUtc)%20%3e%20ago(30d)%20and%20properties.status%20%3d%3d%20%27New%27" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2falerts%27%0a%7c%20where%20todatetime(properties.startTimeUtc)%20%3e%20ago(30d)%20and%20properties.status%20%3d%3d%20%27New%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2falerts%27%0a%7c%20where%20todatetime(properties.startTimeUtc)%20%3e%20ago(30d)%20and%20properties.status%20%3d%3d%20%27New%27" target="_blank">portal.azure.cn</a>

---

