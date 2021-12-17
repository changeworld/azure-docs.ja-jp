---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 34079d499b5c583ea12fbaac5ba76d9e6139d5ea
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058119"
---
### <a name="count-all-sensors-by-type"></a>すべてのセンサーを種類別にカウントする

このクエリにより、すべてのセンサーがその種類 (OT、EIoT) 別に集計されます。

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sensors'
| summarize count() by tostring(properties.sensorType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sensors' | summarize count() by tostring(properties.sensorType)"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsensors%27%0a%7c%20summarize%20count()%20by%20tostring(properties.sensorType)" target="_blank">portal.azure.cn</a>

---

### <a name="count-how-many-iot-devices-there-are-in-your-network-by-operation-system"></a>ネットワーク内に存在する IoT デバイスの数をオペレーション システム別にカウントする

このクエリにより、すべての IoT デバイスがオペレーション システムのプラットフォーム別に集計されます。

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/devices'
| summarize count() by tostring(properties.operatingSystem.platform)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/devices' | summarize count() by tostring(properties.operatingSystem.platform)"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2fdevices%27%0a%7c%20summarize%20count()%20by%20tostring(properties.operatingSystem.platform)" target="_blank">portal.azure.cn</a>

---

### <a name="get-all-high-severity-recommendations"></a>重要度が高い推奨事項をすべて取得する

このクエリにより、ユーザーの重要度が高いすべての推奨事項の一覧が得られます。

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations'
| where properties.severity == 'High'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/recommendations' | where properties.severity == 'High'"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2frecommendations%27%0a%7c%20where%20properties.severity%20%3d%3d%20%27High%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-sites-with-a-specific-tag-value"></a>特定のタグ値を含むサイトを一覧表示する

このクエリにより、特定のタグ値を含むすべてのサイトの一覧が得られます。

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/sites'
| where properties.tags['key'] =~ 'value1'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/sites' | where properties.tags['key'] =~ 'value1'"
```

# <a name="portal"></a>[ポータル](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: このクエリを Azure Resource Graph エクスプローラーで試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.com</a>
- Azure Government ポータル: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.us</a>
- Azure China 21Vianet ポータル: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2fsites%27%0a%7c%20where%20properties.tags%5b%27key%27%5d%20%3d%7e%20%27value1%27" target="_blank">portal.azure.cn</a>

---

