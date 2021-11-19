---
title: カオス実験用 Azure Resource Manager テンプレートのサンプル
description: Azure Chaos Studio の実験を作成するためのサンプル Azure Resource Manager テンプレート
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: dc0777380cf34465fc89a9e2e61d63aec0ca9e8c
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404313"
---
# <a name="resource-manager-template-samples-for-experiments-in-azure-chaos-studio"></a>Azure Chaos Studio での実験用の Resource Manager テンプレートのサンプル
この記事には、Azure Chaos Studio で[カオス実験](chaos-studio-chaos-experiments.md)を作成するためのサンプルの [Azure Resource Manager テンプレート](../azure-resource-manager/templates/syntax.md)が含まれています。 各サンプルには、テンプレート ファイルと、テンプレートに指定するサンプル値を含むパラメーター ファイルが含まれています。

## <a name="create-experiment-sample"></a>実験の作成 (サンプル)

このサンプルでは、1 つのターゲット リソースと 1 つの CPU 負荷障害を使用してカオス実験を作成します。 実験は、[REST API](/rest/api/chaosstudio/experiments/create-or-update) と[障害ライブラリ](chaos-studio-fault-library.md)を参照することで変更できます。

### <a name="template-file"></a>テンプレート ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "experimentName": {
      "type": "string",
      "defaultValue": "simple-experiment",
      "metadata": {
        "description": "A name for the experiment."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "A region where the experiment will be deployed."
      }
    },
    "chaosTargetResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the chaos target. This is the resource ID of the resource being targeted plus the target proxy resource."
      }
    }
  },
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Chaos/experiments",
      "apiVersion": "2021-09-15-preview",
      "name": "parameters('experimentName')",
      "location": "parameters('location')",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "identity": {
          "properties": {
            "type": "SystemAssigned"
          }
        },
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "parameters('chaosTargetResourceId')"
              }
            ]
          }
        ],
        "startOnCreation": "false",
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ]
              }
            ]
          }
        ]
      }
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>パラメーター ファイル

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "experimentName": {
        "value": "my-first-experiment"
      },
      "location": {
        "value": "eastus"
      },
      "chaosTargetResourceId": {
        "value": "eastus"
      }
  }
}
```

## <a name="next-steps"></a>次のステップ

* [Chaos Studio の詳細を確認する](chaos-studio-overview.md)
* [カオス実験の詳細を確認する](chaos-studio-chaos-experiments.md)
