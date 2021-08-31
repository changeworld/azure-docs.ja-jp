---
title: ARM テンプレートを使用してマシンを Azure Automanage にオンボードする
description: Azure Resource Manager テンプレートを使用してマシンを Azure Automanage にオンボードする方法について説明します。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 34f20a50fc62cff98362380222822ac016b76a48
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746764"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートを使用してマシンを Azure Automanage にオンボードする


## <a name="overview"></a>概要
次の手順に従って、マシンを Automanage のベスト プラクティスにオンボードします。 次の ARM テンプレートでは、`configurationProfileAssignment` オブジェクトを作成します。これは、Automanage にオンボードされているマシンを表す Azure リソースです。

## <a name="prerequisites"></a>必須コンポーネント
* 既存の Automanage アカウントを作成し、適切なアクセス許可を割り当てておく必要があります。 Automanage アカウントとその作成方法およびアクセス許可の割り当て方法の詳細については、[こちらのドキュメント](./automanage-account.md)を参照してください。
* アクセス許可が割り当てられている既存の Automanage アカウントがある場合は、Automanage にオンボードするマシンを含むリソース グループの **共同作成者** ロールも必要です。


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
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM テンプレートのデプロイ
上記の ARM テンプレートでは、指定された Automanage アカウントを使用して、指定されたマシンの構成プロファイル割り当てを作成します。 Automanage アカウントをまだ作成していない場合は、[こちらのドキュメント](./automanage-account.md)を参照してください。

`configurationProfileAssignment` 値は次のいずれかの値になります。
* "Production"
* "DevTest"

ARM テンプレートをデプロイするには、次の手順に従います。
1. `azuredeploy.json` として上記の ARM テンプレートを保存します
1. `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json` を使用して ARM テンプレートのデプロイを実行します
1. プロンプトが表示されたら、machineName、automanageAccountName、および configurationProfileAssignment の値を指定します。
1. これで完了です。

どの ARM テンプレートでも、パラメーターを別の `azuredeploy.parameters.json` ファイルに取り出し、それをデプロイ時に引数として使用できます。

## <a name="next-steps"></a>次のステップ
[Linux](./automanage-linux.md) および [Windows](./automanage-windows-server.md) での Automanage の詳細について説明します。