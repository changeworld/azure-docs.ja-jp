---
title: リンクされたテンプレートを使用してテンプレート スペックを作成する
description: リンクされたテンプレートを使用してテンプレート スペックを作成する方法について説明します。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: b952baa465092fef19ad2feb11a43328a6177d1c
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387865"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>チュートリアル:リンクされたテンプレートを使用してテンプレート スペックを作成する (プレビュー)

[リンクされたテンプレート](linked-templates.md#linked-template)を使用して[テンプレート スペック](template-specs.md)を作成する方法について説明します。 テンプレート スペックは、ARM テンプレートを組織内の他のユーザーと共有するために使用します。 この記事では、[デプロイ リソース](/azure/templates/microsoft.resources/deployments)の新しい `relativePath` プロパティを使用して、メイン テンプレートとそのリンクされたテンプレートをパッケージ化するテンプレート スペックを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> テンプレート スペックは現在プレビュー段階にあります。 使用するには、[プレビューにサインアップする](https://aka.ms/templateSpecOnboarding)必要があります。

## <a name="create-linked-templates"></a>リンク済みテンプレートの作成

メイン テンプレートとリンクされたテンプレートを作成します。

テンプレートをリンクするには、メイン テンプレートに[デプロイ リソース](/azure/templates/microsoft.resources/deployments)を追加します。 `templateLink` プロパティで、親テンプレートのパスに基づいて、リンクされたテンプレートの相対パスを指定します。

リンクされたテンプレートは **linkedTemplate.json** と呼ばれ、メイン テンプレートが格納されているパス内の **artifacts** というサブフォルダーに格納されます。  relativePath には、次のいずれかの値を使用できます。

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath` プロパティは、常に `relativePath` を宣言しているテンプレート ファイルを基準とした相対パスであるため、linkedTemplate.json から呼び出される別の linkedTemplate2.json が存在し、linkedTemplate2.json が同じ artifacts サブフォルダーに格納されている場合は、linkedTemplate.json で指定される relativePath は単に `linkedTemplate2.json` となります。

1. 次の JSON を使用してメイン テンプレートを作成します。 メイン テンプレートを **azuredeploy.json** としてローカル コンピューターに保存します。 このチュートリアルでは、**c:\Templates\linkedTS\azuredeploy.json** パスに保存したものと仮定していますが、任意のパスを使用できます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > `Microsoft.Resources/deployments` の apiVersion は 2020-06-01 以降である必要があります。

1. メイン テンプレートが保存されているフォルダーに **artifacts** という名前のディレクトリを作成します。
1. 次の JSON を使用して、リンクされたテンプレートを作成します。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. テンプレートを **linkedTemplate.json** として **artifacts** フォルダーに保存します。

## <a name="create-template-spec"></a>テンプレート スペックの作成

テンプレート スペックはリソース グループに格納されます。  リソース グループを作成した後、次のスクリプトでテンプレート スペックを作成します。 テンプレート スペック名は **webSpec** です。

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

完了したら、Azure portal から、または次のコマンドレットを使用して、テンプレート スペックを表示できます。

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>テンプレート スペックのデプロイ

テンプレート スペックをデプロイする準備ができました。テンプレート スペックのデプロイは、テンプレート スペックのリソース ID を渡す点を除けば、含まれているテンプレートをデプロイするのと同じです。同じデプロイ コマンドを使用し、必要に応じて、テンプレート スペックのパラメーター値を渡します。

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>次のステップ

テンプレート スペックをリンクされたテンプレートとしてデプロイする方法の詳細については、「[チュートリアル:テンプレート スペックをリンクされたテンプレートとしてデプロイする](template-specs-deploy-linked-template.md)」を参照してください。
