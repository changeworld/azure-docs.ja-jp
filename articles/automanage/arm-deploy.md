---
title: ARM テンプレートを使用してマシンを Azure Automanage にオンボードする
description: Azure Resource Manager テンプレートを使用してマシンを Azure Automanage にオンボードする方法について説明します。
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: cdb10c265ddae4aaf83450c1951ef6cb5c2219df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443853"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートを使用してマシンを Azure Automanage にオンボードする


## <a name="overview"></a>概要
次の手順に従い、ARM テンプレートを使ってマシンを Automanage のベスト プラクティスにオンボードします。

## <a name="prerequisites"></a>前提条件
* 必要な [RBAC アクセス許可](./automanage-virtual-machines.md#required-rbac-permissions)を持っている必要があります
* これらの[前提条件](./automanage-virtual-machines.md#prerequisites)に記載されているサポート対象のリージョンとサポート対象の VM イメージである必要があります


## <a name="arm-template-overview"></a>ARM テンプレートの概要
次の ARM テンプレートでは、指定されたマシンを Azure Automanage のベスト プラクティスにオンボードします。 ARM テンプレートの詳細とデプロイ方法の手順については、[下](#arm-template-deployment)の ARM テンプレートのデプロイに関するセクションを参照してください。
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "configurationProfile": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2021-04-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfile')]",
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM テンプレートのデプロイ
上記の ARM テンプレートを使うと、指定したマシンの構成プロファイル割り当てが作成されます。 

`configurationProfile` 値は次のいずれかの値になります。
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesProduction"
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesDevTest"

ARM テンプレートをデプロイするには、次の手順に従います。
1. `azuredeploy.json` として上記の ARM テンプレートを保存します
1. `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json` を使用して ARM テンプレートのデプロイを実行します
1. プロンプトが表示されたら、machineName、automanageAccountName、および configurationProfileAssignment の値を指定します。
1. これで完了です。

どの ARM テンプレートでも、パラメーターを別の `azuredeploy.parameters.json` ファイルに取り出し、それをデプロイ時に引数として使用できます。

## <a name="next-steps"></a>次のステップ
[Linux](./automanage-linux.md) および [Windows](./automanage-windows-server.md) での Automanage の詳細について説明します。