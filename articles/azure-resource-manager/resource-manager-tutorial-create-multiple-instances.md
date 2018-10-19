---
title: Azure Resource Manager を使用した複数のリソース インスタンスの作成 |Microsoft Docs
description: Azure Resource Manager テンプレートを作成して、複数の Azure リソース インスタンスを作成する方法を説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/10/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 63a18a6ae0ee4c6e0a01bd7ac4a26a4fb89746c2
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419490"
---
# <a name="tutorial-create-multiple-resource-instances-using-resource-manager-templates"></a>チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成

Azure Resource Manager テンプレートで反復処理して、Azure リソースの複数のインスタンスを作成する方法について説明します。 前回のチュートリアルでは、既存のテンプレートを変更して、暗号化された Azure Storage アカウントを作成しました。 このチュートリアルでは、同じテンプレートを変更して、3 つのストレージ アカウント インスタンスを作成します。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/)。
* Resource Manager ツール拡張機能。 インストールするには、[Resource Manager ツール拡張機能のインストール](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)に関するページをご覧ください。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]**>**[名前を付けて保存]** を選択し、ファイルを **azuredeploy.json** としてご自身のローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

このチュートリアルの目的は、リソースの繰り返しを使用して 3 つのストレージ アカウントを作成することです。  サンプル テンプレートは、ストレージ アカウントを 1 つだけ作成します。 

Visual Studio Code で、次の 4 つの変更を行います。

![Azure Resource Manager で複数のインスタンスを作成する](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. ストレージ アカウントのリソース定義に `copy` 要素を追加します。 copy 要素には、そのループの反復回数と名前を指定します。 数値は正の整数で、800 を超えることはできません。
2. `copyIndex()` 関数は、ループ内の現在の繰り返しを返します。 `copyIndex()` は 0 から始まります。 インデックス値をオフセットするには、copyIndex() 関数に値を渡します。 たとえば、 *copyindex (1)* のように指定します。
3. **variables** 要素を削除します。これ以上使われないためです。
4. **outputs** 要素を削除します。

完成したテンプレートは、次のようになります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

複数のインスタンスの作成の詳細については、「[Azure Resource Manager テンプレートでリソースまたはプロパティの複数のインスタンスをデプロイする](./resource-group-create-multiple.md)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、Visual Studio Code のクイック スタートの「[テンプレートのデプロイ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)」セクションを参照してください。

3 つのストレージ アカウントをすべて一覧表示するには、--name パラメータを省略します。

# <a name="clitabcli"></a>[CLI](#tab/CLI)
```cli
az storage account list --resource-group <ResourceGroupName>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzureRmStorageAccount -ResourceGroupName <ResourceGroupName>
```

---

テンプレートの名前定義とストレージ アカウント名を比較します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、複数のストレージ アカウント インスタンスを作成する方法について説明しました。 これまでに、1 つのストレージアカウントまたは複数のストレージ アカウント インスタンスを作成しました。 次のチュートリアルでは、複数のリソースと複数のリソース タイプを含むテンプレートを作成します。 一部のリソースには依存リソースがあります。

> [!div class="nextstepaction"]
> [依存リソースの作成](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
