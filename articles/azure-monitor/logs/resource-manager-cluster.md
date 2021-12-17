---
title: Log Analytics クラスター用の Resource Manager テンプレートのサンプル
description: Log Analytics クラスターをデプロイするための Azure Resource Manager テンプレートのサンプル。
ms.topic: sample
author: yossiy
ms.author: yossiy
ms.date: 09/12/2021
ms.openlocfilehash: 1e585db0e7e0cec05319c74419bb840200d1d86a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659232"
---
# <a name="resource-manager-template-samples-for-log-analytics-clusters-in-azure-monitor"></a>Azure Monitor の Log Analytics クラスター用の Resource Manager テンプレートのサンプル
この記事には、Azure Monitor で Log Analytics クラスターを作成および構成するためのサンプルの [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/syntax.md)が含まれています。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>テンプレート リファレンス

- [Microsoft.OperationalInsights クラスター](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/clusters) 

## <a name="create-a-log-analytics-cluster"></a>Log Analytics クラスターを作成する
次のサンプルでは、新しい空の Log Analytics クラスターを作成します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "CommitmentTier": {
      "type": "int",
      "allowedValues": [
        500,
        1000,
        2000,
        5000
      ],
      "defaultValue": 500,
      "metadata": {
        "description": "The Capacity Reservation value."
      }
  },
  "billingType": {
      "type": "string",
      "allowedValues": [
        "Cluster",
        "Workspaces"
      ],
      "defaultValue": "Cluster",
      "metadata": {
          "description": "The billing type settings. Can be 'Cluster' (default) or 'Workspaces' for proportional billing on workspaces."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "CapacityReservation",
        "capacity": "[parameters('CommitmentTier')]"
      },
      "properties":  {
        "billingType": "[parameters('billingType')]"
      }
    }
  ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "CommitmentTier": {
      "value": 500
    },
    "billingType": {
      "value": "Cluster"
    }
  }
}
```

## <a name="update-a-log-analytics-cluster"></a>Log Analytics クラスターを更新する
次のサンプルでは、カスタマー マネージド キーを使用するように Log Analytics クラスターを更新します。

### <a name="template-file"></a>テンプレート ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "keyVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The key identifier URI."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The key name."
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "The key version. When empty, latest key version is used."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties":  {
        "keyVaultProperties": {
        "keyVaultUri": "https://key-vault-name.vault.azure.net",
        "keyName": "key-name",
        "keyVersion": "current-version"
        }
      }  
    }
  ]
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "keyVaultUri": {
      "value": "https://key-vault-name.vault.azure.net"
    },
    "keyName": {
      "value": "MyKeyName"
    },
    "keyVersion": {
      "value": ""
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

* [Azure Monitor の他のサンプル テンプレートを入手します](../resource-manager-samples.md)。
* [Log Analytics 専用クラスターの詳細情報](./logs-dedicated-clusters.md)。
* [エージェント データ ソースの詳細情報](../agents/agent-data-sources.md)。
