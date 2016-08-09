<properties
	pageTitle="エクスポートした Resource Manager テンプレートをカスタマイズする | Microsoft Azure"
	description="エクスポートした Azure Resource Manager テンプレートにパラメーターを追加し、Azure PowerShell または Azure CLI を通じて再デプロイします。"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/01/2016"
	ms.author="tomfitz"/>

# エクスポートした Azure Resource Manager テンプレートのカスタマイズ

この記事では、エクスポートしたテンプレートに変更を加え、追加の値をパラメーターとして渡せるようにする方法を説明します。[Resource Manager テンプレートのエクスポート](resource-manager-export-template.md)に関する記事で実行される手順が基になっていますが、その記事を先行して行う必要はありません。必要なテンプレートとスクリプトは、この記事の中で紹介しています。

## エクスポートしたテンプレートの表示

[Resource Manager テンプレートのエクスポート](resource-manager-export-template.md)に関するトピックを完了している場合は、ダウンロードしたテンプレートを開きます。テンプレートの名前は、**template.json** になっています。Visual Studio または Visual Code がある場合は、どちらを使用しても、テンプレートを編集することができます。ない場合は、任意の JSON エディターまたはテキスト エディターを使用することができます。

まだ前のチュートリアルが終わっていない場合は、**template.json** という名前のファイルを作成し、エクスポートしたテンプレートから次の内容を抜粋してそのファイルに追加してください。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

すべてのデプロイで同じアドレス プレフィックスと同じサブネット プレフィックスを使用する仮想ネットワークを持つ、同じリージョン内の同じ種類のストレージ アカウントを作成する場合は、template.json テンプレートで正しく動作します。ただし、Resource Manager に備わっている各種オプションを使用すれば、より柔軟にテンプレートをデプロイすることができます。たとえば、デプロイ中に、作成するストレージ アカウントの種類を指定したり、仮想ネットワークのアドレス プレフィックスとサブネット プレフィックスの値を指定したりすることができます。

## テンプレートのカスタマイズ

このセクションでは、エクスポートしたテンプレートにパラメーターを追加して、これらのリソースを他の環境にデプロイするときにテンプレートを再利用できるようにします。また、テンプレートをデプロイするときにエラーが発生する可能性を低くするために、テンプレートにいくつかの機能を追加します。ストレージ アカウントの名前が一意になるように考える必要がなくなります。代わりに、テンプレートが一意の名前を作成します。ユーザーは、ストレージ アカウントの種類として指定できる値を制限して、有効なオプションだけになるようにします。

1. 指定する値をデプロイ時に渡すことができるようにするには、**parameters** セクションを以下のパラメーター定義に置き換えます。**storageAccount\_accountType** に対して許可されている値 (**allowedValues**) に注意してください。間違って無効な値を指定した場合、そのエラーはデプロイの開始前に認識されます。また、ストレージ アカウント名のプレフィックスだけを指定していることと、プレフィックスが 11 文字までに制限されていることにも注意してください。プレフィックスを 11 文字までに制限することで、完全な名前がストレージ アカウントの最大文字数を超えないようにすることができます。プレフィックスを使用すると、ストレージ アカウントに名前付け規則を適用することができます。一意の名前を作成する方法については、次の手順で説明します。

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. テンプレートの **variables** セクションは、現時点では空です。このセクションを、次のコードに置き換えます。**variables** セクションを使用すると、テンプレートの作成者は、テンプレートの残りの部分で構文を簡略化する値を作成できます。**storageAccount\_name** 変数は、パラメーターのプレフィックスを一意の文字列に連結します。この文字列は、リソース グループの ID に基づいて生成されます。

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. リソース定義でパラメーターと変数を使用するには、**resources** セクションを以下の定義に置き換えます。リソース プロパティにどの値を割り当てるかの指定以外に、リソース定義の実質的な変更はほとんどないことに注意してください。プロパティは、エクスポートしたテンプレートのプロパティとまったく同じです。ハードコーディングされた値の代わりに、単にプロパティをパラメーターの値に割り当てています。リソースの場所は、**resourceGroup().location** 式を通じて、リソース グループと同じ場所を使用するように設定されます。ストレージ アカウント名用に作成した変数は、**variables** 式を通じて参照されます。

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## パラメーター ファイルの修正

ダウンロードしたパラメーター ファイルは、テンプレートのパラメーターと一致しなくなっています。パラメーター ファイルは使用しなくてもかまいませんが、使用すると環境の再デプロイ作業を簡略化できます。多くのパラメーターにはテンプレートで定義されている既定値を使用するため、パラメーター ファイルで必要なのは 2 つの値だけです。

parameters.json ファイルの内容を次のように置き換えます。

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

更新後のパラメーター ファイルは、既定値を持たないパラメーターの値だけを指定しています。他のパラメーターは、既定値とは異なる値にするときに、その値を指定できます。

## テンプレートのデプロイ

カスタマイズしたテンプレートとパラメーター ファイルをデプロイするには、Azure PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用することができます。必要に応じて、[Azure PowerShell](powershell-install-configure.md) または [Azure CLI](xplat-cli-install.md) をインストールします。テンプレートをデプロイするには、元のテンプレートをエクスポートしたときにテンプレートと共にダウンロードしたスクリプトを使用するか、独自のスクリプトを記述することができます。この記事では、両方のオプションを紹介します。

2. 独自のスクリプトでデプロイするには、次のいずれかの方法を使用します。

     PowerShell の場合は、次のように実行します。

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Azure CLI の場合は、次ように実行します。

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. エクスポートしたテンプレートとスクリプトをダウンロードした場合は、**deploy.ps1** ファイル (PowerShell 用) または **deploy.sh** ファイル (Azure CLI 用) を見つけます。

     PowerShell の場合は、次のように実行します。

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Azure CLI の場合は、次ように実行します。

        .\deploy.sh

## 次のステップ

- [Resource Manager テンプレート チュートリアル](resource-manager-template-walkthrough.md)は、この記事で学習した内容を基盤として構築されており、より複雑なソリューション用のテンプレートを作成します。使用可能なリソースと、どの値を指定するかの判断方法について、より深く理解できます。
- PowerShell を使用してテンプレートをエクスポートする方法を確認するには、「[Azure Resource Manager での Azure PowerShell の使用](powershell-azure-resource-manager.md)」を参照してください。
- Azure CLI を使用してテンプレートをエクスポートする方法を確認するには、「[Azure Resource Manager での Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」を参照してください。
- テンプレートの構造について学習するには、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->