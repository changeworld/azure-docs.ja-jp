---
title: 初めての Azure Resource Manager テンプレートを作成する | Microsoft Docs
description: Azure Resource Manager テンプレートを初めて作成する際の手順を詳細に説明したガイドです。 ストレージ アカウントのテンプレート リファレンスを使ってテンプレートを作成する方法を紹介します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/30/2018
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 38ed7040c4f1497192c8c37887dc6281aa7935ea
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>初めての Azure Resource Manager テンプレートを作成およびデプロイする
このトピックでは、Azure Resource Manager テンプレートを初めて作成する際の手順を説明します。 Resource Manager テンプレートとは、ソリューションに対してデプロイが必要なリソースを定義した JSON ファイルのことをいいます。 Azure ソリューションのデプロイと管理に関する概念について理解を深めるには、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。 既にリソースがあり、そのリソースのテンプレートを取得するには、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](resource-manager-export-template.md)」を参照してください。

テンプレートの作成と編集には、JSON エディターが必要です。 軽量なオープンソースのクロスプラットフォーム コード エディターとしては、[Visual Studio Code](https://code.visualstudio.com/) があります。 Resource Manager テンプレートの作成には Visual Studio Code を使用することを強くお勧めします。 この記事は、VS Code の使用を前提として書かれています。 別の JSON エディター (Visual Studio など) があれば、そちらを使っても問題ありません。

## <a name="prerequisites"></a>前提条件

* Visual Studio Code。 必要であれば、[https://code.visualstudio.com/](https://code.visualstudio.com/) からインストールします。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* ローカルにインストールされた [Azure PowerShell](/powershell/azure/install-azurerm-ps) または [Azure CLI](/cli/azure/install-azure-cli)。 このチュートリアルでは、テンプレートがローカル ファイルとして保存されるため、ローカル インストールが必要です。 Cloud Shell を使用するには、[ストレージ アカウントにテンプレートを読み込む](resource-group-template-deploy-cli.md#deploy-template-from-cloud-shell)必要があります。

## <a name="create-template"></a>テンプレートの作成

ストレージ アカウントをサブスクリプションにデプロイする単純なテンプレートから始めましょう。

1. **[ファイル]** > **[新しいファイル]** を選択します。 

   ![[新しいファイル]](./media/resource-manager-create-first-template/new-file.png)

2. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   ストレージ アカウント名には制約がいくつもあるため、設定の難易度が高くなっています。 名前の長さは 3 ～ 24 文字で、使用できるのは数字と小文字のみです。また、一意になっている必要があります。 上記のテンプレートでは、[uniqueString](resource-group-template-functions-string.md#uniquestring) 関数を使用してハッシュ値を生成しています。 このハッシュ値に他の意味も加えるために、プレフィックス *storage* を追加します。 

3. このファイルを **azuredeploy.json** という名前でローカル フォルダーに保存します。

   ![テンプレートを保存する](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>テンプレートのデプロイ

これでこのテンプレートをデプロイする準備が整いました。 PowerShell または Azure CLI を使用して、リソース グループを作成します。 次に、そのリソース グループにストレージ アカウントをデプロイします。

* PowerShell の場合、テンプレートを含むフォルダーから次のコマンドを使用します。

   ```powershell
   Connect-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Azure CLI のローカル インストールの場合は、テンプレートを含むフォルダーから次のコマンドを使用します。

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

デプロイが完了すると、リソース グループにストレージ アカウントが含まれた状態になります。

## <a name="customize-the-template"></a>テンプレートのカスタマイズ

テンプレートは正常に機能しますが、柔軟ではありません。 このテンプレートでは、ローカル冗長ストレージは常に米国中南部にデプロイされます。 名前は、常に *storage* の後にハッシュ値が続く形式になります。 さまざまなシナリオでテンプレートを使用できるようにするには、テンプレートにパラメーターを追加します。

次の例は、2 つのパラメーターが含まれた parameters セクションを示しています。 最初のパラメーター `storageSKU` では、冗長性の種類を指定できます。 これで、渡すことができる値を、ストレージ アカウントで有効な値に制限しています。 また、既定値も指定しています。 2 番目のパラメーター `storageNamePrefix` は、最大 11 文字を許可するように設定されています。 ここでは、既定値を指定しています。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

variables セクションに `storageName` という名前の変数を追加します。 ここでは、parameters からのプレフィックス値と [uniqueString](resource-group-template-functions-string.md#uniquestring) 関数からのハッシュ値を組み合わせています。 ここでは、[toLower](resource-group-template-functions-string.md#tolower) 関数を使用してすべての文字を小文字に変換しています。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

これらの新しい値をストレージ アカウントに使用するために、リソース定義を変更します。

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

ストレージ アカウントの名前が追加した変数に設定されている点に注意してください。 SKU 名はパラメーターの値に設定されています。 場所は、リソース グループと同じ場所に設定されています。

ファイルを保存します。 

ここまでの作業が終わった時点で、テンプレートは以下のようになります。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>テンプレートの再デプロイ

異なる値が使用されたテンプレートを再デプロイします。

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Azure CLI では、次を使用します。

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>オートコンプリートの使用

ここまでは、単にこの記事から JSON をコピーして貼り付けることによってのみテンプレートの作業を行ってきましたが、 独自のテンプレートを作成するときは、リソースの種類に応じて利用可能なプロパティと値を探して指定することができます。 VS Code は、リソースの種類に対応したスキーマを読み取り、プロパティと値の候補を表示します。 オートコンプリート機能を見るために、テンプレートの properties 要素に移動し、新しい行を追加してみましょう。 引用符を入力するとすぐに、その properties 要素内で利用できる名前の候補が VS Code によって表示されることがわかります。

![使用できるプロパティの表示](./media/resource-manager-create-first-template/show-properties.png)

**encryption** を選択します。 コロン (:) を入力すると、新しいオブジェクトを追加するように促されます。

![オブジェクトの追加](./media/resource-manager-create-first-template/add-object.png)

オブジェクトを追加するには、Tab キーまたは Enter キーを押します。

もう一度引用符を入力すると、今度は、encryption で利用できるプロパティの候補が表示されます。

![encryption のプロパティの表示](./media/resource-manager-create-first-template/show-encryption-properties.png)

**services** を選択し、VS Code 拡張機能に基づく値を追加していき、次のコードを記述します。

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

ストレージ アカウントに関して BLOB の暗号化を有効にしました。 ところが、VS Code によって問題が検出されています。 encryption に警告が表示されていることに注意してください。

![暗号化の警告](./media/resource-manager-create-first-template/encryption-warning.png)

警告を確認するには、緑色の線にマウス ポインターを合わせます。

![不足しているプロパティ](./media/resource-manager-create-first-template/missing-property.png)

encryption 要素には keySource プロパティが必要であることがわかります。 services オブジェクトの後にコンマを追加して keySource プロパティを追加します。 有効な値の候補として **"Microsoft.Storage"** が VS Code によって表示されます。 完成した properties 要素は次のようになります。

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

完成したテンプレートは次のとおりです。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>暗号化されたストレージのデプロイ

再度テンプレートをデプロイし、新しいストレージ アカウント名を入力します。

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Azure CLI では、次を使用します。

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

デプロイしたリソースが不要になったら、リソース グループを削除して、そのリソースをクリーンアップします。

PowerShell では、次を使用します。

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Azure CLI では、次を使用します。

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>次の手順
* VS Code 拡張機能をインストールすると、テンプレートの開発効率をさらに高めることができます。 詳細については、「[Visual Studio Code 拡張機能を使って Azure Resource Manager テンプレートを作成する](resource-manager-vscode-extension.md)」を参照してください。
* テンプレートの構造の詳細については、「 [Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* ストレージ アカウントのプロパティについては、[ストレージ アカウント テンプレート リファレンス](/azure/templates/microsoft.storage/storageaccounts)のページを参照してください。
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
